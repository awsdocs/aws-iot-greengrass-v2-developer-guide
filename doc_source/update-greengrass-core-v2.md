# Update the AWS IoT Greengrass Core software \(OTA\)<a name="update-greengrass-core-v2"></a>

The AWS IoT Greengrass Core software comprises the [Greengrass nucleus component](greengrass-nucleus-component.md) and other optional components\. You can deploy the nucleus and other components to your devices to perform over\-the\-air \(OTA\) updates of the software\. This feature is built in to the AWS IoT Greengrass Core software\.

OTA updates make it more efficient to:
+ Fix security vulnerabilities\.
+ Address software stability issues\.
+ Deploy new or improved features\.

**Topics**
+ [Requirements](#ota-update-requirements)
+ [Considerations for core devices](#ota-update-considerations)
+ [Greengrass nucleus update behavior](#ota-update-behavior-nucleus)
+ [Perform an OTA update](#create-ota-update)

## Requirements<a name="ota-update-requirements"></a>

The following requirements apply to deploy OTA updates of the AWS IoT Greengrass Core software:
+ The Greengrass core device must have a connection to the AWS Cloud to receive the deployment\.
+ The Greengrass core device must be correctly configured and provisioned with certificates and keys for authentication with AWS IoT Core and AWS IoT Greengrass\.
+ The AWS IoT Greengrass Core software must be set up and running as a system service\. OTA updates don't work if you run the nucleus from the JAR file, `Greengrass.jar`\. For more information, see [Configure AWS IoT Greengrass as a system service](configure-greengrass-core-v2.md#configure-system-service)\.

## Considerations for core devices<a name="ota-update-considerations"></a>

Before perform an OTA update, be aware of the impact on the core devices that you update:
+ The Greengrass nucleus shuts down\. 
+ All components running on the core device also shut down\. If those components write to local resources, they might leave those resources in an incorrect state unless shut down properly\. Components can use [interprocess communication](interprocess-communication.md) to tell the nucleus component to defer the update until they clean up the resources that they use\.
+ While the nucleus component is shut down, the core device loses its connections with the AWS Cloud and local devices\. 
+ Long\-lived Lambda functions that run as components lose their dynamic state information and drop all pending work\.

## Greengrass nucleus update behavior<a name="ota-update-behavior-nucleus"></a>

<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all component dependencies for that component\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\. 

When the version of the [Greengrass nucleus component](greengrass-nucleus-component.md) changes, the AWS IoT Greengrass Core software—which includes the nucleus and all other components on your device—restarts to apply the changes\. When the nucleus component is updated, it has an [impact on core devices](#ota-update-considerations)\. Because of this, consider controlling when a new nucleus patch version is deployed to your devices\. To do so, you must directly include the Greengrass nucleus component in your deployment\. This means that you are including a specific version of that component in your deployment configuration and are not relying on component dependencies to deploy that component to your devices\. For more information about defining dependencies in your component recipes, see [Recipe format](component-recipe-reference.md#recipe-format)\.

Review the following table to understand the update behavior for the Greengrass nucleus component based on your actions and deployment configurations\.


| Action | Deployment configuration | Nucleus update behavior | 
| --- | --- | --- | 
| Add new devices to a thing group targeted by an existing deployment without revising the deployment\. | The deployment does not directly include Greengrass nucleus\.The deployment directly includes at least one AWS\-provided component, or includes a custom component that depends on an AWS\-provided component or on the Greengrass nucleus\. | On new devices, installs the latest patch version of nucleus that meets all component dependency requirements\.On existing devices, does not update the installed version of the nucleus\. | 
| Add new devices to a thing group targeted by an existing deployment without revising the deployment\. |  The deployment directly includes a specific version of the Greengrass nucleus\.  | On new devices, installs the specified nucleus version\.On existing devices, does not update the installed version of the nucleus\. | 
| Create a new deployment or revise an existing deployment\. | The deployment does not directly include Greengrass nucleus\.The deployment directly includes at least one AWS\-provided component, or includes a custom component that depends on an AWS\-provided component or on the Greengrass nucleus\. | On all targeted devices, installs the latest patch version of the nucleus that meets all component dependency requirements, including on any new devices that you add to the targeted thing group\. | 
| Create a new deployment or revise an existing deployment\. | The deployment directly includes a specific version of the Greengrass nucleus\. | On all targeted devices, installs the specified nucleus version, including any new devices that you add to the targeted thing group\.  | 

## Perform an OTA update<a name="create-ota-update"></a>

To perform an OTA update, [create a deployment](create-deployments.md) that includes the [nucleus component](greengrass-nucleus-component.md) and the version to install\.

**Note**  
Several AWS\-provided public components depend on specific minor versions of the Greengrass nucleus\. Because of this dependency, you must update these public components when you update the Greengrass nucleus to a new minor version\. When you create the deployment, check which public components depend on the nucleus, and update those components to a later version that supports the desired Greengrass nucleus version\.  
For example, version 2\.0\.7 of the [stream manager component](stream-manager-component.md) supports v2\.0\.3 to v2\.0\.x of the Greengrass nucleus\. Stream manager v2\.0\.8 supports v2\.0\.3 to v2\.1\.x of the Greengrass nucleus\. Because of these version constraints, you must update stream manager to v2\.0\.8 or later when you update the Greengrass nucleus to v2\.1 on core devices that run stream manager v2\.0\.7\.