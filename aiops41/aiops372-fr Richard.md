
-	Create the name space
oc create namespace cp4waiops

-	Create the secret to pull the Images
oc create secret docker-registry ibm-entitlement-key \
    --docker-username=cp \
    --docker-password=xxxxxx \
    --docker-server=cp.icr.io \
    --namespace=cp4waiops

-	Set the ingress endpoint policy
if [ $(oc get ingresscontroller default -n openshift-ingress-operator -o jsonpath='{.status.endpointPublishingStrategy.type}') = "HostNetwork" ]; then oc patch namespace default --type=json -p '[{"op":"add","path":"/metadata/labels","value":{"network.openshift.io/policy-group":"ingress"}}]'; fi

-	Install the IBM Market place catalogue
cat << EOF | oc apply -f -
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
    name: ibm-operator-catalog
    namespace: openshift-marketplace
spec:
    displayName: ibm-operator-catalog
    publisher: IBM Content
    sourceType: grpc
    image: icr.io/cpopen/ibm-operator-catalog:latest
EOF

-	Create the Operator Group
cat << EOF | oc apply -f -
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
    name: cp4waiops-operator-group
    namespace: cp4waiops
spec:
    targetNamespaces:
        - cp4waiops
EOF

-	Install the Operator
cat << EOF | oc apply -f -
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
    name: ibm-aiops-orchestrator
    namespace: cp4waiops
spec:
    config:
      env:
      - name: REDIS_CHANNEL
        value: v1.6
    channel: v3.7
    installPlanApproval: Automatic
    name: ibm-aiops-orchestrator
    source: ibm-operator-catalog
    sourceNamespace: openshift-marketplace
EOF

** Wait 30 mins for the Operator and ibm-common-services install 

export STORAGE_CLASS=ibmc-file-gold-gid
export STORAGE_CLASS_BLOCK=ibmc-block-gold

-	Install AIOps CloudPak, size = small
cat << EOF | oc apply -f -
apiVersion: orchestrator.aiops.ibm.com/v1alpha1
kind: Installation
metadata:
    name: ibm-cp-watson-aiops
    namespace: cp4waiops
spec:
    imagePullSecret: ibm-entitlement-key
    license:
        accept: true
    pakModules:
    - enabled: true
      name: aiopsFoundation
    - enabled: true
      name: applicationManager
    - enabled: true
      name: aiManager
    - enabled: false
      name: connection
    size: small
    storageClass: ${STORAGE_CLASS}
    storageClassLargeBlock: ${STORAGE_CLASS_BLOCK}
EOF

-	Increase the size of the PVC for Kafka to 120GB
oc patch automationbase/automationbase-sample --type merge -p '{"spec":{"kafka":{"kafka":{"storage":{"size":"120Gi"}}}}}'

-	Print the admin Password
oc -n ibm-common-services get secret platform-auth-idp-credentials -o jsonpath='{.data.admin_password}' | base64 -d && echo
plXE06IoNd6Fw9vxYRA7UfhfvnXXngEm

-	Print the CloudPak URL
echo -n https:// && oc get route -n cp4waiops cpd -o jsonpath='{.spec.host}' && echo
https://cpd-cp4waiops.itzroks-060001mysy-hu08pa-6ccd7f378ae819553d37d5f2ee142bd6-0000.us-south.containers.appdomain.cloud

