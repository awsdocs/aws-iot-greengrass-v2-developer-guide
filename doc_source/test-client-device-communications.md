# Test client device communications<a name="test-client-device-communications"></a>

Client devices can use the AWS IoT Device SDK to discover, connect, and communicate with a core device\. You can use the Greengrass discovery client in the AWS IoT Device SDK to use the [Greengrass discovery API](greengrass-discover-api.md), which returns information about core devices to which a client device can connect\. The API response includes MQTT broker endpoints to connect and certificates to use to verify the identity of each core device\. Then, the client device can try each endpoint until it successfully connects to a core device\.

Client devices can discover only core devices to which you associate them\. Before you test communications between a client device and a core device, you must associate the client device to the core device\. For more information, see [Associate client devices](associate-client-devices.md)\.

The Greengrass discovery API returns the core device MQTT broker endpoints that you specify\. You can use the [IP detector component](ip-detector-component.md) to manage these endpoints for you, or you can manually manage them for each core device\. For more information, see [Manage core device endpoints](manage-core-device-endpoints.md)\.

**Note**  
To use the Greengrass discovery API, a client device must have the `greengrass:Discover` permission\. For more information, see [Minimal AWS IoT policy for client devices](device-auth.md#client-device-minimal-iot-policy)\.

The AWS IoT Device SDK is available in multiple programming languages\. For more information, see [AWS IoT Device SDKs](https://docs.aws.amazon.com/iot/latest/developerguide/iot-sdks.html) in the *AWS IoT Core Developer Guide*\.

**Topics**
+ [Test communications \(Python\)](#test-client-device-communications-python)
+ [Test communications \(C\+\+\)](#test-client-device-communications-cpp)
+ [Test communications \(JavaScript\)](#test-client-device-communications-javascript)
+ [Test communications \(Java\)](#test-client-device-communications-java)

## Test communications \(Python\)<a name="test-client-device-communications-python"></a>

In this section, you use Greengrass discovery sample in the [AWS IoT Device SDK v2 for Python](https://github.com/aws/aws-iot-device-sdk-python-v2) to test communications between a client device and a core device\.

**Important**  
To use the AWS IoT Device SDK v2 for Python, a device must run Python 3\.6 or later\.

**To test communications \(AWS IoT Device SDK v2 for Python\)**

1. <a name="download-iot-device-sdk-python-v2"></a>Download and install the [AWS IoT Device SDK v2 for Python](https://github.com/aws/aws-iot-device-sdk-python-v2) to the AWS IoT thing to connect as a client device\.

   On the client device, do the following:

   1. Clone the AWS IoT Device SDK v2 for Python repository to download it\.

      ```
      git clone https://github.com/aws/aws-iot-device-sdk-python-v2.git
      ```

   1. Install the AWS IoT Device SDK v2 for Python\.

      ```
      python3 -m pip install --user ./aws-iot-device-sdk-python-v2
      ```

1. <a name="cd-iot-device-sdk-python-v2"></a>Change to the samples folder in the AWS IoT Device SDK v2 for Python\.

   ```
   cd aws-iot-device-sdk-python-v2/samples
   ```

1. <a name="test-client-device-communications-application-intro"></a>Run the sample Greengrass discovery application\. This application expects arguments that specify the client device thing name, the MQTT topic and message to use, and the certificates that authenticate and secure the connection\. The following example sends a Hello World message to the `clients/MyClientDevice1/hello/world` topic\.<a name="test-client-device-communications-application-command-replace"></a>
   + Replace both instances of *MyClientDevice1* with the client device's thing name\.
   + Replace *\~/certs/AmazonRootCA1\.pem* with the path to the Amazon root CA certificate on the client device\.
   + Replace *\~/certs/device\.pem\.crt* with the path to the device certificate on the client device\.
   + Replace *\~/certs/private\.pem\.key* with the path to the private key file on the client device\.
   + Replace *us\-east\-1* with the AWS Region where your client device and core device operate\.

   ```
   python3 basic_discovery.py \
     --thing-name MyClientDevice1 \
     --topic 'clients/MyClientDevice1/hello/world' \
     --message 'Hello World!' \
     --root-ca ~/certs/AmazonRootCA1.pem \
     --cert ~/certs/device.pem.crt \
     --key ~/certs/private.pem.key \
     --region us-east-1 \
     --verbosity Warn
   ```

   <a name="test-client-device-communications-application-output-intro"></a>The discovery sample application sends the message 10 times and disconnects\. It also subscribes to the same topic where it publishes messages\. If the output indicates that the application received MQTT messages on the topic, the client device can successfully communicate with the core device\.

   ```
   Performing greengrass discovery...
   awsiot.greengrass_discovery.DiscoverResponse(gg_groups=[awsiot.greengrass_discovery.GGGroup(gg_group_id='greengrassV2-coreDevice-MyGreengrassCore', cores=[awsiot.greengrass_discovery.GGCore(thing_arn='arn:aws:iot:us-east-1:123456789012:thing/MyGreengrassCore', connectivity=[awsiot.greengrass_discovery.ConnectivityInfo(id='203.0.113.0', host_address='203.0.113.0', metadata='', port=8883)])], certificate_authorities=['-----BEGIN CERTIFICATE-----\nMIICiT...EXAMPLE=\n-----END CERTIFICATE-----\n'])])
   Trying core arn:aws:iot:us-east-1:123456789012:thing/MyGreengrassCore at host 203.0.113.0 port 8883
   Connected!
   Published topic clients/MyClientDevice1/hello/world: {"message": "Hello World!", "sequence": 0}
   
   Publish received on topic clients/MyClientDevice1/hello/world
   b'{"message": "Hello World!", "sequence": 0}'
   Published topic clients/MyClientDevice1/hello/world: {"message": "Hello World!", "sequence": 1}
   
   Publish received on topic clients/MyClientDevice1/hello/world
   b'{"message": "Hello World!", "sequence": 1}'
   
   ...
   
   Published topic clients/MyClientDevice1/hello/world: {"message": "Hello World!", "sequence": 9}
   
   Publish received on topic clients/MyClientDevice1/hello/world
   b'{"message": "Hello World!", "sequence": 9}'
   ```

   <a name="test-client-device-communications-application-troubleshooting"></a>If the application outputs an error instead, see [Troubleshooting Greengrass discovery issues](troubleshooting-client-devices.md#greengrass-discovery-issues)\.

   <a name="test-client-device-communications-application-view-core-logs"></a>You can also view the Greengrass logs on the core device to verify if the client device successfully connects and sends messages\. For more information, see [View AWS IoT Greengrass Core software logs](troubleshooting.md#view-greengrass-core-logs)\.

## Test communications \(C\+\+\)<a name="test-client-device-communications-cpp"></a>

In this section, you use Greengrass discovery sample in the [AWS IoT Device SDK v2 for C\+\+](https://github.com/aws/aws-iot-device-sdk-cpp-v2) to test communications between a client device and a core device\.

**Important**  <a name="iot-device-sdk-cpp-v2-build-requirements"></a>
To build the AWS IoT Device SDK v2 for C\+\+, a device must have the following tools:  
C\+\+ 11 or later
CMake 3\.1 or later
One of the following compilers:  
GCC 4\.8 or later
Clang 3\.9 or later
MSVC 2015 or later

**To test communications \(AWS IoT Device SDK v2 for C\+\+\)**

1. Download and build the [AWS IoT Device SDK v2 for C\+\+](https://github.com/aws/aws-iot-device-sdk-cpp-v2) to the AWS IoT thing to connect as a client device\.

   On the client device, do the following:

   1. Create a folder for the AWS IoT Device SDK v2 for C\+\+ workspace, and change to it\.

      ```
      cd
      mkdir iot-device-sdk-cpp
      cd iot-device-sdk-cpp
      ```

   1. Clone the AWS IoT Device SDK v2 for C\+\+ repository to download it\. The `--recursive` flag specifies to download submodules\.

      ```
      git clone --recursive https://github.com/aws/aws-iot-device-sdk-cpp-v2.git
      ```

   1. Create a folder for the AWS IoT Device SDK v2 for C\+\+ build output, and change to it\.

      ```
      mkdir aws-iot-device-sdk-cpp-v2-build
      cd aws-iot-device-sdk-cpp-v2-build
      ```

   1. Build the AWS IoT Device SDK v2 for C\+\+\.

      ```
      cmake -DCMAKE_INSTALL_PREFIX="~/iot-device-sdk-cpp" -DCMAKE_BUILD_TYPE="Release" ../aws-iot-device-sdk-cpp-v2
      cmake --build . --target install
      ```

1. Build the Greengrass discovery sample application in the AWS IoT Device SDK v2 for C\+\+\. Do the following:

   1. Change to the Greengrass discovery sample folder in the AWS IoT Device SDK v2 for C\+\+\.

      ```
      cd ../aws-iot-device-sdk-cpp-v2/samples/greengrass/basic_discovery
      ```

   1. Create a folder for the Greengrass discovery sample build output, and change to it\.

      ```
      mkdir build
      cd build
      ```

   1. Build the Greengrass discovery sample application\.

      ```
      cmake -DCMAKE_PREFIX_PATH="~/iot-device-sdk-cpp" -DCMAKE_BUILD_TYPE="Release" ..
      cmake --build . --config "Release
      ```

1. <a name="test-client-device-communications-application-scanner-intro"></a>Run the sample Greengrass discovery application\. This application expects arguments that specify the client device thing name, the MQTT topic to use, and the certificates that authenticate and secure the connection\. The following example subscribes to the `clients/MyClientDevice1/hello/world` topic and publishes a message that you enter on the command line to the same topic\.<a name="test-client-device-communications-application-command-replace"></a>
   + Replace both instances of *MyClientDevice1* with the client device's thing name\.
   + Replace *\~/certs/AmazonRootCA1\.pem* with the path to the Amazon root CA certificate on the client device\.
   + Replace *\~/certs/device\.pem\.crt* with the path to the device certificate on the client device\.
   + Replace *\~/certs/private\.pem\.key* with the path to the private key file on the client device\.
   + Replace *us\-east\-1* with the AWS Region where your client device and core device operate\.

   ```
   ./basic-discovery \
     --thing_name MyClientDevice1 \
     --topic 'clients/MyClientDevice1/hello/world' \
     --ca_file ~/certs/AmazonRootCA1.pem \
     --cert ~/certs/device.pem.crt \
     --key ~/certs/private.pem.key \
     --region us-east-1
   ```

   <a name="test-client-device-communications-application-scanner-output-intro"></a>The discovery sample application subscribes to the topic and prompts you to enter a message to publish\. 

   ```
   Connecting to group greengrassV2-coreDevice-MyGreengrassCore with thing arn arn:aws:iot:us-east-1:123456789012:thing/MyGreengrassCore, using endpoint 203.0.113.0:8883
   Connected to group greengrassV2-coreDevice-MyGreengrassCore, using connection to 203.0.113.0:8883
   Successfully subscribed to clients/MyClientDevice1/hello/world
   Enter the message you want to publish to topic clients/MyClientDevice1/hello/world and press enter. Enter 'exit' to exit this program.
   ```

   <a name="test-client-device-communications-application-troubleshooting"></a>If the application outputs an error instead, see [Troubleshooting Greengrass discovery issues](troubleshooting-client-devices.md#greengrass-discovery-issues)\.

1. <a name="test-client-device-communications-application-scanner-input"></a>Enter a message, such as **Hello World\!**\.

   ```
   Enter the message you want to publish to topic clients/MyClientDevice1/hello/world and press enter. Enter 'exit' to exit this program.
   Hello World!
   ```

   <a name="test-client-device-communications-application-scanner-input-output"></a>If the output indicates that the application received the MQTT message on the topic, the client device can successfully communicate with the core device\.

   ```
   Operation on packetId 2 Succeeded
   Publish received on topic clients/MyClientDevice1/hello/world
   Message:
   Hello World!
   ```

   <a name="test-client-device-communications-application-view-core-logs"></a>You can also view the Greengrass logs on the core device to verify if the client device successfully connects and sends messages\. For more information, see [View AWS IoT Greengrass Core software logs](troubleshooting.md#view-greengrass-core-logs)\.

## Test communications \(JavaScript\)<a name="test-client-device-communications-javascript"></a>

In this section, you use Greengrass discovery sample in the [AWS IoT Device SDK v2 for JavaScript](https://github.com/aws/aws-iot-device-sdk-js-v2) to test communications between a client device and a core device\. 

**Important**  
To use the AWS IoT Device SDK v2 for JavaScript, a device must run Node v10\.0 or later\.

**To test communications \(AWS IoT Device SDK v2 for JavaScript\)**

1. Download and install the [AWS IoT Device SDK v2 for JavaScript](https://github.com/aws/aws-iot-device-sdk-js-v2) to the AWS IoT thing to connect as a client device\.

   On the client device, do the following:

   1. Clone the AWS IoT Device SDK v2 for JavaScript repository to download it\.

      ```
      git clone https://github.com/aws/aws-iot-device-sdk-js-v2.git
      ```

   1. Install the AWS IoT Device SDK v2 for JavaScript\.

      ```
      cd aws-iot-device-sdk-js-v2
      npm install
      ```

1. Change to the Greengrass discovery sample folder in the AWS IoT Device SDK v2 for JavaScript\.

   ```
   cd samples/node/basic_discovery
   ```

1. Install the Greengrass discovery sample application\.

   ```
   npm install
   ```

1. <a name="test-client-device-communications-application-intro"></a>Run the sample Greengrass discovery application\. This application expects arguments that specify the client device thing name, the MQTT topic and message to use, and the certificates that authenticate and secure the connection\. The following example sends a Hello World message to the `clients/MyClientDevice1/hello/world` topic\.<a name="test-client-device-communications-application-command-replace"></a>
   + Replace both instances of *MyClientDevice1* with the client device's thing name\.
   + Replace *\~/certs/AmazonRootCA1\.pem* with the path to the Amazon root CA certificate on the client device\.
   + Replace *\~/certs/device\.pem\.crt* with the path to the device certificate on the client device\.
   + Replace *\~/certs/private\.pem\.key* with the path to the private key file on the client device\.
   + Replace *us\-east\-1* with the AWS Region where your client device and core device operate\.

   ```
   node dist/index.js \
     --thing_name MyClientDevice1 \
     --topic 'clients/MyClientDevice1/hello/world' \
     --message 'Hello World!' \
     --ca_file ~/certs/AmazonRootCA1.pem \
     --cert ~/certs/device.pem.crt \
     --key ~/certs/private.pem.key \
     --region us-east-1 \
     --verbose warn
   ```

   <a name="test-client-device-communications-application-output-intro"></a>The discovery sample application sends the message 10 times and disconnects\. It also subscribes to the same topic where it publishes messages\. If the output indicates that the application received MQTT messages on the topic, the client device can successfully communicate with the core device\.

   ```
   Discovery Response:
   {"gg_groups":[{"gg_group_id":"greengrassV2-coreDevice-MyGreengrassCore","cores":[{"thing_arn":"arn:aws:iot:us-east-1:123456789012:thing/MyGreengrassCore","connectivity":[{"id":"203.0.113.0","host_address":"203.0.113.0","port":8883,"metadata":""}]}],"certificate_authorities":["-----BEGIN CERTIFICATE-----\nMIICiT...EXAMPLE=\n-----END CERTIFICATE-----\n"]}]}
   Trying endpoint={"id":"203.0.113.0","host_address":"203.0.113.0","port":8883,"metadata":""}
   [WARN] [2021-06-12T00:46:45Z] [00007f90c0e8d700] [socket] - id=0x7f90b8018710 fd=26: setsockopt() for NO_SIGNAL failed with errno 92. If you are having SIGPIPE signals thrown, you may want to install a signal trap in your application layer.
   Connected to endpoint={"id":"203.0.113.0","host_address":"203.0.113.0","port":8883,"metadata":""}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":1}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":2}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":3}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":4}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":5}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":6}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":7}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":8}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":9}
   Publish received. topic:"clients/MyClientDevice1/hello/world" dup:false qos:0 retain:false
   {"message":"Hello World!","sequence":10}
   Complete!
   ```

   <a name="test-client-device-communications-application-troubleshooting"></a>If the application outputs an error instead, see [Troubleshooting Greengrass discovery issues](troubleshooting-client-devices.md#greengrass-discovery-issues)\.

   <a name="test-client-device-communications-application-view-core-logs"></a>You can also view the Greengrass logs on the core device to verify if the client device successfully connects and sends messages\. For more information, see [View AWS IoT Greengrass Core software logs](troubleshooting.md#view-greengrass-core-logs)\.

## Test communications \(Java\)<a name="test-client-device-communications-java"></a>

In this section, you use Greengrass discovery sample in the [AWS IoT Device SDK v2 for Java](https://github.com/aws/aws-iot-device-sdk-java-v2) to test communications between a client device and a core device\.

**Important**  
To build the AWS IoT Device SDK v2 for Java, a device must have the following tools:  
Java 8 or later, with `JAVA_HOME` pointing to the Java folder\.
Apache Maven

**To test communications \(AWS IoT Device SDK v2 for Java\)**

1. Download and build the [AWS IoT Device SDK v2 for Java](https://github.com/aws/aws-iot-device-sdk-java-v2) to the AWS IoT thing to connect as a client device\.

   On the client device, do the following:

   1. Clone the AWS IoT Device SDK v2 for Java repository to download it\.

      ```
      git clone https://github.com/aws/aws-iot-device-sdk-java-v2.git
      ```

   1. Change to the AWS IoT Device SDK v2 for Java folder\.

   1. Build the AWS IoT Device SDK v2 for Java\.

      ```
      cd aws-iot-device-sdk-java-v2
      mvn versions:use-latest-versions -Dincludes="software.amazon.awssdk.crt*"
      mvn clean install
      ```

1. <a name="test-client-device-communications-application-scanner-intro"></a>Run the sample Greengrass discovery application\. This application expects arguments that specify the client device thing name, the MQTT topic to use, and the certificates that authenticate and secure the connection\. The following example subscribes to the `clients/MyClientDevice1/hello/world` topic and publishes a message that you enter on the command line to the same topic\.<a name="test-client-device-communications-application-command-replace"></a>
   + Replace both instances of *MyClientDevice1* with the client device's thing name\.
   + Replace *$HOME/certs/AmazonRootCA1\.pem* with the path to the Amazon root CA certificate on the client device\.
   + Replace *$HOME/certs/device\.pem\.crt* with the path to the device certificate on the client device\.
   + Replace *$HOME/certs/private\.pem\.key* with the path to the private key file on the client device\.
   + Replace *us\-east\-1* with the AWS Region where your client device and core device operate\.

   ```
   DISCOVERY_SAMPLE_ARGS="--thingName MyClientDevice1 \
     --topic 'clients/MyClientDevice1/hello/world' \
     --rootca $HOME/certs/AmazonRootCA1.pem \
     --cert $HOME/certs/device.pem.crt \
     --key $HOME/certs/private.pem.key \
     --region us-east-1"
   
   mvn exec:java -pl samples/Greengrass \
     -Dexec.mainClass=greengrass.BasicDiscovery \
     -Dexec.args="$DISCOVERY_SAMPLE_ARGS"
   ```

   <a name="test-client-device-communications-application-scanner-output-intro"></a>The discovery sample application subscribes to the topic and prompts you to enter a message to publish\. 

   ```
   Connecting to group ID greengrassV2-coreDevice-MyGreengrassCore, with thing arn arn:aws:iot:us-east-1:123456789012:thing/MyGreengrassCore, using endpoint 203.0.113.0:8883
   Started a clean session
   Enter the message you want to publish to topic clients/MyClientDevice1/hello/world and press Enter. Type 'exit' or 'quit' to exit this program:
   ```

   <a name="test-client-device-communications-application-troubleshooting"></a>If the application outputs an error instead, see [Troubleshooting Greengrass discovery issues](troubleshooting-client-devices.md#greengrass-discovery-issues)\.

1. <a name="test-client-device-communications-application-scanner-input"></a>Enter a message, such as **Hello World\!**\.

   ```
   Enter the message you want to publish to topic clients/MyClientDevice1/hello/world and press Enter. Type 'exit' or 'quit' to exit this program:
   Hello World!
   ```

   <a name="test-client-device-communications-application-scanner-input-output"></a>If the output indicates that the application received the MQTT message on the topic, the client device can successfully communicate with the core device\.

   ```
   Message received on topic clients/MyClientDevice1/hello/world: Hello World!
   ```

   <a name="test-client-device-communications-application-view-core-logs"></a>You can also view the Greengrass logs on the core device to verify if the client device successfully connects and sends messages\. For more information, see [View AWS IoT Greengrass Core software logs](troubleshooting.md#view-greengrass-core-logs)\.