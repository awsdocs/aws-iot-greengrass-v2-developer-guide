# Release: AWS IoT Greengrass Core v2\.8\.1 software update on October 13, 2022<a name="greengrass-release-2022-10-13"></a>

This release provides version 2\.8\.1 of the Greengrass nucleus component\.

**Release date:** October 13, 2022

**Note**  
If you are using Greengrass nucleus version 2\.8\.0, we strongly recommend that you upgrade to Greengrass nucleus version 2\.8\.1\.

**Topics**
+ [Public component updates](#greengrass-2022-10-13-components)

## Public component updates<a name="greengrass-2022-10-13-components"></a>

The following table lists AWS\-provided components that include new and updated features\.

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus |  Version 2\.8\.1 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.8.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-10-13.html)  | 