# Tutorial: Interact with local IoT devices over MQTT<a name="client-devices-tutorial"></a>

You can complete this tutorial to configure a core device to interact with local IoT devices, called *client devices*, that connect to the core device over MQTT\. In this tutorial, you configure AWS IoT things to use *cloud discovery* to connect to the core device as client devices\. When you configure cloud discovery, a client device can send a request to the AWS IoT Greengrass cloud service to discover core devices\. The response from AWS IoT Greengrass includes connectivity information and certificates for the core devices that you configure the client device to discover\. Then, the client device can use this information to connect to an available core device where it can communicate over MQTT\.

In this tutorial, you do the following:

1. Review and update the core device's permissions, if needed\.

1. Associate client devices to the core device, so they can discover the core device using cloud discovery\.

1. Deploy Greengrass components to the core device to enable client device support\.

1. Connect client devices to the core device and test communication with the AWS IoT Core cloud service\.

1. Develop a custom Greengrass component that communicates with the client devices\.

1. Develop a custom component that interacts with the client devices' [AWS IoT device shadows](https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html)\.

This tutorial uses a single core device and a single client device\. You can also follow the tutorial to connect and test multiple client devices\.

You can expect to spend 30–60 minutes on this tutorial\.

## Prerequisites<a name="client-devices-tutorial-prerequisites"></a>

To complete this tutorial, you need the following:
+ An AWS account\. If you don't have one, see [Set up an AWS account](setting-up.md#set-up-aws-account)\.
+ An AWS Identity and Access Management \(IAM\) user with administrator permissions\.
+ A Greengrass core device\. For more information about how to set up a core device, see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.
  + The core device must run Greengrass nucleus v2\.6\.0 or later\. This version includes support for wildcards in local publish/subscribe communication and support for client device shadows\.
**Note**  
Client device support requires Greengrass nucleus v2\.2\.0 or later\. However, this tutorial explores newer features, such as support for MQTT wildcards in local publish/subscribe and support for client device shadows\. These features require Greengrass nucleus v2\.6\.0 or later\.
  + The core device must be on the same network as the client devices to connect\.
  + \(Optional\) To complete the modules where you develop custom Greengrass components, the core device must run the Greengrass CLI\. For more information, see [Install the Greengrass CLI](install-gg-cli.md)\.
