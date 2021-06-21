# Connect client devices to core devices<a name="connect-client-devices"></a>

You can configure *cloud discovery* to connect client devices to core devices\. When you configure cloud discovery, client devices can connect to the AWS IoT Greengrass cloud service to retrieve information about core devices to which they can connect\. Then, the client devices can attempt to connect to each core device until they successfully connect\.

To use cloud discovery, you must do the following:
+ Associate client devices to the core devices to which they can connect\.
+ Specify the MQTT broker endpoints where client devices can connect to each core device\.
+ Deploy components to the core device that enable support for client devices\.

  You can also deploy optional components to do the following:
  + Relay messages between client devices, Greengrass components, and the AWS IoT Core cloud service\.
  + Automatically manage core device MQTT broker endpoints for you\.

You must also review and update the core device's AWS IoT policy to ensure that it has the permissions required to connect client devices\. For more information, see [Review and update the core device AWS IoT policy](client-devices-tutorial.md#update-core-device-iot-policy)\.

After you configure cloud discovery, you can test communications between a client device and a core device\. For more information, see [Test client device communications](test-client-device-communications.md)\.

**Topics**
+ [Greengrass components for client device support](#cloud-discovery-components)
+ [Configure cloud discovery \(console\)](#configure-cloud-discovery-console)
+ [Configure cloud discovery \(AWS CLI\)](#configure-cloud-discovery-cli)
+ [Associate client devices](associate-client-devices.md)
+ [Manage core device endpoints](manage-core-device-endpoints.md)
+ [Test client device communications](test-client-device-communications.md)
+ [Greengrass discovery RESTful API](greengrass-discover-api.md)

## Greengrass components for client device support<a name="cloud-discovery-components"></a>

To enable client devices to connect and communicate with a core device, you deploy the following Greengrass components to the core device:<a name="client-device-component-overviews"></a>
+ [Client device auth](client-device-auth-component.md) \(`aws.greengrass.clientdevices.Auth`\)

  Deploy the client device auth component to authenticate client devices and authorize client device actions\. This component allows your AWS IoT things to connect to a core device\.

  This component requires configuration to use\. You must specify groups of client devices and the operations that each group is authorized to perform, such as to connect and communicate over MQTT\. For more information, see [client device auth component configuration](client-device-auth-component.md#client-device-auth-component-configuration)\.
+ [MQTT broker \(Moquette\)](mqtt-broker-moquette-component.md) \(`aws.greengrass.clientdevices.mqtt.Moquette`\)

  Deploy the Moquette MQTT broker component to run the open source Moquette MQTT broker\. The Moquette MQTT broker is compliant with MQTT 3\.1 and includes local support for QoS 0, QoS 1, QoS 2, retained messages, and persistent subscriptions\.

  You aren't required to configure this component to use it\. However, you can configure the port where this component operates the MQTT broker\. By default, it uses port 8883\.
+ [MQTT bridge](mqtt-bridge-component.md) \(`aws.greengrass.clientdevices.mqtt.Bridge`\)

  \(Optional\) Deploy the MQTT bridge component to relay messages between client devices \(local MQTT\), local publish/subscribe, and AWS IoT Core MQTT\. Configure this component to sync client devices with AWS IoT Core and interact with client devices from Greengrass components\.

  This component requires configuration to use\. You must specify the topic mappings where this component relays messages\. For more information, see [MQTT bridge component configuration](mqtt-bridge-component.md#mqtt-bridge-component-configuration)\.
+ [IP detector](ip-detector-component.md) \(`aws.greengrass.clientdevices.IPDetector`\)

  \(Optional\) Deploy the IP detector component to automatically report the core device's MQTT broker endpoints to the AWS IoT Greengrass cloud service\. You can use this component unless you have a complex network setup, such as one where a router forwards the MQTT broker port to the core device\.

  You aren't required to configure this component to use it\.

**Important**  <a name="client-device-support-nucleus-requirement"></a>
The core device must run [Greengrass nucleus](greengrass-nucleus-component.md) v2\.2\.0 or later to support client devices\.

## Configure cloud discovery \(console\)<a name="configure-cloud-discovery-console"></a>

You can use the AWS IoT Greengrass console to associate client devices, manage core device endpoints, and deploy components to enable client device support\. For more information, see [Enable client device support](client-devices-tutorial.md#enable-client-device-support)\.

## Configure cloud discovery \(AWS CLI\)<a name="configure-cloud-discovery-cli"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to associate client devices, manage core device endpoints, and deploy components to enable client device support\. For more information, see the following:
+ [Manage client device associations \(AWS CLI\)](associate-client-devices.md#manage-client-device-associations-cli)
+ [Manage core device endpoints](manage-core-device-endpoints.md)
+ [AWS\-provided client device components](client-device-components.md)
+ [Create deployments](create-deployments.md)