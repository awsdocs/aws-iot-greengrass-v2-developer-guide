# Update the AWS IoT Greengrass Core software \(OTA\)<a name="update-greengrass-core-v2"></a>

The AWS IoT Greengrass Core software is available as a component that you can deploy to perform over\-the\-air \(OTA\) updates of the software\. This feature is built in to the AWS IoT Greengrass Core software\.

OTA updates make it more efficient to do the following:
+ Fix security vulnerabilities
+ Address software stability issues
+ Deploy new or improved features

For more information, see the [Greengrass core nucleus component](greengrass-nucleus-component.md)\.

**Topics**
+ [Requirements](#ota-update-requirements)
+ [Considerations](#ota-update-considerations)
+ [Create an OTA update](#create-ota-update)

## Requirements<a name="ota-update-requirements"></a>

The following requirements apply to deploy the nucleus component for OTA updates of the AWS IoT Greengrass Core software:
+ The Greengrass core device must have a connection to the AWS Cloud to receive the deployment\.
+ The Greengrass core device must be correctly configured and provisioned with certificates and keys for authentication with AWS IoT Core and AWS IoT Greengrass\.
+ The AWS IoT Greengrass Core software must be set up and running as a system service\. OTA updates don't work if you run the kernel from the JAR file, `Greengrass.jar`\. For more information, see [Configure AWS IoT Greengrass as a system service](configure-greengrass-core-v2.md#configure-system-service)\.

## Considerations<a name="ota-update-considerations"></a>

Before you deploy the nucleus component to update the AWS IoT Greengrass Core software, be aware of the impact on the core devices that you update:
+ The AWS IoT Greengrass Core software shuts down during the update\.
+ Components running on the core device shut down\. If those components write to local resources, they might leave those resources in an incorrect state unless shut down properly\. Components can use [interprocess communication](interprocess-communication.md) to tell the AWS IoT Greengrass Core software to defer the update until they clean up the resources that they use\.
+ While the nucleus is shut down, the core device loses its connections with the AWS Cloud and local devices\. 
+ Long\-lived Lambda functions that run as components lose their dynamic state information and drop all pending work\.

## Create an OTA update<a name="create-ota-update"></a>

To create an OTA update, [create a deployment](create-deployments.md) that includes the [nucleus component](greengrass-nucleus-component.md) and the version to install\.