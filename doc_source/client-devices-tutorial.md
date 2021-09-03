# Tutorial: Connect and test client devices<a name="client-devices-tutorial"></a>

You can complete this tutorial to configure a core device to interact with local IoT devices, called *client devices*, that connect to the core device over MQTT\. In this tutorial, you configure AWS IoT things to use *cloud discovery* to connect to the core device as client devices\. When you configure cloud discovery, a client device can send a request to the AWS IoT Greengrass cloud service to discover core devices\. The response from AWS IoT Greengrass includes connectivity information and certificates for the core devices that you configure the client device to discover\. Then, the client device can use this information to connect to an available core device where it can communicate over MQTT\.

In this tutorial, you do the following:
+ Review and update the core device's permissions, if needed\.
+ Associate client devices to the core device, so they can discover the core device using cloud discovery\.
+ Deploy Greengrass components to the core device to enable client device support\.
+ Connect client devices to the core device and test communication with the AWS IoT Core cloud service\.
+ Develop a custom Greengrass component that interacts with the client devices\.

This tutorial uses a single core device and a single client device\. You can also follow the tutorial to connect and test multiple client devices\.

You can expect to spend 30–50 minutes on this tutorial\.

## Prerequisites<a name="prerequisites"></a>

To complete this tutorial, you must meet the following prerequisites:
+ An AWS account\. If you don't have one, see [Set up an AWS account](setting-up.md#set-up-aws-account)\.
+ An AWS Identity and Access Management \(IAM\) user with administrator permissions\.
+ A Greengrass core device\. For more information about how to set up a core device, see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.
  + The core device must be on the same network as the client devices to connect\.
  + \(Optional\) To complete the module where you develop a custom component that interacts with client devices, the core device must run the Greengrass CLI\. For more information, see [Install the Greengrass CLI](install-gg-cli.md)\.
