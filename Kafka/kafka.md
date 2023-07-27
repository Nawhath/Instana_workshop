The following steps are referencing [this](https://hevodata.com/blog/how-to-install-kafka-on-ubuntu/)

# Install Kafka

Step 1: Install Java and Bookeeper

Kafka is written in Java and Scala and requires jre 1.7 and above to run it. In this step, you need to ensure Java is installed.

```sh
sudo apt-get update
sudo apt-get install default-jre
```

ZooKeeper is a centralized service for maintaining configuration information, naming, providing distributed synchronization, and providing group services. Kafka uses Zookeeper for maintaining the heartbeats of its nodes, maintaining configuration, and most importantly to elect leaders.

```sh
sudo apt-get install zookeeperd
```

You will now need to check if Zookeeper is alive and if it’s OK 😛

```sh
telnet localhost 2181
```

at Telnet prompt, You will have to enter 

```sh
ruok
```

(are you okay) if it’s all okay it will end the telnet session and reply with
```sh
imok
```

Step 2: Create a Service User for Kafka

As Kafka is a network application creating a non-root sudo user specifically for Kafka minimizes the risk if the machine is to be compromised.

```sh
sudo adduser kafka
```

Follow the Tabs and set the password to create Kafka User. Now, you have to add the User to the Sudo Group, using the following command:

```sh
sudo adduser kafka sudo
```

Now, your User is ready, you need to log in using, the following command:

```sh
su -l kafka
```

Step 3: Download Apache Kafka

Now, you need to download and extract Kafka binaries in your Kafka user’s home directory. You can create your directory using the following command:

```sh
mkdir ~/Downloads
```

You need to download the Kafka binaries using Curl:

```sh
curl "https://downloads.apache.org/kafka/3.5.1/kafka_2.13-3.5.1.tgz" -o ~/Downloads/kafka.tgz
```

Create a new directory called Kafka and change your path to this directory to make it your base directory. 

```sh
mkdir ~/kafka && cd ~/kafka
```

Now simply extract the archive you have downloaded using the following command:

```sh
tar -xvzf ~/Downloads/kafka.tgz --strip 1
```

Step 4: Configuring Kafka Server

The default behavior of Kafka prevents you from deleting a topic. Messages can be published to a Kafka topic, which is a category, group, or feed name. You must edit the configuration file to change this.

The server.properties file specifies Kafka’s configuration options. Use nano or your favorite editor to open this file:

```sh
vi ~/kafka/config/server.properties
```

Add a setting that allows us to delete Kafka topics first. Add the following to the file’s bottom:

```sh
delete.topic.enable = true
```

Now change the directory for storing logs:

```sh
log.dirs=/home/kafka/logs
```

Now you need to Save and Close the file. The next step is to set up Systemd Unit Files.

Step 5: Setting Up Kafka Systemd Unit Files

In this step, you need to create systemd unit files for the Kafka and Zookeeper service. This will help to manage Kafka services to start/stop using the systemctl command.

Create systemd unit file for Zookeeper with below command: 

```sh
vi /etc/systemd/system/zookeeper.service
```

Next, you need to add the below content:

```sh
[Unit]
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
User=kafka
ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```
Save this file and then close it. Then you need to create a Kafka systemd unit file using the following command snippet:

```sh
sudo vi /etc/systemd/system/kafka.service
```

Now, you need to enter the following unit definition into the file:

```sh
[Unit]
Requires=zookeeper.service
After=zookeeper.service

[Service]
Type=simple
User=kafka
ExecStart=/bin/sh -c '/home/kafka/kafka/bin/kafka-server-start.sh /home/kafka/kafka/config/server.properties > /home/kafka/kafka/kafka.log 2>&1'
ExecStop=/home/kafka/kafka/bin/kafka-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```
This unit file is dependent on zookeeper.service, as specified in the [Unit] section. This will ensure that zookeeper is started when the Kafka service is launched.
The [Service] line specifies that systemd should start and stop the service using the kafka-server-start.sh and Kafka-server-stop.sh shell files. It also indicates that if Kafka exits abnormally, it should be restarted.
After you’ve defined the units, use the following command to start Kafka:

```sh
systemctl start kafka
```

Check the Kafka unit’s journal logs to see if the server has started successfully:

```sh
sudo systemctl status kafka
```

On port 9092, you now have a Kafka server listening.

The Kafka service has been begun. But if you rebooted your server, Kafka would not restart automatically. To enable the Kafka service on server boot, run the following commands:

```sh
sudo systemctl enable zookeeper
sudo systemctl enable kafka
```

You have successfully done the setup and installation of the Kafka server.

Step 6: Testing installation

In this stage, you’ll put your Kafka setup to the test. To ensure that the Kafka server is functioning properly, you will publish and consume a “Hello World” message.

In order to publish messages in Kafka, you must first:

    A producer who allows records and data to be published to topics.
    A person who reads communications and data from different themes.

To get started, make a new topic called TutorialTopic:

The following is for older Kafka version (2.0 and below)
```sh
~/kafka/bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic TutorialTopic
```

This is the new command using the recommended way to manage topics is by using the --bootstrap-server option instead of --zookeeper.

```sh
bin/kafka-topics.sh --create --topic TutorialTopic --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1
```

The kafka-console-producer.sh script can be used to build a producer from the command line. As arguments, it expects the hostname, port, and topic of the Kafka server.

The string “Hello, World” should now be published to the TutorialTopic topic:

```sh
echo "Hello, World" | ~/kafka/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic TutorialTopic > /dev/null
```
Using the Kafka-console-consumer.sh script, establish a Kafka consumer. As parameters, it requests the ZooKeeper server’s hostname and port, as well as a topic name.

Messages from TutorialTopic are consumed by the command below. Note the usage of the —from-beginning flag, which permits messages published before the consumer was launched to be consumed:

```sh
~/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic TutorialTopic --from-beginning
```
Hello, World will appear in your terminal if there are no configuration issues.

The script will keep running while it waits for further messages to be published. Open a new terminal window and log into your server to try this.
Start a producer in this new terminal to send out another message:

```sh
echo "Hello World from Sammy at Hevo Data!" | ~/kafka/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic TutorialTopic > /dev/null
```

# Setup Kafka to use SSL

o generate the necessary SSL certificates and keys for Kafka, you'll need to use OpenSSL or other tools that can generate X.509 certificates. The following steps outline the process of generating self-signed certificates for use in a Kafka SSL setup:

Step 1: Install OpenSSL (if not already installed)
Make sure you have OpenSSL installed on your system. Most Linux distributions come with OpenSSL pre-installed, but if you are on Windows or macOS, you might need to download and install it separately.

Step 2: Create a directory for the certificates
Create a directory where you will store the SSL certificates. This directory will be referred to as <CERTIFICATE_DIRECTORY> in the subsequent steps.

Step 3: Generate a CA (Certificate Authority) key and certificate
The first step is to create a Certificate Authority (CA) key and certificate. The CA certificate will be used to sign the Kafka broker and client certificates.

```sh
openssl req -new -x509 -keyout <CERTIFICATE_DIRECTORY>/ca-key.pem -out <CERTIFICATE_DIRECTORY>/ca-cert.pem -days 365 -nodes
```

Step 4: Create the server (broker) key and certificate signing request (CSR)
Next, create a private key and a Certificate Signing Request (CSR) for the Kafka broker:

```sh
openssl req -new -newkey rsa:2048 -keyout <CERTIFICATE_DIRECTORY>/server-key.pem -out <CERTIFICATE_DIRECTORY>/server-req.pem -days 365 -nodes
```

Step 5: Sign the server certificate with the CA certificate
Sign the server CSR with the CA certificate to generate the server certificate:

```sh
openssl x509 -req -in <CERTIFICATE_DIRECTORY>/server-req.pem -CA <CERTIFICATE_DIRECTORY>/ca-cert.pem -CAkey <CERTIFICATE_DIRECTORY>/ca-key.pem -CAcreateserial -out <CERTIFICATE_DIRECTORY>/server-cert.pem -days 365
```
Step 6: Create the client key and certificate signing request (CSR) (for each client, producer, or consumer)
If you need to authenticate clients (producers and consumers), create a private key and CSR for each client:

```sh
openssl req -new -newkey rsa:2048 -keyout <CERTIFICATE_DIRECTORY>/client-key.pem -out <CERTIFICATE_DIRECTORY>/client-req.pem -days 365 -nodes
```

Step 7: Sign the client certificate with the CA certificate (for each client)
Sign each client CSR with the CA certificate to generate the client certificate for each client:

```sh
openssl x509 -req -in <CERTIFICATE_DIRECTORY>/client-req.pem -CA <CERTIFICATE_DIRECTORY>/ca-cert.pem -CAkey <CERTIFICATE_DIRECTORY>/ca-key.pem -CAcreateserial -out <CERTIFICATE_DIRECTORY>/client-cert.pem -days 365
```

Step 8: Set file permissions (Optional but recommended)
Ensure that the certificate files have the correct file permissions. For security, you should limit access to the certificate files.

```sh
chmod 600 <CERTIFICATE_DIRECTORY>/*.pem
```
Now you have the necessary SSL certificates and keys for Kafka. These certificates can be used to configure SSL encryption and authentication in the Kafka broker and clients. Remember to configure Kafka to use these certificates in the server.properties file for the broker and the appropriate client properties for producers and consumers.



# Apply SSL for the Kafka

To apply the SSL certificates to the server.properties file for the given configuration, follow these steps:

Step 1: Configure the listeners and security.inter.broker.protocol properties:
In the server.properties file, set the listeners property to include both PLAINTEXT and SSL listeners, and set the security.inter.broker.protocol property to SSL.

```sh
# server.properties

# Other Kafka configurations...

# Listener configurations
listeners=PLAINTEXT://<BROKER_HOST>:9092,SSL://<BROKER_HOST>:9093
security.inter.broker.protocol=SSL

# Other Kafka configurations...
```

Step 2: Set the SSL-related properties:
Provide the appropriate paths and passwords for the SSL certificates and truststore. Replace the placeholders <PATH_TO_BROKER_CERT>, <BROKER_CERT_PASSWORD>, <BROKER_KEY_PASSWORD>, <PATH_TO_TRUSTSTORE>, and <TRUSTSTORE_PASSWORD> with the actual values.

```sh
# server.properties

# Other Kafka configurations...

# SSL configurations
ssl.keystore.location=/home/kafka/certificate/server-cert.pem
ssl.keystore.password=netcool
ssl.key.password=netcool
ssl.truststore.location=/home/kafka/certificate/ca-cert.pem
ssl.truststore.password=netcool

# Other Kafka configurations...
```

<BROKER_CERT_PASSWORD> is referring to the challenge password (also known as the passphrase) that you set during the SSL certificate generation for the Kafka broker's certificate.

When you generate a private key and Certificate Signing Request (CSR) for the Kafka broker, you may be prompted to enter a passphrase to protect the private key. This passphrase acts as an additional layer of security, and it's required when using the private key.

Later, when configuring Kafka to use SSL, you'll need to provide the passphrase (challenge password) to access the private key and use the certificate. In the server.properties file, the properties ssl.keystore.password and ssl.key.password refer to this passphrase.

Step 3: Place the certificates and truststore in the correct location:
Make sure the following files are present in the /home/kafka/certificate/ directory:

    server-cert.pem: The server (broker) certificate.
    server-key.pem: The private key for the server certificate.
    ca-cert.pem: The CA certificate used to sign the server certificate.

Additionally, you need to place the truststore containing the CA certificate (ca-cert.pem) in the same directory and set the correct file permissions. Assuming the truststore file is named ca-cert.pem, you can set the permissions with the following command:

```sh
chmod 600 /home/kafka/certificate/*.pem
```

Step 4: Restart Kafka Broker:
After applying the necessary configurations, restart the Kafka broker to apply the SSL settings:

```sh
bin/kafka-server-stop.sh   # Stop Kafka broker if already running
bin/kafka-server-start.sh config/server.properties

OR

systemctl stop kafka
systemctl start kafka
```

# To create client-ssl.properties.

In a Kafka SSL setup, the client-ssl.properties file is not automatically created for you. You'll need to manually create this file and populate it with the necessary SSL configurations for your Kafka client (producer or consumer) to use SSL to communicate with the Kafka broker securely.

Step 1: Create the client-ssl.properties file
Create a new text file named client-ssl.properties in a location of your choice. You can use a text editor or command-line tools to create this file.

```sh
touch config/client-ssl.properties
```

Step 2: Configure SSL properties in the client-ssl.properties file

The kafka-topics.sh script expects the keystore file to be in the JKS (Java KeyStore) format. 

There are two truststore to be created:
- server-keystore.jks
- client.truststore.jks

For server-keystore.jks:

Create a JKS keystore file
You can use the keytool utility that comes with Java to create a JKS keystore and import the server certificate and private key into it. Run the following command to create the keystore:

```sh
keytool -genkeypair -alias server-cert -keyalg RSA -keystore server-keystore.jks -validity 365 -keysize 2048
```
During the keystore creation process, you'll be prompted to enter information like keystore password, distinguished name fields, etc. Make sure to remember the keystore password as you'll need it in the next steps.

Import the server certificate and private key into the JKS keystore

Use keytool for importing:
If you generated the certificate and key using openssl, try importing the certificate and key using keytool instead of openssl. First, convert the private key to PKCS12 format:


```sh
openssl pkcs12 -export -in ../certificate/server-cert.pem -inkey ../certificate/server-key.pem -out server.p12 -name server-cert -passout pass:netcool
```

Then, import the PKCS12 file into the keystore:
```sh
keytool -importkeystore -srckeystore server.p12 -srcstoretype PKCS12 -destkeystore server-keystore.jks -deststoretype JKS -deststorepass netcool
```

To check whether the key is imported:
```sh
keytool -list -keystore server-keystore.jks -storepass netcool
```

For client.truststore.jks:
The truststore file is used to store the CA certificate(s) or public key(s) of the servers (brokers) that the client should trust. This allows the client to verify the authenticity of the server's SSL certificate during the SSL handshake.

The truststore is used by the Kafka client when it acts as a consumer or producer to securely communicate with the Kafka brokers.

Create a truststore
If you haven't already created a truststore, you can create one using the keytool utility. The truststore should contain the CA certificate(s) or public key(s) of the Kafka brokers.

```sh
keytool -importcert -alias ca-cert -file /home/kafka/certificate/ca-cert.pem -keystore client.truststore.jks -storepass netcool
```

Replace /path/to/ca-cert.pem with the path to the CA certificate (ca-cert.pem) that was used to sign the server certificate. Also, replace <TRUSTSTORE_PASSWORD> with the desired password for the truststore.


Step 3: Update client-ssl.properties with the correct keystore and key passwords
In the client-ssl.properties file, use the newly created JKS keystore and set the keystore password and key password:


```sh
security.protocol=SSL
ssl.truststore.location=/home/kafka/kafka/client.truststore.jks
ssl.truststore.password=netcool
ssl.keystore.location=/home/kafka/kafka/server-keystore.jks
ssl.keystore.password=netcool
ssl.key.password=netcool
```

The server.properties needs to be updated with the above.


Explanation of the properties:

- security.protocol=SSL: Specifies that SSL/TLS is used for secure communication.
- ssl.truststore.location: Specifies the path to the truststore file (ca-cert.pem) containing the CA certificate or broker's certificate that your client should trust.
- ssl.truststore.password: The password for the truststore file (set to "netcool" in your case).
- ssl.keystore.location: Specifies the path to the keystore file (server-cert.pem) containing the server (broker) certificate and private key.
- ssl.keystore.password: The password for the keystore file (set to "netcool" in your case).
- ssl.key.password: The password for the server's private key (set to "netcool" in your case).


Quick check on whether the SSL Kafka is working:
```sh
bin/kafka-topics.sh --list --bootstrap-server itz-060001mysy-o144.dte.demo.ibmcloud.com:9093 --command-config config/client-ssl.properties
```



# Testing with kcat with SSL configured

Step 1: Install kcat
```sh
sudo apt-get update
sudo apt-get install kafkacat
sudo ln -s /usr/bin/kafkacat /usr/bin/kcat
```
Step 2: Create a Kafka Topic
Make sure you have already created a Kafka topic with SSL enabled. If you haven't, follow the steps mentioned in the previous responses to set up SSL encryption in Kafka and create a topic.

To check whether there are any topics created in your Kafka cluster, you can use the kafka-topics.sh script that comes with Kafka. This script allows you to list all the topics in the Kafka cluster. Follow this:

```sh
bin/kafka-topics.sh --list --bootstrap-server itz-060001mysy-o144.dte.demo.ibmcloud.com:9093 --command-config config/client-ssl.properties
```

Step 3: Test SSL Publishing (Producer)
Use kcat to test SSL publishing (producing messages) to the Kafka topic. Run the following command:

```sh
kcat -b itz-060001mysy-o144.dte.demo.ibmcloud.com:9093 -X security.protocol=SSL -X ssl.ca.location=<PATH_TO_CA_CERT> -X ssl.certificate.location=<PATH_TO_PRODUCER_CERT> -X ssl.key.location=<PATH_TO_PRODUCER_KEY> -t <TOPIC_NAME> -P

kcat -b itz-060001mysy-o144.dte.demo.ibmcloud.com:9093 -X security.protocol=SSL -X ssl.ca.location=/home/kafka/certificate/ca-cert.pem -X ssl.certificate.location=/home/kafka/certificate/client-cert.pem -X ssl.key.location=/home/kafka/certificate/client-key.pem -t TutorialTopic -P
```

Consumer:
```sh
kcat -b itz-060001mysy-o144.dte.demo.ibmcloud.com:9093      -X security.protocol=SSL      -X ssl.ca.location=/home/kafka/certificate/ca-cert.pem      -X ssl.certificate.location=/home/kafka/certificate/client-cert.pem      -X ssl.key.location=/home/kafka/certificate/client-key.pem      -t TutorialTopic -C -o beginning
```

Another way of testing using kafka-console-producer.sh and kafka-console-consumer.sh

```sh
bin/kafka-console-producer.sh   --broker-list itz-060001mysy-o144.dte.demo.ibmcloud.com:9093   --topic TutorialTopic   --producer.config /home/kafka/kafka/config/client-ssl.properties
```

```sh
bin/kafka-console-consumer.sh --bootstrap-server itz-060001mysy-o144.dte.demo.ibmcloud.com:9093 --topic TutorialTopic --consumer.config config/client-ssl.properties
```

# Python to consume the message

Install pip3:
```sh
sudo apt update
sudo apt install python3-pip
pip3 --version
```



Import confluent_kafka:
```sh
pip3 install confluent-kafka
```

Sample:
```sh
from confluent_kafka import Consumer, KafkaError

# Kafka consumer configuration
#config = {
#    'bootstrap.servers': 'itz-060001mysy-o144.dte.demo.ibmcloud.com:9093',
#    'security.protocol': 'SSL',
#    'ssl.truststore.location': '/home/kafka/kafka/client.truststore.jks',
#    'ssl.truststore.password': 'netcool',
#    'ssl.keystore.location': '/home/kafka/kafka/server-keystore.jks',
#    'ssl.keystore.password': 'netcool',
#    'ssl.key.password': 'netcool',
#    'group.id': 'my-group'  # Change 'my-group' to a unique consumer group ID
#}

config = {
    'bootstrap.servers': 'itz-060001mysy-o144.dte.demo.ibmcloud.com:9093',
    'security.protocol': 'SSL',
    'ssl.ca.location': '/home/kafka/certificate/ca-cert.pem',
    'ssl.certificate.location': '/home/kafka/certificate/client-cert.pem',
    'ssl.key.location': '/home/kafka/certificate/client-key.pem',
    'group.id': 'my-group'  # Change 'my-group' to a unique consumer group ID
}

#config = {
#    'bootstrap.servers': 'itz-060001mysy-o144.dte.demo.ibmcloud.com:9093',
#    'security.protocol': 'SSL',
#    'ssl.keystore.location': '/home/kafka/certificate/server-cert.pem',
#    'ssl.keystore.password': 'netcool',
#    'ssl.key.password': 'netcool',
#    'ssl.truststore.location': '/home/kafka/certificate/ca-cert.pem',
#    'ssl.truststore.password': 'netcool',
#    'group.id': 'my-group'  # Change 'my-group' to a unique consumer group ID
#}

def consume_messages():
    consumer = Consumer(config)
    topic = 'TutorialTopic'

    # Subscribe to the topic
    consumer.subscribe([topic])

    try:
        while True:
            msg = consumer.poll(1.0)

            if msg is None:
                continue
            if msg.error():
                if msg.error().code() == KafkaError._PARTITION_EOF:
                    print(f"Reached end of partition for {msg.topic()} [{msg.partition()}]")
                else:
                    print(f"Error while consuming message: {msg.error()}")
            else:
                # Process the received message
                print(f"Received message: {msg.value().decode('utf-8')}")

    except KeyboardInterrupt:
        print("Exiting...")
    finally:
        consumer.close()

if __name__ == "__main__":
    consume_messages()

```


<picture>
  <img alt="image" src="./assets/images/createAPItoken.png">
</picture>



