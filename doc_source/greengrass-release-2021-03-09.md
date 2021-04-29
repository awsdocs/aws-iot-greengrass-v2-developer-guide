# Release: AWS IoT Greengrass Core v2\.0\.5 software update on March 09, 2021<a name="greengrass-release-2021-03-09"></a>

This release provides version 2\.0\.5 of the Greengrass nucleus component\. It fixes an issue with network proxy support and an issue with the Greengrass data plane endpoint in AWS China Regions\.

**Release date:** March 09, 2021



## Public component updates<a name="greengrass-2021-03-09-components"></a>

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all component dependencies for that component\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus | Version 2\.0\.5 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.0.5"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-03-09.html)  | 