# Interact with device shadows<a name="interact-with-shadows"></a>

Greengrass devices can interact with [AWS IoT device shadows](https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html) using components\. A *shadow* is a JSON document that stores the current or desired state information for an AWS IoT thing\. Shadows can make a device’s state available to other AWS IoT Greengrass components whether the device is connected to AWS IoT or not\. Each AWS IoT device has its own classic, unnamed shadow\. You can also create multiple named shadows for each device\. 

Devices and services can create, update, and delete cloud shadows by using MQTT and the [reserved MQTT shadow topics](https://docs.aws.amazon.com/iot/latest/developerguide/reserved-topics.html#reserved-topics-shadow), HTTP using the [Device Shadow REST API](https://docs.aws.amazon.com/iot/latest/developerguide/device-shadow-rest-api.html), and the [AWS CLI for AWS IoT](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iot-data/index.html)\.

The [shadow manager](shadow-manager-component.md) component enables your Greengrass components to create, update, and delete local shadows by using the [local shadow service](ipc-local-shadows.md) and the local publish/subscribe shadow topics\. The shadow manager also manages the storage of these local shadow documents on your core device, and handles the synchronization of shadow state information with cloud shadows\.

For more information about AWS IoT device shadow concepts, see [AWS IoT Device Shadow service](https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html) in the *AWS IoT Developer Guide*\.

**Topics**
+ [Interact with shadows in components](interact-with-shadows-in-components.md)
+ [Sync local device shadows with AWS IoT Core](sync-shadows-with-iot-core.md)