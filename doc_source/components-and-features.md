# Features of AWS IoT Greengrass<a name="components-and-features"></a>

AWS IoT Greengrass Version 2 consists of the following elements:
+ **Software distributions**
  + The Greengrass nucleus component, which is the minimum installation of the AWS IoT Greengrass Core software\. This component manages deployments, orchestration, and lifecycle management of Greengrass components\.
  + Additional optional components provided by AWS that integrate with services, protocols, and software\.
  + The AWS IoT Device SDK which contains the interprocess communication \(IPC\) SDK\.
  + The Stream Manager SDK\.
+ **Cloud service**
  + AWS IoT Greengrass V2 API
  + AWS IoT Greengrass V2 console

## AWS IoT Greengrass Core software<a name="greengrass-core-software"></a>

You can use the AWS IoT Greengrass Core software that runs on your edge devices to do the following:
+ Deploy and invoke components and Lambda functions\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.
+ Process data streams on the local device with automatic exports to the AWS Cloud\. For more information, see [Manage data streams on the AWS IoT Greengrass Core](manage-data-streams.md)\.
+ Support local publish/subscribe messaging between components\. For more information, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.
+ Support MQTT messaging between AWS IoT and components\. For more information, see [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.
+ Perform secure, over\-the\-air \(OTA\) software updates of the AWS IoT Greengrass Core software and custom components\. For more information, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md) and [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.
+ Manage component lifecycles, such as with support for install and run scripts\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.
+ Provide secure, encrypted storage of local secrets and controlled access by components\. For more information, see [Secret manager](secret-manager-component.md)\.

You configure and manage Greengrass core devices through AWS IoT Greengrass APIs where you create continuous software deployments\.

For more information about supported platforms, requirements, and downloads, see [Setting up AWS IoT Greengrass Version 2](setting-up.md)\.

<a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.