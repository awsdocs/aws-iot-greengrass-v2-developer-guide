# Release: AWS IoT Greengrass Core v2\.5\.2 software update on December 3, 2021<a name="greengrass-release-2021-12-03"></a>

This release provides version 2\.5\.2 of the Greengrass nucleus component\.

**Release date:** December 3, 2021

**Topics**
+ [Public component updates](#greengrass-2021-12-03-components)

## Public component updates<a name="greengrass-2021-12-03-components"></a>

The following table lists AWS\-provided components that include new and updated features\.

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus |  Version 2\.5\.2 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.5.2"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-12-03.html)  | 
| AWS IoT Device Defender |  Version 3\.0\.1 of the [AWS IoT Device Defender](device-defender-component.md) component is available\. <a name="changelog-device-defender-3.0.0-major-version-changes"></a>This version of the AWS IoT Device Defender component expects different configuration parameters than version 2\.x\. If you use a non\-default configuration for version 2\.x, and you want to upgrade from v2\.x to v3\.x, you must update the component's configuration\. For more information, see [AWS IoT Device Defender component configuration](device-defender-component.md#device-defender-component-configuration)\. <a name="changelog-device-defender-3.0.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-12-03.html)  | 