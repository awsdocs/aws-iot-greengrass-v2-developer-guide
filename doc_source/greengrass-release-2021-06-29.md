# Release: AWS IoT Greengrass Core v2\.3\.0 software update on June 29, 2021<a name="greengrass-release-2021-06-29"></a>

This release provides version 2\.3\.0 of the Greengrass nucleus component\.

**Release date:** June 29, 2021

**Release highlights**
+ **Large configuration support**—The Greengrass nucleus component now supports deployment documents up to 10 MB\. You can now deploy larger configuration updates to Greengrass components\.
**Note**  
<a name="greengrass-nucleus-v2.3.0-large-configuration-support-permission"></a>To use this feature, a core device's AWS IoT policy must allow the `greengrass:GetDeploymentConfiguration` permission\. If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), your core device's AWS IoT policy allows `greengrass:*`, which includes this permission\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.

**Topics**
+ [Public component updates](#greengrass-2021-06-29-components)

## Public component updates<a name="greengrass-2021-06-29-components"></a>

The following table lists AWS\-provided components that include new and updated features\.

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus | Version 2\.3\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.3.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-06-29.html)  | 