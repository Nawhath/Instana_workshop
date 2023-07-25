The integration is referencing [this](https://www.ibm.com/docs/en/cloud-paks/cloud-pak-watson-aiops/4.1.0?topic=integrations-tivoli-netcoolimpact-connections)

# Before you begin

- Ensure that you have IBM Tivoli Netcool/Impact Version 7.1 or later and IBM Cloud Pak for Watson AIOps 4.1.0 installed.

- Obtain the following details for your IBM Tivoli Netcool/Impact deployment:

    - The GUI server URL (example: https://noi-impact.fyre.ibm.com:16311)

    - The Back-end server URL (example https://noi-impact.fyre.ibm.com:9081)

    - The administrator username and password for connecting to the IBM Tivoli Netcool/Impact endpoint.

    - A valid CA certificate for IBM Tivoli Netcool/Impact if the endpoint is secured by SSL/TLS. If self-signed this means all the certificates from all the server.

      You can extract these certificates from your IBM Tivoli Netcool/Impact deployment with openssl:

      For the GUI server (port 16311)
```sh
openssl s_client -showcerts -servername noi-impact.mycluster.com -connect noi-impact.mycluster.com:16311 </dev/null
```

Sample:      

<picture>
  <img alt="image" src="./assets/images/CertExtraction.png">
</picture>

      For the backend server (port 9081)
```sh
openssl s_client -showcerts -servername noi-impact.mycluster.com -connect noi-impact.mycluster.com:9081 </dev/null
```

Sample:
<picture>
  <img alt="image" src="./assets/images/CertExtraction2.png">
</picture>


Combine the Impact certificate for GUI server and Backend server.

```sh
touch ImpactCert
```

```sh
openssl s_client -showcerts -servername itz-060001mysy-ahp0.dte.demo.ibmcloud.com -connect itz-060001mysy-ahp0.dte.demo.ibmcloud.com:16311 < /dev/null >> ImpactCert

openssl s_client -showcerts -servername itz-060001mysy-ahp0.dte.demo.ibmcloud.com -connect itz-060001mysy-ahp0.dte.demo.ibmcloud.com:9081 < /dev/null >> ImpactCert
```

Edit ImpactCert:

<picture>
  <img alt="image" src="./assets/images/CombineCert.png">
</picture>

Adding Impact Connection:


<picture>
  <img alt="image" src="./assets/images/AddImpactConnection.png">
</picture>

<picture>
  <img alt="image" src="./assets/images/Done.png">
</picture>

[Configure the connection in IBM Tivoli Netcool/Impact](https://www.ibm.com/docs/en/cloud-paks/cloud-pak-watson-aiops/4.1.0?topic=connections-configuring-connection-in-tivoli-netcoolimpact)

> Note: 
