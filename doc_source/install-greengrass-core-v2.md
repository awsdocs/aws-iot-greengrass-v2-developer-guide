# Install the AWS IoT Greengrass Core software<a name="install-greengrass-core-v2"></a>

AWS IoT Greengrass extends AWS to edge devices so that they can act on the data they generate, while they use the AWS Cloud for management, analytics, and durable storage\. Install the AWS IoT Greengrass Core software on edge devices to integrate with AWS IoT Greengrass and the AWS Cloud\.

**Important**  
Before you download the AWS IoT Greengrass Core software, check that your core device meets the [requirements](setting-up.md#greengrass-v2-requirements) to install and run the AWS IoT Greengrass Core software v2\.0\.

The AWS IoT Greengrass Core software includes an installer that sets up your device as a Greengrass core device\. When you run the installer, you can configure options, such as the root folder and the AWS Region to use\. You can choose to have the installer create required AWS IoT and IAM resources for you\. You can also choose to deploy local development tools to configure a device that you use for custom component development\.

The AWS IoT Greengrass Core software requires the following AWS IoT and IAM resources to connect to the AWS Cloud and operate:
+ An AWS IoT thing\. When you register a device as an AWS IoT thing, that device can use a digital certificate to authenticate with AWS\. This certificate allows the device to communicate with AWS IoT and AWS IoT Greengrass\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.
+ \(Optional\) An AWS IoT thing group\. You use thing groups to manage fleets of Greengrass core devices\. When you deploy software components to your devices, you can choose to deploy to individual devices or to groups of devices\. You can add a device to a thing group to deploy that thing group's software components to the device\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.
+ An IAM role\. Greengrass core devices use the AWS IoT Core credentials provider to authorize calls to AWS services with an IAM role\. This role allows your device to interact with AWS IoT, send logs to Amazon CloudWatch Logs, and download custom component artifacts from Amazon Simple Storage Service \(Amazon S3\)\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.
+ An AWS IoT role alias\. Greengrass core devices use the role alias to identify the IAM role to use\. The role alias enables you to change the IAM role but keep the device configuration the same\. For more information, see [Authorizing direct calls to AWS services](https://docs.aws.amazon.com/iot/latest/developerguide/authorizing-direct-aws.html) in the *AWS IoT Core Developer Guide*\.

Choose one of the following options to install the AWS IoT Greengrass Core software on your core device\.
+ **Quick installation**

  Choose this option to set up a Greengrass core device in as few steps as possible\. The installer creates the required AWS IoT and IAM resources for you\. This option requires you to provide AWS credentials to the installer to create resources in your AWS account\.

  You can't use this option to install behind a firewall or network proxy\. If your devices are behind a firewall or network proxy, consider [manual installation](manual-installation.md)\.

  For more information, see [Install AWS IoT Greengrass Core software with automatic resource provisioning](quick-installation.md)\.
+ **Manual installation**

  Choose this option to create the required AWS resources manually or to install behind a firewall or network proxy\. By using a manual installation, you don't need to give the installer permission to create resources in your AWS account, because you create the required AWS IoT and IAM resources\. You can also configure your device to connect on port 443 or through a network proxy\.

  For more information, see [Install AWS IoT Greengrass Core software with manual resource provisioning](manual-installation.md)\.
+ **Installation with AWS IoT fleet provisioning**

  Choose this option to create the required AWS resources from an AWS IoT fleet provisioning template\. You might choose this option to create similar devices in a fleet, or if you manufacture devices that your customers later activate, such as vehicles or smart home devices\. Devices use claim certificates to authenticate and provision AWS resources, including an X\.509 client certificate that the device uses to connect to the AWS Cloud for normal operation\. You can embed or flash the claim certificates into the device's hardware during manufacturing, and you can use the same claim certificate and key to provision multiple devices\. You can also configure devices to connect on port 443 or through a network proxy\.

  For more information, see [Install AWS IoT Greengrass Core software with AWS IoT fleet provisioning](fleet-provisioning.md)\.
+ **Installation with custom provisioning**

  Choose this option to develop a custom Java application that provisions the required AWS resources\. You might choose this option if you [create your own X\.509 client certificates](https://docs.aws.amazon.com/iot/latest/developerguide/device-certs-your-own.html) or if you want more control over the provisioning process\. AWS IoT Greengrass provides an interface that you can implement to exchange information between your custom provisioning application and the AWS IoT Greengrass Core software installer\.

  For more information, see [Install AWS IoT Greengrass Core software with custom resource provisioning](custom-provisioning.md)\.

AWS IoT Greengrass also provides containerized environments that run the AWS IoT Greengrass Core software\. You can use a Dockerfile to [run AWS IoT Greengrass in a Docker container](run-greengrass-docker.md)\.

**Topics**
+ [Install with automatic provisioning](quick-installation.md)
+ [Install with manual provisioning](manual-installation.md)
+ [Install with fleet provisioning](fleet-provisioning.md)
+ [Install with custom provisioning](custom-provisioning.md)
+ [Installer arguments](configure-installer.md)