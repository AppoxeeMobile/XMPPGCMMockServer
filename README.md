# A GCM CCS/XMPP Mock server (Java)

(for specific directions of Teradata, see at the bottom of this document)

GCM CCS Mock server is an XMPP server to simulate the Google Cloud Messaging (GCM) Cloud Connection Server (CCS).
It is useful in the development, QA and performance test environment.

## Features
* an XMPP server based on Apache Vysper with a custom GCM extension that supports the CCS stanzas i.e. Ack, Nack, delivery receipt and draining.
* A message store to store the incoming message. It is useful for test verification.
* A simple way to trigger Nack message and draining control message.

## Requirements
* Apache Vysper
* Dagger
* Jedis
* Dagger
* Slf4j

## Installation

### Build
build the jar with dependencies:

	mvn clean compile assembly:single

### Install

copy the jar file to target location:

	cp target/mock-gcm-xmpp-server-1.0.0-SNAPSHOT-jar-with-dependencies.jar <target_location>

## Configuration

* Take a look at the configuration file src/main/resource/config.properties. Create a new config file if there is a need to change it.
* You can use the built-in keystore (src/main/resources/mockgcm.keystore) or supply your own or create a new one with the keytool command, e.g.

		keytool -genkey -alias mockgcm -keysize 512 -validity 365 -keyalg RSA -dname "CN=yourco.com,O=your co.,L=SF,ST=CA,C=US" -keypass password -storepass password -keystore mockgcm.cert


## Run it

1. [NOT NEEDED!] start the Redis server if message store is needed (message.store.enabled=true).
2. start the mock server:

		java -cp <target_location>/mockgcmxmppserver-1.0.0-SNAPSHOT-jar-with-dependencies.jar -DconfigFile=<CONFIG_FILE_PATH> com.jql.gcmccsmock.MockGCMXmppServer

## Usage

* To use it, simply have your GCM CCS provider connect to the mock server with the same credentials and keystore as specified in the config file. Then the provider can send GCM requests as usual. If all is good, for each GCM request, the provider will receive an Ack message and a delivery receipt shortly after.
* To trigger a Nack message, use the registration id specified in the configuration property bad.registration.id.
* To trigger a draining control message, use the registration id specified in the configuration property draining.registration.id.

Enjoy and if you find it useful, drop me a line or star it. Thanks.

## Teradata specific instructions
1. On Windows OS, you can use the my.cmd to run the mock server.
2. If you see SSL problems when a client tries to connect to the mock server, this is probably because of certificate issues.
do the following:
Create a new cert file with this command:

"\Program Files\Java\jdk1.8.0_74\jre\bin\keytool.exe" -genkey -alias localhost -keysize 512 -validity 365 -keyalg RSA -dname "CN=localhost,O=localhost,L=SF,ST=CA,C=US" -keypass password -storepass password -keystore mockgcm.cert -ext san=dns:localhost

Copy the file mockgcm.cert to c:/temp/mock1.cert

In config1.properties replace this line:
keystore.path=mockgcm.keystore
With this one:
keystore.path=c:/temp/mock1.cert

