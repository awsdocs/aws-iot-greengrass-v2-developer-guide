# How AWS IoT Greengrass works<a name="how-it-works"></a>

The AWS IoT Greengrass client software, also called AWS IoT Greengrass Core software, runs on Windows and Linux\-based distributions, such as Ubuntu or Raspberry Pi OS, for devices with ARM or x86 architectures\. With AWS IoT Greengrass, you can program devices to act locally on the data they generate, run predictions based on machine learning models, and filter and aggregate device data\. AWS IoT Greengrass enables local execution of AWS Lambda functions, Docker containers, native OS processes, or custom runtimes of your choice\. 

AWS IoT Greengrass provides pre\-built software modules called components that let you easily extend edge device functionality\. AWS IoT Greengrass components enable you to connect to AWS services and third\-party applications at the edge\. After you develop your IoT applications, AWS IoT Greengrass enables you to remotely deploy, configure, and manage those applications on your fleet of devices in the field\.

The following example shows how an AWS IoT Greengrass device interacts with the AWS IoT Greengrass cloud service and other AWS services in the AWS Cloud\.

![\[An overview of how an AWS IoT Greengrass device interacts with the AWS IoT Greengrass cloud service and other AWS services in the AWS Cloud.\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/how-it-works.png)

## Key concepts for AWS IoT Greengrass<a name="concept-overview"></a>

The following are essential concepts for understanding and using AWS IoT Greengrass: 

****Greengrass core device****  
A device that runs the AWS IoT Greengrass Core software\. A Greengrass core device is an AWS IoT thing\. You can add multiple core devices to AWS IoT thing groups to create and manage groups of Greengrass core devices\. For more information, see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.

****Greengrass client device****  
A device that connects to and communicates with a Greengrass core device over MQTT\. A Greengrass client device is an AWS IoT thing\. The core device can process, filter, and aggregate data from client devices that connect to it\. You can configure the core device to relay MQTT messages between client devices, the AWS IoT Core cloud service, and Greengrass components\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.  
Client devices can run [FreeRTOS](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-lib-gg-connectivity.html) or use the [AWS IoT Device SDK](https://docs.aws.amazon.com/iot/latest/developerguide/iot-sdks.html) or [Greengrass discovery API](greengrass-discover-api.md) to get information about core devices to which they can connect\.

****Greengrass component****  
A software module that is deployed to and runs on a Greengrass core device\. All software that is developed and deployed with AWS IoT Greengrass is modeled as a component\. AWS IoT Greengrass provides pre\-built public components that provide features and functionality that you can use in your applications\. You can also develop your own custom components, on your local device or in the cloud\. After you develop a custom component, you can use the AWS IoT Greengrass cloud service to deploy it to single or multiple core devices\. You can create a custom component and deploy that component to a core device\. When you do, the core device downloads the following resources to run the component:  
+ **Recipe**: A JSON or YAML file that describes the software module by defining component details, configuration, and parameters\. 
+ **Artifact**: The source code, binaries, or scripts that define the software that will run on your device\. You can create artifacts from scratch, or you can create a component using a Lambda function, a Docker container, or a custom runtime\. 
+ **Dependency**: The relationship between components that enables you to enforce automatic updates or restarts of dependent components\. For example, you can have a secure message processing component dependent on an encryption component\. This ensures that any updates to the encryption component automatically update and restart the message processing component\. 
For more information, see [AWS\-provided components](public-components.md) and [Develop AWS IoT Greengrass components](develop-greengrass-components.md)\.

****Deployment****  
The process to send components and apply the desired component configuration to a destination target device, which can be a single Greengrass core device or a group of Greengrass core devices\. Deployments automatically apply any updated component configurations to the target and include any other components that are defined as dependencies\. You can also clone an existing deployment to create a new deployment that uses the same components but is deployed to a different target\. Deployments are continuous, which means that any updates you make to the components or the component configuration of a deployment automatically get sent to all destination targets\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

****AWS IoT Greengrass Core software****  
The set of all AWS IoT Greengrass software that you install on a core device\. AWS IoT Greengrass Core software comprises the following:   
+ **Nucleus**: This required component provides the minimum functionality of the AWS IoT Greengrass Core software\. The nucleus manages deployments, orchestration, and lifecycle management of other components\. It also facilitates communication between AWS IoT Greengrass components locally on an individual device\. For more information, see [Greengrass nucleus](greengrass-nucleus-component.md)\. 
+ **Optional components**: These configurable components are provided by AWS IoT Greengrass and enable additional features on your edge devices\. Depending on your requirements, you can choose the optional components that you want to deploy to your device, such as data streaming, local machine learning inference, or a local command line interface\. For more information, see [AWS\-provided components](public-components.md)\.
You can upgrade your AWS IoT Greengrass Core software by deploying new versions of your components to your device\.

## Features of AWS IoT Greengrass<a name="components-and-features"></a>

AWS IoT Greengrass Version 2 consists of the following elements:
+ **Software distributions**
  + The Greengrass nucleus component, which is the minimum installation of the AWS IoT Greengrass Core software\. This component manages deployments, orchestration, and lifecycle management of Greengrass components\.
  + Additional optional components provided by AWS that integrate with services, protocols, and software\.
  + The AWS IoT Device SDK, which contains the interprocess communication \(IPC\) SDK and the Greengrass discovery SDK for client devices\.
  + The Stream Manager SDK\.
+ **Cloud service**
  + AWS IoT Greengrass V2 API
  + AWS IoT Greengrass V2 console

### AWS IoT Greengrass Core software<a name="greengrass-core-software"></a>

You can use the AWS IoT Greengrass Core software that runs on your edge devices to do the following:
+ Process data streams on the local device with automatic exports to the AWS Cloud\. For more information, see [Manage data streams on the AWS IoT Greengrass Core](manage-data-streams.md)\.
+ Support MQTT messaging between AWS IoT and components\. For more information, see [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.
+ Interact with local devices that connect and communicate over MQTT\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.
+ Support local publish and subscribe messaging between components\. For more information, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.
+ Deploy and invoke components and Lambda functions\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.
+ Manage component lifecycles, such as with support for install and run scripts\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.
+ Perform secure, over\-the\-air \(OTA\) software updates of the AWS IoT Greengrass Core software and custom components\. For more information, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md) and [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.
+ Provide secure, encrypted storage of local secrets and controlled access by components\. For more information, see [Secret manager](secret-manager-component.md)\.
+ Secure connections between devices and the AWS Cloud with device authentication and authorization\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.

You configure and manage Greengrass core devices through AWS IoT Greengrass APIs where you create continuous software deployments\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

Some features are supported on only certain platforms\. For more information, see [Greengrass feature compatibility by operating system](operating-system-feature-support-matrix.md)\.

For more information about supported platforms, requirements, and downloads, see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.

<a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.