# Deploy AWS IoT Greengrass components to devices<a name="manage-deployments"></a>

You can deploy components to individual devices or groups of devices with AWS IoT Greengrass\. With *deployments*, you define the AWS IoT Greengrass components and configurations to deploy to core devices\. AWS IoT Greengrass deploys to *target devices*, which are AWS IoT things or thing groups that represent Greengrass core devices\. AWS IoT Greengrass uses [AWS IoT Core jobs](https://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html) to deploy to your core devices, so you can configure how the job rolls out to your devices\.

Each core device runs the combination of the software from the deployments that target the device\. However, deployments to the same target overwrite previous deployments to that target\. When you create a deployment, you define the components and configurations to apply to the core devices' existing software\. When you revise a deployment for a target, you replace the components from the previous revision with the components in the new revision\. For example, you deploy components `A` and `B` to a thing group `X`\. You then deploy components `B` and `C` to a different thing group `Y`\. As a result, the components `A`, `B`, and `C` all run on any core device that is a member of both groups\. Then, you create another deployment to thing group `X` that specifies only component `B`\. As a result, the core devices in that group no longer run component `A`\.

Deployments are continuous\. When you create a deployment, AWS IoT Greengrass rolls out the deployment to target devices that are online\. If a target device isn't online, then it receives the deployment the next time it connects to AWS IoT Greengrass\. When you add a core device to a target thing group, AWS IoT Greengrass sends the device the latest deployment for that thing group\.

Each target thing or thing group can have one deployment at a time\. This means that when you create a deployment for a target, AWS IoT Greengrass no longer deploys the previous revision of that target's deployment\.

Deployments provide several options that let you control which devices receive an update and how the update deploys\. When you create a deployment, you can configure the following options:
+ **AWS IoT Greengrass components**

  Define the components to install and run on the target devices\. AWS IoT Greengrass components are software modules that you deploy and run on Greengrass core devices\. Devices receive components only if the component supports the device's platform\. This lets you deploy to groups of devices even if the target devices run on multiple platforms\. If a component doesn't support the device's platform, the component doesn't deploy to the device\.

  You can deploy custom components and AWS\-provided components to your devices\. When you deploy a component, AWS IoT Greengrass identifies any component dependencies and deploys them too\. For more information, see [Manage AWS IoT Greengrass components](manage-components.md)\.

  You define the version and configuration update to deploy for each component\. The *configuration update* specifies how to modify the component's existing configuration on the core device, or the component's default configuration if the component doesn't exist on the core device\. You can specify which configuration values to reset to default values and the new configuration values to merge onto the core device\. For more information, see [Update component configurations](update-component-configurations.md)\.
+ **Deployment policies**

  Define when it's safe to deploy a configuration and what to do if the deployment fails\. You can specify whether or not to wait for components to report that they can update\. You can also specify whether or not to roll back devices to their previous configuration if they apply a deployment that fails\.
+ **Stop configuration**

  Define when and how to stop a deployment\. The deployment stops and fails if the criteria that you define are met\. For example, you can configure a deployment to stop if a percentage of devices fail to apply that deployment after a minimum number of devices receive it\.
+ **Rollout configuration**

  Define the rate at which a deployments rolls out to the target devices\. You can configure an exponential rate increase with minimum and maximum rate bounds\.
+ **Timeout configuration**

  Define the maximum amount of time each device has to apply a deployment\. If a device exceeds the duration that you specify, then the device fails to apply the deployment\.

**Important**  
Custom components can define artifacts in S3 buckets\. When the AWS IoT Greengrass Core software deploys a component, it downloads the component's artifacts from the AWS Cloud\. Core device roles don't allow access to S3 buckets by default\. To deploy custom components that define artifacts in an S3 bucket, the core device role must grant permissions to download artifacts from that bucket\. For more information, see [Access to S3 buckets for component artifacts](device-service-role.md#device-service-role-access-s3-bucket)\.

**Topics**
+ [Create deployments](create-deployments.md)
+ [Revise deployments](revise-deployments.md)
+ [Cancel deployments](cancel-deployments.md)
+ [Check deployment status](check-deployment-status.md)