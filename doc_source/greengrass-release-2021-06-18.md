# Release: AWS IoT Greengrass Core v2\.2\.0 software update on June 18, 2021<a name="greengrass-release-2021-06-18"></a>

This release provides version 2\.2\.0 of the Greengrass nucleus component, new AWS\-provided components, and updates to AWS\-provided components\.

**Release date:** June 18, 2021

**Release highlights**
+ **Client device support**—The new AWS\-provided client device components enable you to connect client devices to your core devices using cloud discovery\. You can sync client devices with AWS IoT Core and interact with client devices in Greengrass components\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.
+ **Local shadow service**—The new shadow manager component enables the local shadow service on your core devices\. You can use this shadow service to interact with local shadows while offline using the Greengrass interprocess communication \(IPC\) libraries in the AWS IoT Device SDK\. You can also use the shadow manager component to synchronize local shadow states with AWS IoT Core\. For more information, see [Interact with device shadows](interact-with-shadows.md)\.

**Topics**
+ [Public component updates](#greengrass-2021-06-18-components)

## Public component updates<a name="greengrass-2021-06-18-components"></a>

The following table lists AWS\-provided components that include new and updated features\. 

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus | Version 2\.2\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.2.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-18.html)  | 
| Shadow manager |  Version 2\.0\.0 of the new [shadow manager component](shadow-manager-component.md) is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-18.html)  | 
| Client device auth |  Version 2\.0\.0 of the new [client device auth component](client-device-auth-component.md) is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-18.html)  | 
| Moquette MQTT broker |  Version 2\.0\.0 of the new [Moquette MQTT broker component](mqtt-broker-moquette-component.md) is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-18.html)  | 
| MQTT bridge |  Version 2\.0\.0 of the new [MQTT bridge component](mqtt-bridge-component.md) is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-18.html)  | 
| IP detector |  Version 2\.0\.0 of the new [IP detector component](ip-detector-component.md) is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-18.html)  | 
| Log manager |  Version 2\.1\.1 of the [log manager component](log-manager-component.md) is available\. <a name="changelog-log-manager-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-18.html)  | 
| DLR object detection |  Version 2\.1\.2 of the [DLR object detection](dlr-object-detection-component.md) is available\. <a name="changelog-dlr-object-detection-2.1.2"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-18.html)  | 
| TensorFlow Lite object detection |  Version 2\.1\.1 of the [TensorFlow Lite object detection](tensorflow-lite-object-detection-component.md) is available\. <a name="changelog-tensorflow-lite-object-detection-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-18.html)  | 