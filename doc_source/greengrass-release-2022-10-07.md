# Release: AWS IoT Greengrass Core v2\.8\.0 software update on October 7, 2022<a name="greengrass-release-2022-10-07"></a>

This release provides version 2\.8\.0 of the Greengrass nucleus component and version 1\.1\.0 of the MQTT 5 broker \(EMQX\) component\.

**Release date:** October 7, 2022

**Release highlights**
+ **Deployment error codes** – The Greengrass nucleus now reports a [deployment health status](deployment-health-notifications.md) response that includes detailed error codes when a component deployment can't be completed\. For more information, see [Detailed deployment error codes](troubleshooting-deployment.md)\.
+ **Component error statuses** – The Greengrass nucleus now reports a [component health status](deployment-health-notifications.md) response that includes detailed error statuses when a component enters the `BROKEN` or `ERRORED` state\. For more information, see [Detailed component status codes](troubleshooting-component.md)\.

**Topics**
+ [Public component updates](#greengrass-2022-10-07-components)

## Public component updates<a name="greengrass-2022-10-07-components"></a>

The following table lists AWS\-provided components that include new and updated features\.

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus |  Version 2\.8\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.8.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-10-07.html)  | 
| MQTT 5 broker \(EMQX\) |  Version 1\.1\.0 of the [MQTT 5 broker \(EMQX\)](mqtt-broker-emqx-component.md) component is available\. <a name="changelog-emqx-1.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-10-07.html)  | 