# Interact with local IoT devices<a name="interact-with-local-iot-devices"></a>

*Client devices* are local IoT devices that connect to and communicate with a Greengrass core device over MQTT\. You can connect client devices to core devices to do the following:
+ Relay messages and data between client devices and AWS IoT Core\.
+ Interact with MQTT messages in Greengrass components\.
+ Sync client devices shadows with AWS IoT Core\.
+ Interact with device shadows in Greengrass components\.

To connect to a core device, client devices can use *cloud discovery*\. Client devices connect to the AWS IoT Greengrass cloud service to retrieve information about core devices to which they can connect\. Then, they can connect to a core device to process their messages and sync their data with the AWS IoT Core cloud service\.

You can follow a tutorial that walks through how to configure a core device to connect and communicate with an AWS IoT thing\. This tutorial also explores how to develop a custom Greengrass component that interacts with client devices\. For more information, see [Tutorial: Connect and test client devices](client-devices-tutorial.md)\.

**Topics**
+ [Tutorial: Connect and test client devices](client-devices-tutorial.md)
+ [AWS\-provided client device components](client-device-components.md)
+ [Connect client devices to core devices](connect-client-devices.md)
+ [Relay MQTT messages between client devices and AWS IoT Core](relay-client-device-messages.md)
+ [Interact with client devices in components](interact-with-client-devices-in-components.md)
+ [Sync and interact with client device shadows](work-with-client-device-shadows.md)
+ [Troubleshooting client devices](troubleshooting-client-devices.md)