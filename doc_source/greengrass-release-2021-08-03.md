# Release: AWS IoT Greengrass Core v2\.4\.0 software update on August 3, 2021<a name="greengrass-release-2021-08-03"></a>

This release provides version 2\.4\.0 of the Greengrass nucleus component, new AWS\-provided components, and updates to AWS\-provided components\.

**Release date:** August 3, 2021

**Release highlights**
+ **System resource limits**—The Greengrass nucleus component now supports system resource limits\. You can configure the maximum amount of CPU and RAM usage that each component's processes can use on the core device\. For more information, see [Configure system resource limits for components](configure-greengrass-core-v2.md#configure-component-system-resource-limits)\.
+ **Pause/resume components**—The Greengrass nucleus now supports pausing and resuming components\. You can use the interprocess communication \(IPC\) library to develop custom components that pause and resume other components' processes\. For more information, see [PauseComponent](ipc-component-lifecycle.md#ipc-operation-pausecomponent) and [ResumeComponent](ipc-component-lifecycle.md#ipc-operation-resumecomponent)\.
+ **Install with AWS IoT fleet provisioning**—Use the new AWS IoT fleet provisioning plugin to install the AWS IoT Greengrass Core software on devices that connect to AWS IoT to provision required AWS resources\. Devices use a claim certificate to provision\. You can embed the claim certificate on devices during manufacturing, so each device can provision as soon as it comes online\. For more information, see [Install AWS IoT Greengrass Core software with AWS IoT fleet provisioning](fleet-provisioning.md)\.
+ **Install with custom provisioning**—Develop a custom provisioning plugin to provision required AWS resources when you install the AWS IoT Greengrass Core software on devices\. You can create a Java application that runs during installation to set up Greengrass core devices for your custom use case\. For more information, see [Install AWS IoT Greengrass Core software with custom resource provisioning](custom-provisioning.md)\.

**Topics**
+ [Public component updates](#greengrass-2021-08-03-components)

## Public component updates<a name="greengrass-2021-08-03-components"></a>

The following table lists AWS\-provided components that include new and updated features\. 

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus | Version 2\.4\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.4.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-08-03.html)  | 
| Greengrass CLI | Version 2\.4\.0 of the [Greengrass CLI](greengrass-cli-component.md) is available\. <a name="changelog-greengrass-cli-2.4.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-08-03.html)  | 
| AWS IoT fleet provisioning by claim |  The AWS IoT fleet provisioning by claim plugin is now available\. For more information, see [Install AWS IoT Greengrass Core software with AWS IoT fleet provisioning](fleet-provisioning.md)\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-08-03.html)  | 