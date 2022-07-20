# Release: AWS IoT Greengrass Core v2\.5\.6 software update on May 31, 2022<a name="greengrass-release-2022-05-31"></a>

This release provides version 2\.5\.6 of the Greengrass nucleus component and version 2\.2\.4 of the log manager component\.

**Release date:** May 31, 2022

**Topics**
+ [Public component updates](#greengrass-2022-05-31-components)

## Public component updates<a name="greengrass-2022-05-31-components"></a>

The following table lists AWS\-provided components that include new and updated features\.

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus |  Version 2\.5\.6 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.5.6"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-05-31.html)  | 
| Log manager |  Version 2\.2\.4 of the [log manager](log-manager-component.md) component is available\. <a name="changelog-log-manager-2.2.4"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-05-31.html)  | 