+ An AWS IoT thing to connect as a client device in this tutorial\. For more information, see [Create AWS IoT resources](https://docs.aws.amazon.com/iot/latest/developerguide/create-iot-resources.html) in the *AWS IoT Core Developer Guide*\.
  + The client device's AWS IoT policy must allow the `greengrass:Discover` permission\. For more information, see [Minimal AWS IoT policy for client devices](device-auth.md#client-device-minimal-iot-policy)\.
  + The client device must be on the same network as the core device\.
  + The client device must run [Python 3](https://www.python.org/)\.
  + The client device must run [Git](https://git-scm.com/)\.

## Review and update the core device AWS IoT policy<a name="update-core-device-iot-policy-client-devices"></a>

To support client devices, a core device's AWS IoT policy must allow the following permissions:
+ `greengrass:PutCertificateAuthorities`
+ `greengrass:VerifyClientDeviceIdentity`
+ `greengrass:VerifyClientDeviceIoTCertificateAssociation`
+ `greengrass:GetConnectivityInfo`
+ `greengrass:UpdateConnectivityInfo` – \(Optional\) This permission is required to use the [IP detector component](ip-detector-component.md), which reports the core device's network connectivity information to the AWS IoT Greengrass cloud service\.

For more information, see [AWS IoT policies for data plane operations](device-auth.md#iot-policies) and [Minimal AWS IoT policy to support client devices](device-auth.md#client-device-support-minimal-iot-policy)\.

In this section, you review the AWS IoT policies for your core device and add any required permissions that are missing\. If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), your core device has an AWS IoT policy that allows access to all AWS IoT Greengrass actions \(`greengrass:*`\)\. 

**To review and update a core device's AWS IoT policy**

1. <a name="update-iot-policy-open-greengrass-console"></a>In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Core devices**\.

1. <a name="update-iot-policy-choose-core-device"></a>On the **Core devices** page, choose the core device to update\.

1. <a name="update-iot-policy-choose-core-device-thing"></a>On the core device details page, choose the link to the core device's **Thing**\. This link opens the thing details page in the AWS IoT console\.

1. <a name="update-iot-policy-choose-thing-security"></a>On the thing details page, choose **Certificates**\.

1. <a name="update-iot-policy-choose-thing-certificate"></a>In the **Certificates** tab, choose the thing's active certificate\.

1. <a name="update-iot-policy-choose-certificate-policies"></a>On the certificate details page, choose **Policies**\.

1. <a name="update-iot-policy-choose-policy"></a>In the **Policies** tab, choose the AWS IoT policy to review and update\. You can add the required permissions to any policy that is attached to the core device's active certificate\.
**Note**  
If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), you have two AWS IoT policies\. We recommend that you choose the policy named **GreengrassV2IoTThingPolicy**, if it exists\. Core devices that you create with the quick installer use this policy name by default\. If you add permissions to this policy, you are also granting these permissions to other core devices that use this policy\.

1. <a name="update-iot-policy-edit-policy"></a>In the policy overview, choose **Edit policy document**\.

1. Review the policy for the required permissions, and add any required permissions that are missing\.

1. <a name="update-iot-policy-save-policy"></a>Choose **Save as new version**\.

## Enable client device support<a name="enable-client-device-support"></a>

For a client device to use cloud discovery to connect to a core device, you must associate the devices\. When you associate a client device to a core device, you enable that client device to retrieve the core device's IP addresses and certificates to use to connect\.

To enable client devices to securely connect to a core device and communicate with Greengrass components and AWS IoT Core, you deploy the following Greengrass components to the core device:<a name="client-device-component-overviews"></a>
+ [Client device auth](client-device-auth-component.md) \(`aws.greengrass.clientdevices.Auth`\)

  Deploy the client device auth component to authenticate client devices and authorize client device actions\. This component allows your AWS IoT things to connect to a core device\.

  This component requires some configuration to use it\. You must specify groups of client devices and the operations that each group is authorized to perform, such as to connect and communicate over MQTT\. For more information, see [client device auth component configuration](client-device-auth-component.md#client-device-auth-component-configuration)\.
+ [MQTT broker \(Moquette\)](mqtt-broker-moquette-component.md) \(`aws.greengrass.clientdevices.mqtt.Moquette`\)

  Deploy the Moquette MQTT broker component to run the open source Moquette MQTT broker\. The Moquette MQTT broker is compliant with MQTT 3\.1\.1 and includes local support for QoS 0, QoS 1, QoS 2, retained messages, last will messages, and persistent subscriptions\.

  You aren't required to configure this component to use it\. However, you can configure the port where this component operates the MQTT broker\. By default, it uses port 8883\.
+ [MQTT bridge](mqtt-bridge-component.md) \(`aws.greengrass.clientdevices.mqtt.Bridge`\)

  \(Optional\) Deploy the MQTT bridge component to relay messages between client devices \(local MQTT\), local publish/subscribe, and AWS IoT Core MQTT\. Configure this component to sync client devices with AWS IoT Core and interact with client devices from Greengrass components\.

  This component requires configuration to use\. You must specify the topic mappings where this component relays messages\. For more information, see [MQTT bridge component configuration](mqtt-bridge-component.md#mqtt-bridge-component-configuration)\.
+ [IP detector](ip-detector-component.md) \(`aws.greengrass.clientdevices.IPDetector`\)

  \(Optional\) Deploy the IP detector component to automatically report the core device's MQTT broker endpoints to the AWS IoT Greengrass cloud service\. You cannot use this component if you have a complex network setup, such as one where a router forwards the MQTT broker port to the core device\.

  You aren't required to configure this component to use it\.

In this section, you use the AWS IoT Greengrass console to associate client devices and deploy client device components to a core device\.

**To enable client device support**

1. <a name="navigate-greengrass-console"></a>Navigate to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. In the left navigation menu, choose **Core devices**\.

1. On the **Core devices** page, choose the core device where you want to enable client device support\.

1. On the core device details page, choose the **Client devices** tab\.

1. On the **Client devices** tab, choose **Configure cloud discovery**\.

   The **Configure core device discovery** page opens\. On this page, you can associate client devices to a core device and deploy client device components\. This page selects the core device for you in **Step 1: Select target core devices**\.
**Note**  
You can also use this page to configure core device discovery for a thing group\. If you choose this option, you can deploy client device components to all core devices in a thing group\. However, if you choose this option, you must manually associate client devices to each core device later after you create the deployment\. In this tutorial, you configure a single core device\.

1. In **Step 2: Associate client devices**, associate the client device's AWS IoT thing to the core device\. This enables the client device to use cloud discovery to retrieve the core device's connectivity information and certificates\. Do the following:

   1. Choose **Associate client devices**\.

   1. In the **Associate client devices with core device** modal, enter the name of the AWS IoT thing to associate\.

   1. Choose **Add**\.

   1. Choose **Associate**\.

1. In **Step 3: Configure and deploy Greengrass components**, deploy components to enable client device support\. If the target core device has a previous deployment, this page revises that deployment\. Otherwise, this page creates a new deployment for the core device\. Do the following to configure and deploy the client device components:

   1. The core device must run [Greengrass nucleus](greengrass-nucleus-component.md) v2\.2\.0 or later to support client devices\. If the core device runs an earlier version, select the box to deploy the **aws\.greengrass\.Nucleus** component\.
**Note**  
If you upgrade the Greengrass nucleus from an earlier minor version, and the core device runs [public components](public-components.md) that depend on the nucleus, you must also update the public components to newer versions\. You can configure the version of these components when you review the deployment later in this tutorial\. For more information, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.

   1. For the **aws\.greengrass\.clientdevices\.Auth** component, choose **Edit configuration**\.

   1. In the **Edit configuration** modal for the client device auth component, configure an authorization policy that allows client devices to publish and subscribe to the MQTT broker on the core device\. Do the following:

      1. Under **Configuration**, in the **Configuration to merge** code block, enter the following configuration, which contains a *device group authorization policy*\. Each device group authorization policy specifies a set of actions and the resources on which a client device can perform those actions\.
         + This policy allows client devices whose names start with `MyClientDevice` to connect and communicate on all MQTT topics\. Replace *MyClientDevice\** with the name of the AWS IoT thing to connect as a client device\. You can also specify a name with the `*` wildcard that matches the client device's name\. The `*` wildcard must be at the end of the name\.

           If you have a second client device to connect, replace *MyOtherClientDevice\** with the name of that client device, or a wildcard pattern that matches that client device's name\. Otherwise, you can remove or keep this section of the selection rule that allows client devices with names that match `MyOtherClientDevice*` to connect and communicate\.
         + This policy uses an `OR` operator to also allow client devices whose names start with `MyOtherClientDevice` to connect and communicate on all MQTT topics\. You can remove this clause in the selection rule or modify it to match the client devices to connect\.
         + This policy allows the client devices to publish and subscribe on all MQTT topics\. To follow best security practices, restrict the `mqtt:publish` and `mqtt:subscribe` operations to the minimal set of topics that the client devices use to communicate\.

         ```
         {
           "deviceGroups": {
             "formatVersion": "2021-03-05",
             "definitions": {
               "MyDeviceGroup": {
                 "selectionRule": "thingName: MyClientDevice* OR thingName: MyOtherClientDevice*",
                 "policyName": "MyClientDevicePolicy"
               }
             },
             "policies": {
               "MyClientDevicePolicy": {
                 "AllowConnect": {
                   "statementDescription": "Allow client devices to connect.",
                   "operations": [
                     "mqtt:connect"
                   ],
                   "resources": [
                     "*"
                   ]
                 },
                 "AllowPublish": {
                   "statementDescription": "Allow client devices to publish to all topics.",
                   "operations": [
                     "mqtt:publish"
                   ],
                   "resources": [
                     "*"
                   ]
                 },
                 "AllowSubscribe": {
                   "statementDescription": "Allow client devices to subscribe to all topics.",
                   "operations": [
                     "mqtt:subscribe"
                   ],
                   "resources": [
                     "*"
                   ]
                 }
               }
             }
           }
         }
         ```

         For more information, see [Client device auth component configuration](client-device-auth-component.md#client-device-auth-component-configuration)\.

      1. Choose **Confirm**\.

   1. For the **aws\.greengrass\.clientdevices\.mqtt\.Bridge** component, choose **Edit configuration**\.

   1. In the **Edit configuration** modal for the MQTT bridge component, configure a topic mapping that relays MQTT messages from client devices to AWS IoT Core\. Do the following:

      1. Under **Configuration**, in the **Configuration to merge** code block, enter the following configuration\. This configuration specifies to relay MQTT messages on the `clients/+/hello/world` topic filter from client devices to the AWS IoT Core cloud service\. For example, this topic filter matches the `clients/MyClientDevice1/hello/world` topic\.

         ```
         {
           "mqttTopicMapping": {
             "HelloWorldIotCoreMapping": {
               "topic": "clients/+/hello/world",
               "source": "LocalMqtt",
               "target": "IotCore"
             }
           }
         }
         ```

         For more information, see [MQTT bridge component configuration](mqtt-bridge-component.md#mqtt-bridge-component-configuration)\.

      1. Choose **Confirm**\.

1. <a name="choose-review-and-deploy-configure-discovery-step"></a>Choose **Review and deploy** to review the deployment that this page creates for you\.

1. If you haven't previously set up the [Greengrass service role](greengrass-service-role.md) in this Region, the console opens a modal to set up the service role for you\. The client device auth component uses this service role to verify the identity of client devices\. Choose **Grant permissions**\.

1. <a name="choose-deploy-configure-discovery-step"></a>On the **Review** page, choose **Deploy** to start the deployment to the core device\.

1. <a name="verify-deployment-success-step"></a>To verify that the deployment succeeds, check the status of the deployment, and check the logs on the core device\. To check the status of the deployment on the core device, you can choose **Target** in the deployment **Overview**\. For more information, see the following:
   + [Check deployment status](check-deployment-status.md)
   + [View AWS IoT Greengrass Core software logs](troubleshooting.md#view-greengrass-core-logs)

## Connect client devices<a name="connect-client-devices"></a>

Client devices can use the AWS IoT Device SDK to discover, connect, and communicate with a core device\. In this section, you install the [AWS IoT Device SDK v2 for Python](https://github.com/aws/aws-iot-device-sdk-python-v2) and run the Greengrass discovery sample application from the AWS IoT Device SDK\.

**Note**  
The AWS IoT Device SDK is also available in other programming languages\. This tutorial uses the AWS IoT Device SDK v2 for Python, but you can explore the other SDKs for your use case\. For more information, see [AWS IoT Device SDKs](https://docs.aws.amazon.com/iot/latest/developerguide/iot-sdks.html) in the *AWS IoT Core Developer Guide*\.

**To connect a client device to a core device**

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

1. <a name="test-client-device-communications-application-intro"></a>Run the sample Greengrass discovery application\. This application expects arguments that specify the client device thing name, the MQTT topic and message to use, and the certificates that authenticate and secure the connection\. The following example sends a Hello World message to the `clients/MyClientDevice1/hello/world` topic\.
**Note**  
This topic is the same topic where you configured the MQTT bridge to relay messages to AWS IoT Core earlier\.<a name="test-client-device-communications-application-command-replace"></a>
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

1. Verify that the MQTT bridge relays the messages from the client device to AWS IoT Core\. You can use the MQTT test client in the AWS IoT Core console to subscribe to an MQTT topic filter\. Do the following:

   1. Navigate to the [AWS IoT console](https://console.aws.amazon.com/iot)\.

   1. In the left navigation menu, under **Test**, choose **MQTT test client**\.

   1. On the **Subscribe to a topic** tab, for **Topic filter**, enter `clients/+/hello/world` to subscribe to client device messages from the core device\.

   1. Choose **Subscribe**\.

   1. Run the publish/subscribe application on the client device again\.

      The MQTT test client displays the messages that you send from the client device on topics that match this topic filter\.

## Develop a component that interacts with client devices<a name="develop-client-device-subscriber-component"></a>

You can develop Greengrass components that interact with client devices\. Components use [interprocess communication \(IPC\)](interprocess-communication.md) and the [local publish/subscribe interface](ipc-publish-subscribe.md) to communicate on a core device\. To interact with client devices, configure the MQTT bridge component to relay messages between client devices and local publish/subscribe\.

In this section, you update the MQTT bridge component to relay messages from client devices to the local publish/subscribe interface\. Then, you develop a component that subscribes to these messages and prints the messages when it receives them\.

**To develop a component that interacts with client devices**

1. Revise the deployment to the core device and configure the MQTT bridge component to relay messages from client devices to local publish/subscribe\. Do the following:

   1. <a name="navigate-greengrass-console"></a>Navigate to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

   1. In the left navigation menu, choose **Core devices**\.

   1. On the **Core devices** page, choose the core device that you are using for this tutorial\.

   1. On the core device details page, choose the **Client devices** tab\.

   1. On the **Client devices** tab, choose **Configure cloud discovery**\.

      The **Configure core device discovery** page opens\. On this page, you can change or configure which client device components deploy to the core device\.

   1. In **Step 3**, for the **aws\.greengrass\.clientdevices\.mqtt\.Bridge** component, choose **Edit configuration**\.

   1. In the **Edit configuration** modal for the MQTT bridge component, configure a topic mapping that relays MQTT messages from client devices to the local publish/subscribe interface\. Do the following:

      1. Under **Configuration**, in the **Configuration to merge** code block, enter the following configuration\. This configuration specifies to relay MQTT messages on topics that match the `clients/+/hello/world` topic filter from client devices to the AWS IoT Core cloud service and the local Greengrass publish/subscribe broker\.

         ```
         {
           "mqttTopicMapping": {
             "HelloWorldIotCoreMapping": {
               "topic": "clients/+/hello/world",
               "source": "LocalMqtt",
               "target": "IotCore"
             },
             "HelloWorldPubsubMapping": {
               "topic": "clients/+/hello/world",
               "source": "LocalMqtt",
               "target": "Pubsub"
             }
           }
         }
         ```

         For more information, see [MQTT bridge component configuration](mqtt-bridge-component.md#mqtt-bridge-component-configuration)\.

      1. Choose **Confirm**\.

   1. <a name="choose-review-and-deploy-configure-discovery-step"></a>Choose **Review and deploy** to review the deployment that this page creates for you\.

   1. <a name="choose-deploy-configure-discovery-step"></a>On the **Review** page, choose **Deploy** to start the deployment to the core device\.

   1. <a name="verify-deployment-success-step"></a>To verify that the deployment succeeds, check the status of the deployment, and check the logs on the core device\. To check the status of the deployment on the core device, you can choose **Target** in the deployment **Overview**\. For more information, see the following:
      + [Check deployment status](check-deployment-status.md)
      + [View AWS IoT Greengrass Core software logs](troubleshooting.md#view-greengrass-core-logs)

1. Develop and deploy a Greengrass component that subscribes to Hello World messages from client devices\. For more information, see [Create custom AWS IoT Greengrass components](create-components.md)\.

   1. Create a component recipe with the following contents\. This recipe specifies installing the AWS IoT Device SDK v2 for Python and running a script that subscribes to the topic and prints messages\.

      ```
      {
        "RecipeFormatVersion": "2020-01-25",
        "ComponentName": "com.example.clientdevices.MyHelloWorldSubscriber",
        "ComponentVersion": "1.0.0",
        "ComponentDescription": "A component that subscribes to Hello World messages from client devices.",
        "ComponentPublisher": "Amazon",
        "ComponentConfiguration": {
          "DefaultConfiguration": {
            "accessControl": {
              "aws.greengrass.ipc.pubsub": {
                "com.example.clientdevices.MyHelloWorldSubscriber:pubsub:1": {
                  "policyDescription": "Allows access to subscribe to all topics.",
                  "operations": [
                    "aws.greengrass#SubscribeToTopic"
                  ],
                  "resources": [
                    "*"
                  ]
                }
              }
            }
          }
        },
        "Manifests": [
          {
            "Lifecycle": {
              "Install": "python3 -m pip install --user awsiotsdk",
              "Run": "python3 -u {artifacts:path}/hello_world_subscriber.py"
            }
          }
        ]
      }
      ```

   1. Create a Python script artifact named `hello_world_subscriber.py` with the following contents\. This application uses the publish/subscribe IPC service to subscribe to the `clients/MyClientDevice1/hello/world` topic and print messages that it receives\. Replace *MyClientDevice1* with the name of the client device\.

      ```
      import concurrent.futures
      import sys
      import time
      import traceback
      
      import awsiot.greengrasscoreipc
      import awsiot.greengrasscoreipc.client as client
      from awsiot.greengrasscoreipc.model import (
          SubscribeToTopicRequest,
          SubscriptionResponseMessage,
          UnauthorizedError
      )
      
      topic = "clients/MyClientDevice1/hello/world"
      TIMEOUT = 10
      
      
      class StreamHandler(client.SubscribeToTopicStreamHandler):
          def __init__(self):
              super().__init__()
      
          def on_stream_event(self, event: SubscriptionResponseMessage) -> None:
              try:
                  message = str(event.binary_message.message, "utf-8")
                  print("Received new message: " + message)
              except:
                  traceback.print_exc()
      
          def on_stream_error(self, error: Exception) -> bool:
              print("Received a stream error.", file=sys.stderr)
              traceback.print_exc()
              return False  # Return True to close stream, False to keep stream open.
      
          def on_stream_closed(self) -> None:
              print('Subscribe to topic stream closed.')
      
      
      try:
          ipc_client = awsiot.greengrasscoreipc.connect()
      
          request = SubscribeToTopicRequest()
          request.topic = topic
          handler = StreamHandler()
          operation = ipc_client.new_subscribe_to_topic(handler)
          future = operation.activate(request)
      
          try:
              future.result(TIMEOUT)
              print('Successfully subscribed to topic: ' + topic)
          except concurrent.futures.TimeoutError as e:
              print('Timeout occurred while subscribing to topic: ' + topic, file=sys.stderr)
              raise e
          except UnauthorizedError as e:
              print('Unauthorized error while subscribing to topic: ' + topic, file=sys.stderr)
              raise e
          except Exception as e:
              print('Exception while subscribing to topic: ' + topic, file=sys.stderr)
              raise e
      
          # Keep the main thread alive, or the process will exit.
          try:
              while True:
                  time.sleep(10)
          except InterruptedError:
              print('Subscribe interrupted.')
      except Exception:
          print('Exception occurred when using IPC.', file=sys.stderr)
          traceback.print_exc()
          exit(1)
      ```

   1. Use the Greengrass CLI to deploy the component\.

      ```
      sudo /greengrass/v2/bin/greengrass-cli deployment create \
        --recipeDir recipes \
        --artifactDir artifacts \
        --merge "com.example.clientdevices.MyHelloWorldSubscriber=1.0.0"
      ```

1. View the component logs to verify that the component installs successfully and subscribes to the topic\.

   ```
   sudo tail -f /greengrass/v2/logs/com.example.clientdevices.MyHelloWorldSubscriber.log
   ```

   You can keep the log feed open to verify that the core device receives messages\.

1. On the client device, run the sample Greengrass discovery application again to send messages to the core device\.

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

1. View the component logs again to verify that the component receives and prints the messages from the client device\.

   ```
   sudo tail -f /greengrass/v2/logs/com.example.clientdevices.MyHelloWorldSubscriber.log
   ```

You've completed this tutorial\. The client device connects to the core device and sends MQTT messages to AWS IoT Core and Greengrass components\. For more information about the topics covered in this tutorial, see the following:
+ [Associate client devices](associate-client-devices.md)
+ [Manage core device endpoints](manage-core-device-endpoints.md)
+ [Test client device communications](test-client-device-communications.md)
+ [Greengrass discovery RESTful API](greengrass-discover-api.md)
+ [Relay MQTT messages between client devices and AWS IoT Core](relay-client-device-messages.md)
+ [Interact with client devices in components](interact-with-client-devices-in-components.md)