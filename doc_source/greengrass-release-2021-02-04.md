# Release: AWS IoT Greengrass Core v2\.0\.4 software update on February 04, 2021<a name="greengrass-release-2021-02-04"></a>

This release provides version 2\.0\.4 of the Greengrass nucleus component\. It includes the new `greengrassDataPlanePort` parameter to configure HTTPS communication over port 443 and fixes bugs\. The minimal IAM policy now requires the `iam:GetPolicy` and `sts:GetCallerIdentity` when the AWS IoT Greengrass Core software installer is run with `--provision true`\.

**Release date:** February 04, 2021



## Public component updates<a name="greengrass-2021-02-04-components"></a>

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all component dependencies for that component\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus | Version 2\.0\.4 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.0.4"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-02-04.html)  | 