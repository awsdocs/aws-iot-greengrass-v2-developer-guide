# Deploy AWS IoT Greengrass components to devices<a name="manage-deployments"></a>

You can deploy components to individual devices or groups of devices with AWS IoT Greengrass\. With *deployments*, you define the AWS IoT Greengrass components and configurations to deploy to core devices\. AWS IoT Greengrass deploys to *target devices*, which are AWS IoT things or thing groups that represent Greengrass core devices\. AWS IoT Greengrass uses [AWS IoT Core jobs](https://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html) to deploy to your core devices, so you can configure how the job rolls out to your devices\.

Each core device runs the combination of the software from the deployments that target the device\. However, deployments to the same target overwrite previous deployments to that target\. When you create a deployment, you define the components and configurations to apply to the core devices' existing software\. When you revise a deployment for a target, you replace the components from the previous revision with the components in the new revision\. For example, you deploy components `A` and `B` to a thing group `X`\. You then deploy components `B` and `C` to a different thing group `Y`\. As a result, the components `A`, `B`, and `C` all run on any core device that is a member of both groups\. Then, you create another deployment to thing group `X` that specifies only component `B`\. As a result, the core devices in that group no longer run component `A`\.

When a core device receives a deployment, it checks whether the deployment's components are compatible with the core device\. The core device checks whether it meets each component's platform requirements\. The deployment fails if a component doesn't support the core device's platform\. The core device also checks whether each component's dependencies are compatible with version constraints from other deployments to different targets that include this core device\. For example, you deploy component `A` to a thing group `X`, and component `A` depends on component `com.example.PythonRuntime` versions `3.5 - 3.10`\. You then deploy component `B` to a thing group `Y`, and component `B` depends on component `com.example.PythonRuntime` versions `3.7 - 3.10`\. As a result, core devices in these thing groups determine that they can deploy versions `3.7 - 3.10` of the `com.example.PythonRuntime` component, because these versions are where the deployments' version constraints overlap\. Then, you deploy component `C` to a thing group `Z`, and component `C` depends on component `com.example.PythonRuntime` versions `2.6 - 2.7`\. This deployment fails, because there's no component version that meets the version constraints `2.6 - 2.7` and `3.7 - 3.10`\. AWS IoT Greengrass uses [NPM semantic version constraints](https://semver.npmjs.com/)\.

**Note**  <a name="thing-group-removal-behavior"></a>
When you remove a core device from a thing group, the component deployment behavior depends on the version of the [Greengrass nucleus](greengrass-nucleus-component.md) that the core device runs\.  
The thing group removal behavior depends on whether the core device's AWS IoT policy grants the `greengrass:ListThingGroupsForCoreDevice` permission\. For more information about this permission and AWS IoT policies for core devices, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.  
**If the AWS IoT policy grants this permission**  
<a name="thing-group-removal-behavior-remove-components"></a>When you remove a core device from a thing group, AWS IoT Greengrass removes that thing group's components from the device in the next deployment\. However, if another deployment targets the core device and includes a component, the core device doesn't remove that component\.
**If the AWS IoT policy doesn't grant this permission**  
<a name="thing-group-removal-behavior-no-remove-components"></a>When you remove a core device from a thing group, AWS IoT Greengrass doesn't delete that thing group's components from the device\. Because of this, we recommend that you don't remove core devices from thing groups where you deploy components\.  
<a name="thing-group-removal-behavior-no-remove-components-how-to-remove"></a>To remove a component from a device, where the device is no longer a member of the thing group that deploys that component, use the [deployment create](gg-cli-deployment.md#deployment-create) command of the Greengrass CLI\. Specify the component to remove with the `--remove` argument, and specify the thing group with the `--groupId` argument\.
<a name="thing-group-removal-behavior-remove-components"></a>When you remove a core device from a thing group, AWS IoT Greengrass removes that thing group's components from the device in the next deployment\. However, if another deployment targets the core device and includes a component, the core device doesn't remove that component\.  
This behavior requires that the core device's AWS IoT policy grants the `greengrass:ListThingGroupsForCoreDevice` permission\. If a core device doesn't have this permission, the core device fails to apply deployments\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.
<a name="thing-group-removal-behavior-no-remove-components"></a>When you remove a core device from a thing group, AWS IoT Greengrass doesn't delete that thing group's components from the device\. Because of this, we recommend that you don't remove core devices from thing groups where you deploy components\.  
<a name="thing-group-removal-behavior-no-remove-components-how-to-remove"></a>To remove a component from a device, where the device is no longer a member of the thing group that deploys that component, use the [deployment create](gg-cli-deployment.md#deployment-create) command of the Greengrass CLI\. Specify the component to remove with the `--remove` argument, and specify the thing group with the `--groupId` argument\.

Deployments are continuous\. When you create a deployment, AWS IoT Greengrass rolls out the deployment to target devices that are online\. If a target device isn't online, then it receives the deployment the next time it connects to AWS IoT Greengrass\. When you add a core device to a target thing group, AWS IoT Greengrass sends the device the latest deployment for that thing group\.

Each target thing or thing group can have one deployment at a time\. This means that when you create a deployment for a target, AWS IoT Greengrass no longer deploys the previous revision of that target's deployment\.

Deployments provide several options that let you control which devices receive an update and how the update deploys\. When you create a deployment, you can configure the following options:
+ **AWS IoT Greengrass components**

  Define the components to install and run on the target devices\. AWS IoT Greengrass components are software modules that you deploy and run on Greengrass core devices\. Devices receive components only if the component supports the device's platform\. This lets you deploy to groups of devices even if the target devices run on multiple platforms\. If a component doesn't support the device's platform, the component doesn't deploy to the device\.

  You can deploy custom components and AWS\-provided components to your devices\. When you deploy a component, AWS IoT Greengrass identifies any component dependencies and deploys them too\. For more information, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md) and [AWS\-provided components](public-components.md)\.

  You define the version and configuration update to deploy for each component\. The *configuration update* specifies how to modify the component's existing configuration on the core device, or the component's default configuration if the component doesn't exist on the core device\. You can specify which configuration values to reset to default values and the new configuration values to merge onto the core device\. When a core device receives deployments for different targets, and each deployment specifies compatible component versions, the core device applies configuration updates in order based on the timestamp of when you create the deployment\. For more information, see [Update component configurations](update-component-configurations.md)\.
**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.
+ **Deployment policies**

  Define when it's safe to deploy a configuration and what to do if the deployment fails\. You can specify whether or not to wait for components to report that they can update\. You can also specify whether or not to roll back devices to their previous configuration if they apply a deployment that fails\.
+ **Stop configuration**

  Define when and how to stop a deployment\. The deployment stops and fails if the criteria that you define are met\. For example, you can configure a deployment to stop if a percentage of devices fail to apply that deployment after a minimum number of devices receive it\.
+ **Rollout configuration**

  Define the rate at which a deployments rolls out to the target devices\. You can configure an exponential rate increase with minimum and maximum rate bounds\.
+ **Timeout configuration**

  Define the maximum amount of time each device has to apply a deployment\. If a device exceeds the duration that you specify, then the device fails to apply the deployment\.

**Important**  
Custom components can define artifacts in S3 buckets\. When the AWS IoT Greengrass Core software deploys a component, it downloads the component's artifacts from the AWS Cloud\. Core device roles don't allow access to S3 buckets by default\. To deploy custom components that define artifacts in an S3 bucket, the core device role must grant permissions to download artifacts from that bucket\. For more information, see [Allow access to S3 buckets for component artifacts](device-service-role.md#device-service-role-access-s3-bucket)\.

**Topics**
+ [Create deployments](create-deployments.md)
+ [Revise deployments](revise-deployments.md)
+ [Cancel deployments](cancel-deployments.md)
+ [Check deployment status](check-deployment-status.md)