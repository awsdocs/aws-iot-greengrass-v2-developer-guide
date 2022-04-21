# Release: AWS IoT Greengrass Core v2\.5\.0 software update on November 12, 2021<a name="greengrass-release-2021-11-12"></a>

This release provides version 2\.5\.0 of the Greengrass nucleus component, new AWS\-provided components, and updates to AWS\-provided components\.

**Release date:** November 12, 2021

**Release highlights**
+ **Windows device support**—You can now run the AWS IoT Greengrass Core software on devices running Windows operating systems\. For more information, see [Supported platforms and requirements](setting-up.md#installation-requirements) and [Greengrass feature compatibility by operating system](operating-system-feature-support-matrix.md)\.
+ **New thing group removal behavior**—You can now remove a core device from a thing group to remove that thing group's components in the next deployment to that device\.
**Important**  
<a name="greengrass-nucleus-v2.5.0-thing-group-removal-permission-requirement"></a>As a result of this change, a core device's AWS IoT policy must have the `greengrass:ListThingGroupsForCoreDevice` permission\. If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), the default AWS IoT policy allows `greengrass:*`, which includes this permission\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.
+ **Hardware security support**—You can now configure the AWS IoT Greengrass Core software to use a hardware security module \(HSM\), so you can securely store the device's private key and certificate\. For more information, see [Hardware security integration](hardware-security.md)\.
+ **HTTPS proxy support**—You can now configure the AWS IoT Greengrass Core software to connect through HTTPS proxies\. For more information, see [Connect on port 443 or through a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy)\.

**Topics**
+ [Platform support updates](#greengrass-2021-11-12-platforms)
+ [Public component updates](#greengrass-2021-11-12-components)

## Platform support updates<a name="greengrass-2021-11-12-platforms"></a>


| **Platform** | **Details** | 
| --- | --- | 
| Windows |  AWS IoT Greengrass now supports running the AWS IoT Greengrass Core software on the following versions of Windows: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-11-12.html) For more information, see [Supported platforms and requirements](setting-up.md#installation-requirements) and [Greengrass feature compatibility by operating system](operating-system-feature-support-matrix.md)\. | 

## Public component updates<a name="greengrass-2021-11-12-components"></a>

The following table lists AWS\-provided components that include new and updated features\.

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all component dependencies for that component\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus |  Version 2\.5\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.5.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-11-12.html)  | 
| Greengrass CLI |  Version 2\.5\.0 of the [Greengrass CLI](greengrass-cli-component.md) is available\. <a name="changelog-greengrass-cli-2.5.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-11-12.html)  | 
| CloudWatch metrics |  Version 3\.0\.0 of the [CloudWatch metrics](cloudwatch-metrics-component.md) component is available\. <a name="changelog-cloudwatch-metrics-3.0.0-major-version-changes"></a>This version of the CloudWatch metrics component expects different configuration parameters than version 2\.x\. If you use a non\-default configuration for version 2\.x, and you want to upgrade from v2\.x to v3\.x, you must update the component's configuration\. For more information, see [CloudWatch metrics component configuration](cloudwatch-metrics-component.md#cloudwatch-metrics-component-configuration)\. <a name="changelog-cloudwatch-metrics-3.0.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-11-12.html)  | 
| Lambda manager |  Version 2\.2\.0 of the [Lambda manager](lambda-manager-component.md) component is available\. <a name="changelog-lambda-manager-2.2.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-11-12.html)  | 