+ An AWS IoT thing to connect as a client device in this tutorial\. For more information, see [Create AWS IoT resources](https://docs.aws.amazon.com/iot/latest/developerguide/create-iot-resources.html) in the *AWS IoT Core Developer Guide*\.
  + The client device's AWS IoT policy must allow the `greengrass:Discover` permission\. For more information, see [Minimal AWS IoT policy for client devices](device-auth.md#client-device-minimal-iot-policy)\.
  + The client device must be on the same network as the core device\.
  + The client device must run [Python 3](https://www.python.org/)\.
  + The client device must run [Git](https://git-scm.com/)\.

## Step 1: Review and update the core device AWS IoT policy<a name="update-core-device-iot-policy-client-devices"></a>

To support client devices, a core device's AWS IoT policy must allow the following permissions:
+ <a name="core-device-iot-policy-client-device-permissions-putcertificateauthorities"></a>`greengrass:PutCertificateAuthorities`
+ <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceidentity"></a>`greengrass:VerifyClientDeviceIdentity`
+ <a name="core-device-iot-policy-client-device-permissions-verifyclientdeviceiotcertificateassociation"></a>`greengrass:VerifyClientDeviceIoTCertificateAssociation`
+ <a name="core-device-iot-policy-client-device-permissions-getconnectivityinfo"></a>`greengrass:GetConnectivityInfo`
+ <a name="core-device-iot-policy-client-device-permissions-updateconnectivityinfo"></a>`greengrass:UpdateConnectivityInfo` – \(Optional\) This permission is required to use the [IP detector component](ip-detector-component.md), which reports the core device's network connectivity information to the AWS IoT Greengrass cloud service\.

For more information about these permissions and AWS IoT policies for core devices, see [AWS IoT policies for data plane operations](device-auth.md#iot-policies) and [Minimal AWS IoT policy to support client devices](device-auth.md#client-device-support-minimal-iot-policy)\.

In this section, you review the AWS IoT policies for your core device and add any required permissions that are missing\. If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), your core device has an AWS IoT policy that allows access to all AWS IoT Greengrass actions \(`greengrass:*`\)\. In this case, you must update the AWS IoT policy only if you plan to configure the shadow manager component to sync device shadows with AWS IoT Core\. Otherwise, you can skip this section\.

**To review and update a core device's AWS IoT policy**

1. <a name="update-iot-policy-console-open-greengrass-console"></a>In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Core devices**\.

1. <a name="update-iot-policy-console-choose-core-device"></a>On the **Core devices** page, choose the core device to update\.

1. <a name="update-iot-policy-console-choose-core-device-thing"></a>On the core device details page, choose the link to the core device's **Thing**\. This link opens the thing details page in the AWS IoT console\.

1. <a name="update-iot-policy-console-choose-thing-security"></a>On the thing details page, choose **Certificates**\.

1. <a name="update-iot-policy-console-choose-thing-certificate"></a>In the **Certificates** tab, choose the thing's active certificate\.

1. <a name="update-iot-policy-console-choose-certificate-policies"></a>On the certificate details page, choose **Policies**\.

1. <a name="update-iot-policy-console-choose-policy"></a>In the **Policies** tab, choose the AWS IoT policy to review and update\. You can add the required permissions to any policy that is attached to the core device's active certificate\.
**Note**  <a name="quick-installation-iot-policies-note"></a>
If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), you have two AWS IoT policies\. We recommend that you choose the policy named **GreengrassV2IoTThingPolicy**, if it exists\. Core devices that you create with the quick installer use this policy name by default\. If you add permissions to this policy, you are also granting these permissions to other core devices that use this policy\.

1. <a name="update-iot-policy-console-edit-policy"></a>In the policy overview, choose **Edit active version**\.

1. Review the policy for the required permissions, and add any required permissions that are missing\.

1. <a name="update-iot-policy-console-set-as-active-version"></a>To set a new policy version as the active version, under **Policy version status**, select **Set the edited version as the active version for this policy**\.

1. <a name="update-iot-policy-console-save-policy"></a>Choose **Save as new version**\.

## Step 2: Enable client device support<a name="enable-client-device-support"></a>

For a client device to use cloud discovery to connect to a core device, you must associate the devices\. When you associate a client device to a core device, you enable that client device to retrieve the core device's IP addresses and certificates to use to connect\.

To enable client devices to securely connect to a core device and communicate with Greengrass components and AWS IoT Core, you deploy the following Greengrass components to the core device:
+ <a name="client-device-component-overview-client-device-auth"></a>[Client device auth](client-device-auth-component.md) \(`aws.greengrass.clientdevices.Auth`\)

  Deploy the client device auth component to authenticate client devices and authorize client device actions\. This component allows your AWS IoT things to connect to a core device\.

  This component requires some configuration to use it\. You must specify groups of client devices and the operations that each group is authorized to perform, such as to connect and communicate over MQTT\. For more information, see [client device auth component configuration](client-device-auth-component.md#client-device-auth-component-configuration)\.
+ <a name="client-device-component-overview-mqtt-broker-moquette"></a>[MQTT 3\.1\.1 broker \(Moquette\)](mqtt-broker-moquette-component.md) \(`aws.greengrass.clientdevices.mqtt.Moquette`\)

  Deploy the Moquette MQTT broker component to run a lightweight MQTT broker\. The Moquette MQTT broker is compliant with MQTT 3\.1\.1 and includes local support for QoS 0, QoS 1, QoS 2, retained messages, last will messages, and persistent subscriptions\.

  You aren't required to configure this component to use it\. However, you can configure the port where this component operates the MQTT broker\. By default, it uses port 8883\.
+ <a name="client-device-component-overview-mqtt-bridge"></a>[MQTT bridge](mqtt-bridge-component.md) \(`aws.greengrass.clientdevices.mqtt.Bridge`\)

  \(Optional\) Deploy the MQTT bridge component to relay messages between client devices \(local MQTT\), local publish/subscribe, and AWS IoT Core MQTT\. Configure this component to sync client devices with AWS IoT Core and interact with client devices from Greengrass components\.

  This component requires configuration to use\. You must specify the topic mappings where this component relays messages\. For more information, see [MQTT bridge component configuration](mqtt-bridge-component.md#mqtt-bridge-component-configuration)\.
+ <a name="client-device-component-overview-ip-detector"></a>[IP detector](ip-detector-component.md) \(`aws.greengrass.clientdevices.IPDetector`\)

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

   1. The core device must run [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later to complete this tutorial\. If the core device runs an earlier version, do the following:

      1. Select the box to deploy the **aws\.greengrass\.Nucleus** component\.

      1. For the **aws\.greengrass\.Nucleus** component, choose **Edit configuration**\.

      1. For **Component version**, choose version 2\.6\.0 or later\.

      1. Choose **Confirm**\.
**Note**  
If you upgrade the Greengrass nucleus from an earlier minor version, and the core device runs [AWS\-provided components](public-components.md) that depend on the nucleus, you must also update the AWS\-provided components to newer versions\. You can configure the version of these components when you review the deployment later in this tutorial\. For more information, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.

   1. For the **aws\.greengrass\.clientdevices\.Auth** component, choose **Edit configuration**\.

   1. In the **Edit configuration** modal for the client device auth component, configure an authorization policy that allows client devices to publish and subscribe to the MQTT broker on the core device\. Do the following:

      1. Under **Configuration**, in the **Configuration to merge** code block, enter the following configuration, which contains a *client device authorization policy*\. Each device group authorization policy specifies a set of actions and the resources on which a client device can perform those actions\.
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

1. If you haven't previously set up the [Greengrass service role](greengrass-service-role.md) in this Region, the console opens a modal to set up the service role for you\. The client device auth component uses this service role to verify the identity of client devices, and the IP detector component uses this service role to manage core device connectivity information\. Choose **Grant permissions**\.

1. <a name="choose-deploy-configure-discovery-step"></a>On the **Review** page, choose **Deploy** to start the deployment to the core device\.

1. <a name="verify-deployment-success-step"></a>To verify that the deployment succeeds, check the status of the deployment, and check the logs on the core device\. To check the status of the deployment on the core device, you can choose **Target** in the deployment **Overview**\. For more information, see the following:
   + [Check deployment status](check-deployment-status.md)
   + [Monitor AWS IoT Greengrass logs](monitor-logs.md)

## Step 3: Connect client devices<a name="connect-client-devices-tutorial"></a>

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
This topic matches the topic where you configured the MQTT bridge to relay messages to AWS IoT Core earlier\.<a name="test-client-device-communications-application-command-replace"></a>
   + Replace *MyClientDevice1* with the client device's thing name\.
   + Replace *\~/certs/AmazonRootCA1\.pem* with the path to the Amazon root CA certificate on the client device\.
   + Replace *\~/certs/device\.pem\.crt* with the path to the device certificate on the client device\.
   + Replace *\~/certs/private\.pem\.key* with the path to the private key file on the client device\.
   + Replace *us\-east\-1* with the AWS Region where your client device and core device operate\.

   ```
   python3 basic_discovery.py \
     --thing_name MyClientDevice1 \
     --topic 'clients/MyClientDevice1/hello/world' \
     --message 'Hello World!' \
     --ca_file ~/certs/AmazonRootCA1.pem \
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

   <a name="test-client-device-communications-application-view-core-logs"></a>You can also view the Greengrass logs on the core device to verify if the client device successfully connects and sends messages\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

1. Verify that the MQTT bridge relays the messages from the client device to AWS IoT Core\. You can use the MQTT test client in the AWS IoT Core console to subscribe to an MQTT topic filter\. Do the following:

   1. Navigate to the [AWS IoT console](https://console.aws.amazon.com/iot)\.

   1. In the left navigation menu, under **Test**, choose **MQTT test client**\.

   1. On the **Subscribe to a topic** tab, for **Topic filter**, enter `clients/+/hello/world` to subscribe to client device messages from the core device\.

   1. Choose **Subscribe**\.

   1. Run the publish/subscribe application on the client device again\.

      The MQTT test client displays the messages that you send from the client device on topics that match this topic filter\.

## Step 4: Develop a component that communicates with client devices<a name="develop-client-device-subscriber-component"></a>

You can develop Greengrass components that communicate with client devices\. Components use [interprocess communication \(IPC\)](interprocess-communication.md) and the [local publish/subscribe interface](ipc-publish-subscribe.md) to communicate on a core device\. To interact with client devices, configure the MQTT bridge component to relay messages between client devices and the local publish/subscribe interface\.

In this section, you update the MQTT bridge component to relay messages from client devices to the local publish/subscribe interface\. Then, you develop a component that subscribes to these messages and prints the messages when it receives them\.

**To develop a component that communicates with client devices**

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
      + [Monitor AWS IoT Greengrass logs](monitor-logs.md)

1. Develop and deploy a Greengrass component that subscribes to Hello World messages from client devices\. Do the following:

   1. Create folders for recipes and artifacts on the core device\.

------
#### [ Linux or Unix ]

      ```
      mkdir recipes
      mkdir -p artifacts/com.example.clientdevices.MyHelloWorldSubscriber/1.0.0
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      mkdir recipes
      mkdir artifacts\com.example.clientdevices.MyHelloWorldSubscriber\1.0.0
      ```

------
#### [ PowerShell ]

      ```
      mkdir recipes
      mkdir artifacts\com.example.clientdevices.MyHelloWorldSubscriber\1.0.0
      ```

------
**Important**  <a name="local-artifact-folder-name-requirements"></a>
You must use the following format for the artifact folder path\. Include the component name and version that you specify in the recipe\.  

      ```
      artifacts/componentName/componentVersion/
      ```

   1. Use a text editor to create a component recipe with the following contents\. This recipe specifies to install the AWS IoT Device SDK v2 for Python and run a script that subscribes to the topic and prints messages\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano recipes/com.example.clientdevices.MyHelloWorldSubscriber-1.0.0.json
      ```

      Copy the following recipe into the file\.

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
            "Platform": {
              "os": "linux"
            },
            "Lifecycle": {
              "Install": "python3 -m pip install --user awsiotsdk",
              "Run": "python3 -u {artifacts:path}/hello_world_subscriber.py"
            }
          },
          {
            "Platform": {
              "os": "windows"
            },
            "Lifecycle": {
              "Install": "py -3 -m pip install --user awsiotsdk",
              "Run": "py -3 -u {artifacts:path}/hello_world_subscriber.py"
            }
          }
        ]
      }
      ```

   1. Use a text editor to create a Python script artifact named `hello_world_subscriber.py` with the following contents\. This application uses the publish/subscribe IPC service to subscribe to the `clients/+/hello/world` topic and print messages that it receives\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano artifacts/com.example.clientdevices.MyHelloWorldSubscriber/1.0.0/hello_world_subscriber.py
      ```

      Copy the following Python code into the file\.

      ```
      import sys
      import time
      import traceback
      
      from awsiot.greengrasscoreipc.clientv2 import GreengrassCoreIPCClientV2
      
      CLIENT_DEVICE_HELLO_WORLD_TOPIC = 'clients/+/hello/world'
      TIMEOUT = 10
      
      
      def on_hello_world_message(event):
          try:
              message = str(event.binary_message.message, 'utf-8')
              print('Received new message: %s' % message)
          except:
              traceback.print_exc()
      
      
      try:
          ipc_client = GreengrassCoreIPCClientV2()
      
          # SubscribeToTopic returns a tuple with the response and the operation.
          _, operation = ipc_client.subscribe_to_topic(
              topic=CLIENT_DEVICE_HELLO_WORLD_TOPIC, on_stream_event=on_hello_world_message)
          print('Successfully subscribed to topic: %s' %
                CLIENT_DEVICE_HELLO_WORLD_TOPIC)
      
          # Keep the main thread alive, or the process will exit.
          try:
              while True:
                  time.sleep(10)
          except InterruptedError:
              print('Subscribe interrupted.')
      
          operation.close()
      except Exception:
          print('Exception occurred when using IPC.', file=sys.stderr)
          traceback.print_exc()
          exit(1)
      ```
**Note**  
This component uses the IPC client V2 in the [AWS IoT Device SDK v2 for Python](https://github.com/aws/aws-iot-device-sdk-python-v2) to communicate with the AWS IoT Greengrass Core software\. Compared to the original IPC client, the IPC client V2 reduces the amount of code that you need to write to use IPC in custom components\.

   1. Use the Greengrass CLI to deploy the component\.

------
#### [ Linux or Unix ]

      ```
      sudo /greengrass/v2/bin/greengrass-cli deployment create \
        --recipeDir recipes \
        --artifactDir artifacts \
        --merge "com.example.clientdevices.MyHelloWorldSubscriber=1.0.0"
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      C:\greengrass\v2/bin/greengrass-cli deployment create ^
        --recipeDir recipes ^
        --artifactDir artifacts ^
        --merge "com.example.clientdevices.MyHelloWorldSubscriber=1.0.0"
      ```

------
#### [ PowerShell ]

      ```
      C:\greengrass\v2/bin/greengrass-cli deployment create `
        --recipeDir recipes `
        --artifactDir artifacts `
        --merge "com.example.clientdevices.MyHelloWorldSubscriber=1.0.0"
      ```

------

1. View the component logs to verify that the component installs successfully and subscribes to the topic\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.clientdevices.MyHelloWorldSubscriber.log
   ```

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2/logs/com.example.clientdevices.MyHelloWorldSubscriber.log -Tail 10 -Wait
   ```

------

   You can keep the log feed open to verify that the core device receives messages\.

1. On the client device, run the sample Greengrass discovery application again to send messages to the core device\.

   ```
   python3 basic_discovery.py \
     --thing_name MyClientDevice1 \
     --topic 'clients/MyClientDevice1/hello/world' \
     --message 'Hello World!' \
     --ca_file ~/certs/AmazonRootCA1.pem \
     --cert ~/certs/device.pem.crt \
     --key ~/certs/private.pem.key \
     --region us-east-1 \
     --verbosity Warn
   ```

1. View the component logs again to verify that the component receives and prints the messages from the client device\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.clientdevices.MyHelloWorldSubscriber.log
   ```

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2/logs/com.example.clientdevices.MyHelloWorldSubscriber.log -Tail 10 -Wait
   ```

------

## Step 5: Develop a component that interacts with client device shadows<a name="develop-client-device-shadow-component"></a>

You can develop Greengrass components that interact with client device's [AWS IoT device shadows](https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html)\. A *shadow* is a JSON document that stores the current or desired state information for an AWS IoT thing, such as a client device\. Custom components can access client devices' shadows to manage their state, even when the client device isn't connected to AWS IoT\. Each AWS IoT thing has an unnamed shadow, and you can also create multiple named shadows for each thing\.

In this section, you deploy the [shadow manager component](shadow-manager-component.md) to manage shadows on the core device\. You also update the MQTT bridge component to relay shadow messages between client devices and the shadow manager component\. Then, you develop a component that updates the client devices' shadows, and you run a sample application on the client devices that responds to shadow updates from the component\. This component represents a smart light management application, where the core device manages the color state of smart lights that connect to it as client devices\.

**To develop a component that interacts with client device shadows**

1. Revise the deployment to the core device to deploy the shadow manager component and configure the MQTT bridge component to relay shadow messages between client devices and local publish/subscribe, where shadow manager communicates\. Do the following:

   1. <a name="navigate-greengrass-console"></a>Navigate to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

   1. In the left navigation menu, choose **Core devices**\.

   1. On the **Core devices** page, choose the core device that you are using for this tutorial\.

   1. On the core device details page, choose the **Client devices** tab\.

   1. On the **Client devices** tab, choose **Configure cloud discovery**\.

      The **Configure core device discovery** page opens\. On this page, you can change or configure which client device components deploy to the core device\.

   1. In **Step 3**, for the **aws\.greengrass\.clientdevices\.mqtt\.Bridge** component, choose **Edit configuration**\.

   1. In the **Edit configuration** modal for the MQTT bridge component, configure a topic mapping that relays MQTT messages on [device shadow topics](https://docs.aws.amazon.com/iot/latest/developerguide/reserved-topics.html#reserved-topics-shadow) between client devices and the local publish/subscribe interface\. You also confirm that the deployment specifies a compatible MQTT bridge version\. Client device shadow support requires MQTT bridge v2\.2\.0 or later\. Do the following:

      1. For **Component version**, choose version 2\.2\.0 or later\.

      1. Under **Configuration**, in the **Configuration to merge** code block, enter the following configuration\. This configuration specifies to relays MQTT messages on shadow topics\.

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
             },
             "ShadowsLocalMqttToPubsub": {
               "topic": "$aws/things/+/shadow/#",
               "source": "LocalMqtt",
               "target": "Pubsub"
             },
             "ShadowsPubsubToLocalMqtt": {
               "topic": "$aws/things/+/shadow/#",
               "source": "Pubsub",
               "target": "LocalMqtt"
             }
           }
         }
         ```

         For more information, see [MQTT bridge component configuration](mqtt-bridge-component.md#mqtt-bridge-component-configuration)\.

      1. Choose **Confirm**\.

   1. In **Step 3**, select the **aws\.greengrass\.ShadowManager** component to deploy it\.

   1. <a name="choose-review-and-deploy-configure-discovery-step"></a>Choose **Review and deploy** to review the deployment that this page creates for you\.

   1. <a name="choose-deploy-configure-discovery-step"></a>On the **Review** page, choose **Deploy** to start the deployment to the core device\.

   1. <a name="verify-deployment-success-step"></a>To verify that the deployment succeeds, check the status of the deployment, and check the logs on the core device\. To check the status of the deployment on the core device, you can choose **Target** in the deployment **Overview**\. For more information, see the following:
      + [Check deployment status](check-deployment-status.md)
      + [Monitor AWS IoT Greengrass logs](monitor-logs.md)

1. Develop and deploy a Greengrass component that manage smart light client devices\. Do the following:

   1. Create a folder the component's artifacts on the core device\.

------
#### [ Linux or Unix ]

      ```
      mkdir -p artifacts/com.example.clientdevices.MyHelloWorldSubscriber/1.0.0
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      mkdir artifacts\com.example.clientdevices.MyHelloWorldSubscriber\1.0.0
      ```

------
#### [ PowerShell ]

      ```
      mkdir artifacts\com.example.clientdevices.MyHelloWorldSubscriber\1.0.0
      ```

------
**Important**  <a name="local-artifact-folder-name-requirements"></a>
You must use the following format for the artifact folder path\. Include the component name and version that you specify in the recipe\.  

      ```
      artifacts/componentName/componentVersion/
      ```

   1. Use a text editor to create a component recipe with the following contents\. This recipe specifies to install the AWS IoT Device SDK v2 for Python and run a script that interacts with smart light client devices' shadows to manage their colors\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano recipes/com.example.clientdevices.MySmartLightManager-1.0.0.json
      ```

      Copy the following recipe into the file\.

      ```
      {
        "RecipeFormatVersion": "2020-01-25",
        "ComponentName": "com.example.clientdevices.MySmartLightManager",
        "ComponentVersion": "1.0.0",
        "ComponentDescription": "A component that interacts with smart light client devices.",
        "ComponentPublisher": "Amazon",
        "ComponentDependencies": {
          "aws.greengrass.Nucleus": {
            "VersionRequirement": "^2.6.0"
          },
          "aws.greengrass.ShadowManager": {
            "VersionRequirement": "^2.2.0"
          },
          "aws.greengrass.clientdevices.mqtt.Bridge": {
            "VersionRequirement": "^2.2.0"
          }
        },
        "ComponentConfiguration": {
          "DefaultConfiguration": {
            "smartLightDeviceNames": [],
            "accessControl": {
              "aws.greengrass.ShadowManager": {
                "com.example.clientdevices.MySmartLightManager:shadow:1": {
                  "policyDescription": "Allows access to client devices' unnamed shadows",
                  "operations": [
                    "aws.greengrass#GetThingShadow",
                    "aws.greengrass#UpdateThingShadow"
                  ],
                  "resources": [
                    "$aws/things/MyClientDevice*/shadow"
                  ]
                }
              },
              "aws.greengrass.ipc.pubsub": {
                "com.example.clientdevices.MySmartLightManager:pubsub:1": {
                  "policyDescription": "Allows access to client devices' unnamed shadow updates",
                  "operations": [
                    "aws.greengrass#SubscribeToTopic"
                  ],
                  "resources": [
                    "$aws/things/+/shadow/update/accepted"
                  ]
                }
              }
            }
          }
        },
        "Manifests": [
          {
            "Platform": {
              "os": "linux"
            },
            "Lifecycle": {
              "Install": "python3 -m pip install --user awsiotsdk",
              "Run": "python3 -u {artifacts:path}/smart_light_manager.py"
            }
          },
          {
            "Platform": {
              "os": "windows"
            },
            "Lifecycle": {
              "Install": "py -3 -m pip install --user awsiotsdk",
              "Run": "py -3 -u {artifacts:path}/smart_light_manager.py"
            }
          }
        ]
      }
      ```

   1. Use a text editor to create a Python script artifact named `smart_light_manager.py` with the following contents\. This application uses the shadow IPC service to get and update client device shadows and the local publish/subscribe IPC service to receive reported shadow updates\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano artifacts/com.example.clientdevices.MySmartLightManager/1.0.0/smart_light_manager.py
      ```

      Copy the following Python code into the file\.

      ```
      import json
      import random
      import sys
      import time
      import traceback
      from uuid import uuid4
      
      from awsiot.greengrasscoreipc.clientv2 import GreengrassCoreIPCClientV2
      from awsiot.greengrasscoreipc.model import ResourceNotFoundError
      
      SHADOW_COLOR_PROPERTY = 'color'
      CONFIGURATION_CLIENT_DEVICE_NAMES = 'smartLightDeviceNames'
      COLORS = ['red', 'orange', 'yellow', 'green', 'blue', 'purple']
      SHADOW_UPDATE_TOPIC = '$aws/things/+/shadow/update/accepted'
      SET_COLOR_INTERVAL = 15
      
      
      class SmartLightDevice():
          def __init__(self, client_device_name: str, reported_color: str = None):
              self.name = client_device_name
              self.reported_color = reported_color
              self.desired_color = None
      
      
      class SmartLightDeviceManager():
          def __init__(self, ipc_client: GreengrassCoreIPCClientV2):
              self.ipc_client = ipc_client
              self.devices = {}
              self.client_tokens = set()
              self.shadow_update_accepted_subscription_operation = None
              self.client_device_names_configuration_subscription_operation = None
              self.update_smart_light_device_list()
      
          def update_smart_light_device_list(self):
              # Update the device list from the component configuration.
              response = self.ipc_client.get_configuration(
                  key_path=[CONFIGURATION_CLIENT_DEVICE_NAMES])
              # Identify the difference between the configuration and the currently tracked devices.
              current_device_names = self.devices.keys()
              updated_device_names = response.value[CONFIGURATION_CLIENT_DEVICE_NAMES]
              added_device_names = set(updated_device_names) - set(current_device_names)
              removed_device_names = set(current_device_names) - set(updated_device_names)
              # Stop tracking any smart light devices that are no longer in the configuration.
              for name in removed_device_names:
                  print('Removing %s from smart light device manager' % name)
                  self.devices.pop(name)
              # Start tracking any new smart light devices that are in the configuration.
              for name in added_device_names:
                  print('Adding %s to smart light device manager' % name)
                  device = SmartLightDevice(name)
                  device.reported_color = self.get_device_reported_color(device)
                  self.devices[name] = device
                  print('Current color for %s is %s' % (name, device.reported_color))
      
          def get_device_reported_color(self, smart_light_device):
              try:
                  response = self.ipc_client.get_thing_shadow(
                      thing_name=smart_light_device.name, shadow_name='')
                  shadow = json.loads(str(response.payload, 'utf-8'))
                  if 'reported' in shadow['state']:
                      return shadow['state']['reported'].get(SHADOW_COLOR_PROPERTY)
                  return None
              except ResourceNotFoundError:
                  return None
      
          def request_device_color_change(self, smart_light_device, color):
              # Generate and track a client token for the request.
              client_token = str(uuid4())
              self.client_tokens.add(client_token)
              # Create a shadow payload, which must be a blob.
              payload_json = {
                  'state': {
                      'desired': {
                          SHADOW_COLOR_PROPERTY: color
                      }
                  },
                  'clientToken': client_token
              }
              payload = bytes(json.dumps(payload_json), 'utf-8')
              self.ipc_client.update_thing_shadow(
                  thing_name=smart_light_device.name, shadow_name='', payload=payload)
              smart_light_device.desired_color = color
      
          def subscribe_to_shadow_update_accepted_events(self):
              if self.shadow_update_accepted_subscription_operation == None:
                  # SubscribeToTopic returns a tuple with the response and the operation.
                  _, self.shadow_update_accepted_subscription_operation = self.ipc_client.subscribe_to_topic(
                      topic=SHADOW_UPDATE_TOPIC, on_stream_event=self.on_shadow_update_accepted_event)
                  print('Successfully subscribed to shadow update accepted topic')
      
          def close_shadow_update_accepted_subscription(self):
              if self.shadow_update_accepted_subscription_operation is not None:
                  self.shadow_update_accepted_subscription_operation.close()
      
          def on_shadow_update_accepted_event(self, event):
              try:
                  message = str(event.binary_message.message, 'utf-8')
                  accepted_payload = json.loads(message)
                  # Check for reported states from smart light devices and ignore desired states from components.
                  if 'reported' in accepted_payload['state']:
                      # Process this update only if it uses a client token created by this component.
                      client_token = accepted_payload.get('clientToken')
                      if client_token is not None and client_token in self.client_tokens:
                          self.client_tokens.remove(client_token)
                          shadow_state = accepted_payload['state']['reported']
                          if SHADOW_COLOR_PROPERTY in shadow_state:
                              reported_color = shadow_state[SHADOW_COLOR_PROPERTY]
                              topic = event.binary_message.context.topic
                              client_device_name = topic.split('/')[2]
                              if client_device_name in self.devices:
                                  # Set the reported color for the smart light device.
                                  self.devices[client_device_name].reported_color = reported_color
                                  print(
                                      'Received shadow update confirmation from client device: %s' % client_device_name)
                          else:
                              print("Shadow update doesn't specify color")
              except:
                  traceback.print_exc()
      
          def subscribe_to_client_device_name_configuration_updates(self):
              if self.client_device_names_configuration_subscription_operation == None:
                  # SubscribeToConfigurationUpdate returns a tuple with the response and the operation.
                  _, self.client_device_names_configuration_subscription_operation = self.ipc_client.subscribe_to_configuration_update(
                      key_path=[CONFIGURATION_CLIENT_DEVICE_NAMES], on_stream_event=self.on_client_device_names_configuration_update_event)
                  print(
                      'Successfully subscribed to configuration updates for smart light device names')
      
          def close_client_device_names_configuration_subscription(self):
              if self.client_device_names_configuration_subscription_operation is not None:
                  self.client_device_names_configuration_subscription_operation.close()
      
          def on_client_device_names_configuration_update_event(self, event):
              try:
                  if CONFIGURATION_CLIENT_DEVICE_NAMES in event.configuration_update_event.key_path:
                      print('Received configuration update for list of client devices')
                      self.update_smart_light_device_list()
              except:
                  traceback.print_exc()
      
      
      def choose_random_color():
          return random.choice(COLORS)
      
      def main():
          try:
              # Create an IPC client and a smart light device manager.
              ipc_client = GreengrassCoreIPCClientV2()
              smart_light_manager = SmartLightDeviceManager(ipc_client)
              smart_light_manager.subscribe_to_shadow_update_accepted_events()
              smart_light_manager.subscribe_to_client_device_name_configuration_updates()
              try:
                  # Keep the main thread alive, or the process will exit.
                  while True:
                      # Set each smart light device to a random color at a regular interval.
                      for device_name in smart_light_manager.devices:
                          device = smart_light_manager.devices[device_name]
                          desired_color = choose_random_color()
                          print('Chose random color (%s) for %s' %
                                (desired_color, device_name))
                          if desired_color == device.desired_color:
                              print('Desired color for %s is already %s' %
                                    (device_name, desired_color))
                          elif desired_color == device.reported_color:
                              print('Reported color for %s is already %s' %
                                    (device_name, desired_color))
                          else:
                              smart_light_manager.request_device_color_change(
                                  device, desired_color)
                              print('Requested color change for %s to %s' %
                                    (device_name, desired_color))
                      time.sleep(SET_COLOR_INTERVAL)
              except InterruptedError:
                  print('Application interrupted')
              smart_light_manager.close_shadow_update_accepted_subscription()
              smart_light_manager.close_client_device_names_configuration_subscription()
          except Exception:
              print('Exception occurred', file=sys.stderr)
              traceback.print_exc()
              exit(1)
      
      
      if __name__ == '__main__':
          main()
      ```

      This Python application does the following:
      + Reads the component's configuration to get the list of smart light client devices to manage\.
      + Subscribes to configuration update notifications using the [SubscribeToConfigurationUpdate](ipc-component-configuration.md#ipc-operation-subscribetoconfigurationupdate) IPC operation\. The AWS IoT Greengrass Core software sends notifications each time the component's configuration changes\. When the component receives a configuration update notification, it updates the list of smart light client devices that it manages\.
      + Gets each smart light client device's shadow to get its initial color state\.
      + Sets each smart light client device's color to a random color every 15 seconds\. The component updates the client device's thing shadow to change its color\. This operation sends a shadow delta event to the client device over MQTT\.
      + Subscribes to shadow update accepted messages on the local publish/subscribe interface using the [SubscribeToTopic](ipc-publish-subscribe.md#ipc-operation-subscribetotopic) IPC operation\. This component receives these messages to track the color of each smart light client device\. When a smart light client device receives a shadow update, it sends an MQTT message to confirm that it received the update\. The MQTT bridge relays this message to the local publish/subscribe interface\.

   1. Use the Greengrass CLI to deploy the component\. When you deploy this component, you specify the list of client devices, `smartLightDeviceNames`, whose shadows it manages\. Replace *MyClientDevice1* with the client device's thing name\.

------
#### [ Linux or Unix ]

      ```
      sudo /greengrass/v2/bin/greengrass-cli deployment create \
        --recipeDir recipes \
        --artifactDir artifacts \
        --merge "com.example.clientdevices.MySmartLightManager=1.0.0" \
        --update-config '{
          "com.example.clientdevices.MySmartLightManager": {
            "MERGE": {
              "smartLightDeviceNames": [
                "MyClientDevice1"
              ]
            }
          }
        }'
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      C:\greengrass\v2/bin/greengrass-cli deployment create ^
        --recipeDir recipes ^
        --artifactDir artifacts ^
        --merge "com.example.clientdevices.MySmartLightManager=1.0.0" ^
        --update-config '{"com.example.clientdevices.MySmartLightManager":{"MERGE":{"smartLightDeviceNames":["MyClientDevice1"]}}}'
      ```

------
#### [ PowerShell ]

      ```
      C:\greengrass\v2/bin/greengrass-cli deployment create `
        --recipeDir recipes `
        --artifactDir artifacts `
        --merge "com.example.clientdevices.MySmartLightManager=1.0.0" `
        --update-config '{
          "com.example.clientdevices.MySmartLightManager": {
            "MERGE": {
              "smartLightDeviceNames": [
                "MyClientDevice1"
              ]
            }
          }
        }'
      ```

------

1. View the component logs to verify that the component installs and runs successfully\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.clientdevices.MySmartLightManager.log
   ```

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2/logs/com.example.clientdevices.MySmartLightManager.log -Tail 10 -Wait
   ```

------

   The component sends requests to change the color of the smart light client device\. The shadow manager receives the request and sets the shadow's `desired` state\. However, the smart light client device isn't running yet, so the shadow's `reported` state doesn't change\. The component's logs include the following messages\.

   ```
   2022-07-07T03:49:24.908Z [INFO] (Copier) com.example.clientdevices.MySmartLightManager: stdout. Chose random color (blue) for MyClientDevice1. {scriptName=services.com.example.clientdevices.MySmartLightManager.lifecycle.Run, serviceName=com.example.clientdevices.MySmartLightManager, currentState=RUNNING}
   2022-07-07T03:49:24.912Z [INFO] (Copier) com.example.clientdevices.MySmartLightManager: stdout. Requested color change for MyClientDevice1 to blue. {scriptName=services.com.example.clientdevices.MySmartLightManager.lifecycle.Run, serviceName=com.example.clientdevices.MySmartLightManager, currentState=RUNNING}
   ```

   You can keep the log feed open to see when the component prints messages\.

1. Download and run a sample application that uses Greengrass discovery and subscribes to device shadow updates\. On the client device, do the following:

   1. Change to the samples folder in the AWS IoT Device SDK v2 for Python\. This sample application uses a command line parsing module in the samples folder\.

      ```
      cd aws-iot-device-sdk-python-v2/samples
      ```

   1. Use a text editor to create a Python script named `basic_discovery_shadow.py` with the following contents\. This application uses Greengrass discovery and shadows to keep a property in sync between the client device and the core device\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano basic_discovery_shadow.py
      ```

      Copy the following Python code into the file\.

      ```
      # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
      # SPDX-License-Identifier: Apache-2.0.
      
      from awscrt import io
      from awscrt import mqtt
      from awsiot import iotshadow
      from awsiot.greengrass_discovery import DiscoveryClient
      from awsiot import mqtt_connection_builder
      from concurrent.futures import Future
      import sys
      import threading
      import traceback
      from uuid import uuid4
      
      # Parse arguments
      import command_line_utils;
      cmdUtils = command_line_utils.CommandLineUtils("Basic Discovery - Greengrass discovery example with device shadows.")
      cmdUtils.add_common_mqtt_commands()
      cmdUtils.add_common_topic_message_commands()
      cmdUtils.add_common_logging_commands()
      cmdUtils.register_command("key", "<path>", "Path to your key in PEM format.", True, str)
      cmdUtils.register_command("cert", "<path>", "Path to your client certificate in PEM format.", True, str)
      cmdUtils.remove_command("endpoint")
      cmdUtils.register_command("thing_name", "<str>", "The name assigned to your IoT Thing", required=True)
      cmdUtils.register_command("region", "<str>", "The region to connect through.", required=True)
      cmdUtils.register_command("shadow_property", "<str>", "The name of the shadow property you want to change (optional, default='color'", default="color")
      # Needs to be called so the command utils parse the commands
      cmdUtils.get_args()
      
      # Using globals to simplify sample code
      is_sample_done = threading.Event()
      mqtt_connection = None
      shadow_thing_name = cmdUtils.get_command_required("thing_name")
      shadow_property = cmdUtils.get_command("shadow_property")
      
      SHADOW_VALUE_DEFAULT = "off"
      
      class LockedData:
          def __init__(self):
              self.lock = threading.Lock()
              self.shadow_value = None
              self.disconnect_called = False
              self.request_tokens = set()
      
      locked_data = LockedData()
      
      
      def on_connection_interupted(connection, error, **kwargs):
          print('connection interrupted with error {}'.format(error))
      
      
      def on_connection_resumed(connection, return_code, session_present, **kwargs):
          print('connection resumed with return code {}, session present {}'.format(return_code, session_present))
      
      # Try IoT endpoints until we find one that works
      def try_iot_endpoints():
          for gg_group in discover_response.gg_groups:
              for gg_core in gg_group.cores:
                  for connectivity_info in gg_core.connectivity:
                      try:
                          print('Trying core {} at host {} port {}'.format(gg_core.thing_arn, connectivity_info.host_address, connectivity_info.port))
                          mqtt_connection = mqtt_connection_builder.mtls_from_path(
                              endpoint=connectivity_info.host_address,
                              port=connectivity_info.port,
                              cert_filepath=cmdUtils.get_command_required("cert"),
                              pri_key_filepath=cmdUtils.get_command_required("key"),
                              ca_bytes=gg_group.certificate_authorities[0].encode('utf-8'),
                              on_connection_interrupted=on_connection_interupted,
                              on_connection_resumed=on_connection_resumed,
                              client_id=cmdUtils.get_command_required("thing_name"),
                              clean_session=False,
                              keep_alive_secs=30)
      
                          connect_future = mqtt_connection.connect()
                          connect_future.result()
                          print('Connected!')
                          return mqtt_connection
      
                      except Exception as e:
                          print('Connection failed with exception {}'.format(e))
                          continue
      
          exit('All connection attempts failed')
      
      # Function for gracefully quitting this sample
      def exit(msg_or_exception):
          if isinstance(msg_or_exception, Exception):
              print("Exiting sample due to exception.")
              traceback.print_exception(msg_or_exception.__class__, msg_or_exception, sys.exc_info()[2])
          else:
              print("Exiting sample:", msg_or_exception)
      
          with locked_data.lock:
              if not locked_data.disconnect_called:
                  print("Disconnecting...")
                  locked_data.disconnect_called = True
                  future = mqtt_connection.disconnect()
                  future.add_done_callback(on_disconnected)
      
      def on_disconnected(disconnect_future):
          # type: (Future) -> None
          print("Disconnected.")
      
          # Signal that sample is finished
          is_sample_done.set()
      
      def on_get_shadow_accepted(response):
          # type: (iotshadow.GetShadowResponse) -> None
          try:
              with locked_data.lock:
                  # check that this is a response to a request from this session
                  try:
                      locked_data.request_tokens.remove(response.client_token)
                  except KeyError:
                      return
      
                  print("Finished getting initial shadow state.")
                  if locked_data.shadow_value is not None:
                      print("  Ignoring initial query because a delta event has already been received.")
                      return
      
              if response.state:
                  if response.state.delta:
                      value = response.state.delta.get(shadow_property)
                      if value:
                          print("  Shadow contains delta value '{}'.".format(value))
                          change_shadow_value(value)
                          return
      
                  if response.state.reported:
                      value = response.state.reported.get(shadow_property)
                      if value:
                          print("  Shadow contains reported value '{}'.".format(value))
                          set_local_value_due_to_initial_query(response.state.reported[shadow_property])
                          return
      
              print("  Shadow document lacks '{}' property. Setting defaults...".format(shadow_property))
              change_shadow_value(SHADOW_VALUE_DEFAULT)
              return
      
          except Exception as e:
              exit(e)
      
      def on_get_shadow_rejected(error):
          # type: (iotshadow.ErrorResponse) -> None
          try:
              # check that this is a response to a request from this session
              with locked_data.lock:
                  try:
                      locked_data.request_tokens.remove(error.client_token)
                  except KeyError:
                      return
      
              if error.code == 404:
                  print("Thing has no shadow document. Creating with defaults...")
                  change_shadow_value(SHADOW_VALUE_DEFAULT)
              else:
                  exit("Get request was rejected. code:{} message:'{}'".format(
                      error.code, error.message))
      
          except Exception as e:
              exit(e)
      
      def on_shadow_delta_updated(delta):
          # type: (iotshadow.ShadowDeltaUpdatedEvent) -> None
          try:
              print("Received shadow delta event.")
              if delta.state and (shadow_property in delta.state):
                  value = delta.state[shadow_property]
                  if value is None:
                      print("  Delta reports that '{}' was deleted. Resetting defaults...".format(shadow_property))
                      change_shadow_value(SHADOW_VALUE_DEFAULT)
                      return
                  else:
                      print("  Delta reports that desired value is '{}'. Changing local value...".format(value))
                      if (delta.client_token is not None):
                          print ("  ClientToken is: " + delta.client_token)
                      change_shadow_value(value, delta.client_token)
              else:
                  print("  Delta did not report a change in '{}'".format(shadow_property))
      
          except Exception as e:
              exit(e)
      
      def on_publish_update_shadow(future):
          #type: (Future) -> None
          try:
              future.result()
              print("Update request published.")
          except Exception as e:
              print("Failed to publish update request.")
              exit(e)
      
      def on_update_shadow_accepted(response):
          # type: (iotshadow.UpdateShadowResponse) -> None
          try:
              # check that this is a response to a request from this session
              with locked_data.lock:
                  try:
                      locked_data.request_tokens.remove(response.client_token)
                  except KeyError:
                      return
      
              try:
                  if response.state.reported != None:
                      if shadow_property in response.state.reported:
                          print("Finished updating reported shadow value to '{}'.".format(response.state.reported[shadow_property])) # type: ignore
                      else:
                          print ("Could not find shadow property with name: '{}'.".format(shadow_property)) # type: ignore
                  else:
                      print("Shadow states cleared.") # when the shadow states are cleared, reported and desired are set to None
              except:
                  exit("Updated shadow is missing the target property")
      
          except Exception as e:
              exit(e)
      
      def on_update_shadow_rejected(error):
          # type: (iotshadow.ErrorResponse) -> None
          try:
              # check that this is a response to a request from this session
              with locked_data.lock:
                  try:
                      locked_data.request_tokens.remove(error.client_token)
                  except KeyError:
                      return
      
              exit("Update request was rejected. code:{} message:'{}'".format(
                  error.code, error.message))
      
          except Exception as e:
              exit(e)
      
      def set_local_value_due_to_initial_query(reported_value):
          with locked_data.lock:
              locked_data.shadow_value = reported_value
      
      def change_shadow_value(value, token=None):
          with locked_data.lock:
              if locked_data.shadow_value == value:
                  print("Local value is already '{}'.".format(value))
                  return
      
              print("Changed local shadow value to '{}'.".format(value))
              locked_data.shadow_value = value
      
              print("Updating reported shadow value to '{}'...".format(value))
      
              reuse_token = token is not None
              # use a unique token so we can correlate this "request" message to
              # any "response" messages received on the /accepted and /rejected topics
              if not reuse_token:
                  token = str(uuid4())
      
              # if the value is "clear shadow" then send a UpdateShadowRequest with None
              # for both reported and desired to clear the shadow document completely.
              if value == "clear_shadow":
                  tmp_state = iotshadow.ShadowState(reported=None, desired=None, reported_is_nullable=True, desired_is_nullable=True)
                  request = iotshadow.UpdateShadowRequest(
                      thing_name=shadow_thing_name,
                      state=tmp_state,
                      client_token=token,
                  )
              # Otherwise, send a normal update request
              else:
                  # if the value is "none" then set it to a Python none object to
                  # clear the individual shadow property
                  if value == "none":
                      value = None
      
                  request = iotshadow.UpdateShadowRequest(
                  thing_name=shadow_thing_name,
                  state=iotshadow.ShadowState(
                      reported={ shadow_property: value }
                      ),
                      client_token=token,
                  )
      
              future = shadow_client.publish_update_shadow(request, mqtt.QoS.AT_LEAST_ONCE)
      
              if not reuse_token:
                  locked_data.request_tokens.add(token)
      
              future.add_done_callback(on_publish_update_shadow)
      
      
      if __name__ == '__main__':
          tls_options = io.TlsContextOptions.create_client_with_mtls_from_path(cmdUtils.get_command_required("cert"), cmdUtils.get_command_required("key"))
          if cmdUtils.get_command(cmdUtils.m_cmd_ca_file):
              tls_options.override_default_trust_store_from_path(None, cmdUtils.get_command(cmdUtils.m_cmd_ca_file))
          tls_context = io.ClientTlsContext(tls_options)
      
          socket_options = io.SocketOptions()
      
          print('Performing greengrass discovery...')
          discovery_client = DiscoveryClient(io.ClientBootstrap.get_or_create_static_default(), socket_options, tls_context, cmdUtils.get_command_required("region"))
          resp_future = discovery_client.discover(cmdUtils.get_command_required("thing_name"))
          discover_response = resp_future.result()
      
          print(discover_response)
          if cmdUtils.get_command("print_discover_resp_only"):
              exit(0)
      
          mqtt_connection = try_iot_endpoints()
          shadow_client = iotshadow.IotShadowClient(mqtt_connection)
      
          try:
              # Subscribe to necessary topics.
              # Note that is **is** important to wait for "accepted/rejected" subscriptions
              # to succeed before publishing the corresponding "request".
              print("Subscribing to Update responses...")
              update_accepted_subscribed_future, _ = shadow_client.subscribe_to_update_shadow_accepted(
                  request=iotshadow.UpdateShadowSubscriptionRequest(thing_name=shadow_thing_name),
                  qos=mqtt.QoS.AT_LEAST_ONCE,
                  callback=on_update_shadow_accepted)
      
              update_rejected_subscribed_future, _ = shadow_client.subscribe_to_update_shadow_rejected(
                  request=iotshadow.UpdateShadowSubscriptionRequest(thing_name=shadow_thing_name),
                  qos=mqtt.QoS.AT_LEAST_ONCE,
                  callback=on_update_shadow_rejected)
      
              # Wait for subscriptions to succeed
              update_accepted_subscribed_future.result()
              update_rejected_subscribed_future.result()
      
              print("Subscribing to Get responses...")
              get_accepted_subscribed_future, _ = shadow_client.subscribe_to_get_shadow_accepted(
                  request=iotshadow.GetShadowSubscriptionRequest(thing_name=shadow_thing_name),
                  qos=mqtt.QoS.AT_LEAST_ONCE,
                  callback=on_get_shadow_accepted)
      
              get_rejected_subscribed_future, _ = shadow_client.subscribe_to_get_shadow_rejected(
                  request=iotshadow.GetShadowSubscriptionRequest(thing_name=shadow_thing_name),
                  qos=mqtt.QoS.AT_LEAST_ONCE,
                  callback=on_get_shadow_rejected)
      
              # Wait for subscriptions to succeed
              get_accepted_subscribed_future.result()
              get_rejected_subscribed_future.result()
      
              print("Subscribing to Delta events...")
              delta_subscribed_future, _ = shadow_client.subscribe_to_shadow_delta_updated_events(
                  request=iotshadow.ShadowDeltaUpdatedSubscriptionRequest(thing_name=shadow_thing_name),
                  qos=mqtt.QoS.AT_LEAST_ONCE,
                  callback=on_shadow_delta_updated)
      
              # Wait for subscription to succeed
              delta_subscribed_future.result()
      
              # The rest of the sample runs asynchronously.
      
              # Issue request for shadow's current state.
              # The response will be received by the on_get_accepted() callback
              print("Requesting current shadow state...")
      
              with locked_data.lock:
                  # use a unique token so we can correlate this "request" message to
                  # any "response" messages received on the /accepted and /rejected topics
                  token = str(uuid4())
      
                  publish_get_future = shadow_client.publish_get_shadow(
                      request=iotshadow.GetShadowRequest(thing_name=shadow_thing_name, client_token=token),
                      qos=mqtt.QoS.AT_LEAST_ONCE)
      
                  locked_data.request_tokens.add(token)
      
              # Ensure that publish succeeds
              publish_get_future.result()
      
          except Exception as e:
              exit(e)
      
          # Wait for the sample to finish (user types 'quit', or an error occurs)
          is_sample_done.wait()
      ```

      This Python application does the following:
      + Uses Greengrass discovery to discover and connect to the core device\.
      + Requests the shadow document from the core device to get the property's initial state\.
      + Subscribes to shadow delta events, which the core device sends when the property's `desired` value differs from its `reported` value\. When the application receives a shadow delta event, it changes the value of the property and sends an update to the core device to set the new value as its `reported` value\.

      This application combines the Greengrass discovery and shadow samples from the AWS IoT Device SDK v2\.

   1. Run the sample application\. This application expects arguments that specify the client device thing name, the shadow property to use, and the certificates that authenticate and secure the connection\.<a name="test-client-device-communications-application-command-replace"></a>
      + Replace *MyClientDevice1* with the client device's thing name\.
      + Replace *\~/certs/AmazonRootCA1\.pem* with the path to the Amazon root CA certificate on the client device\.
      + Replace *\~/certs/device\.pem\.crt* with the path to the device certificate on the client device\.
      + Replace *\~/certs/private\.pem\.key* with the path to the private key file on the client device\.
      + Replace *us\-east\-1* with the AWS Region where your client device and core device operate\.

      ```
      python3 basic_discovery_shadow.py \
        --thing_name MyClientDevice1 \
        --shadow_property color \
        --ca_file ~/certs/AmazonRootCA1.pem \
        --cert ~/certs/device.pem.crt \
        --key ~/certs/private.pem.key \
        --region us-east-1 \
        --verbosity Warn
      ```

      The sample application subscribes to the shadow topics and waits to receive shadow delta events from the core device\. If the output indicates that the application receives and responds to shadow delta events, the client device can successfully interact with its shadow on the core device\.

      ```
      Performing greengrass discovery...
      awsiot.greengrass_discovery.DiscoverResponse(gg_groups=[awsiot.greengrass_discovery.GGGroup(gg_group_id='greengrassV2-coreDevice-MyGreengrassCore', cores=[awsiot.greengrass_discovery.GGCore(thing_arn='arn:aws:iot:us-east-1:123456789012:thing/MyGreengrassCore', connectivity=[awsiot.greengrass_discovery.ConnectivityInfo(id='203.0.113.0', host_address='203.0.113.0', metadata='', port=8883)])], certificate_authorities=['-----BEGIN CERTIFICATE-----\nMIICiT...EXAMPLE=\n-----END CERTIFICATE-----\n'])])
      Trying core arn:aws:iot:us-east-1:123456789012:thing/MyGreengrassCore at host 203.0.113.0 port 8883
      Connected!
      Subscribing to Update responses...
      Subscribing to Get responses...
      Subscribing to Delta events...
      Requesting current shadow state...
      Received shadow delta event.
        Delta reports that desired value is 'purple'. Changing local value...
        ClientToken is: 3dce4d3f-e336-41ac-aa4f-7882725f0033
      Changed local shadow value to 'purple'.
      Updating reported shadow value to 'purple'...
      Update request published.
      ```

      <a name="test-client-device-communications-application-troubleshooting"></a>If the application outputs an error instead, see [Troubleshooting Greengrass discovery issues](troubleshooting-client-devices.md#greengrass-discovery-issues)\.

      <a name="test-client-device-communications-application-view-core-logs"></a>You can also view the Greengrass logs on the core device to verify if the client device successfully connects and sends messages\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

1. View the component logs again to verify that the component receives shadow update confirmations from the smart light client device\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.clientdevices.MySmartLightManager.log
   ```

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2/logs/com.example.clientdevices.MySmartLightManager.log -Tail 10 -Wait
   ```

------

   The component logs messages to confirm that smart light client device changed its color\.

   ```
   2022-07-07T03:49:24.908Z [INFO] (Copier) com.example.clientdevices.MySmartLightManager: stdout. Chose random color (blue) for MyClientDevice1. {scriptName=services.com.example.clientdevices.MySmartLightManager.lifecycle.Run, serviceName=com.example.clientdevices.MySmartLightManager, currentState=RUNNING}
   2022-07-07T03:49:24.912Z [INFO] (Copier) com.example.clientdevices.MySmartLightManager: stdout. Requested color change for MyClientDevice1 to blue. {scriptName=services.com.example.clientdevices.MySmartLightManager.lifecycle.Run, serviceName=com.example.clientdevices.MySmartLightManager, currentState=RUNNING}
   2022-07-07T03:49:24.959Z [INFO] (Copier) com.example.clientdevices.MySmartLightManager: stdout. Received shadow update confirmation from client device: MyClientDevice1. {scriptName=services.com.example.clientdevices.MySmartLightManager.lifecycle.Run, serviceName=com.example.clientdevices.MySmartLightManager, currentState=RUNNING}
   ```

**Note**  
The client device's shadow is in sync between the core device and the client device\. However, the core device doesn't sync the client device's shadow with AWS IoT Core\. You might sync a shadow with AWS IoT Core to view or modify the state of all devices in your fleet, for example\. For more information about how to configure the shadow manager component to sync shadows with AWS IoT Core, see [Sync local device shadows with AWS IoT Core](sync-shadows-with-iot-core.md)\.

You've completed this tutorial\. The client device connects to the core device, sends MQTT messages to AWS IoT Core and Greengrass components, and receives shadow updates from the core device\. For more information about the topics covered in this tutorial, see the following:
+ [Associate client devices](associate-client-devices.md)
+ [Manage core device endpoints](manage-core-device-endpoints.md)
+ [Test client device communications](test-client-device-communications.md)
+ [Greengrass discovery RESTful API](greengrass-discover-api.md)
+ [Relay MQTT messages between client devices and AWS IoT Core](relay-client-device-messages.md)
+ [Interact with client devices in components](interact-with-client-devices-in-components.md)
+ [Interact with device shadows](interact-with-shadows.md)
+ [Interact with and sync client device shadows](work-with-client-device-shadows.md)