# What is AWS IoT Greengrass?<a name="what-is-iot-greengrass"></a>

AWS IoT Greengrass is an open source Internet of Things \(IoT\) edge runtime and cloud service that helps you build, deploy and manage IoT applications on your devices\. You can use AWS IoT Greengrass to build software that enables your devices to act locally on the data that they generate, run predictions based on machine learning models, and filter and aggregate device data\. AWS IoT Greengrass enables your devices to collect and analyze data closer to where that data is generated, react autonomously to local events, and communicate securely with other devices on the local network\. Greengrass devices can also communicate securely with AWS IoT Core and export IoT data to the AWS Cloud\. You can use AWS IoT Greengrass to build edge applications using pre\-built software modules, called components, that can connect your edge devices to AWS services or third\-party services\. You can also use AWS IoT Greengrass to package and run your software using Lambda functions, Docker containers, native operating system processes, or custom runtimes of your choice\. 

The following example shows how an AWS IoT Greengrass device interacts with the AWS Cloud\.

![\[An overview of how an AWS IoT Greengrass device interacts with the AWS Cloud.\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/greengrass-overview.png)

## For first\-time users of AWS IoT Greengrass<a name="first-time-user"></a>

If you're new to AWS IoT Greengrass, we recommend that you review the following sections in this topic:
+ [How AWS IoT Greengrass works](#how-it-works)
+ [Key concepts for AWS IoT Greengrass](#concept-overview)

Next, follow the [getting started tutorial](getting-started.md) to try out the basic features of AWS IoT Greengrass\. In this tutorial, you install the AWS IoT Greengrass Core software on a device, develop a Hello World component, and package that component for deployment\.

## For existing users of AWS IoT Greengrass<a name="existing-users"></a>

For current users of AWS IoT Greengrass, we recommend the following topics to help you understand the new features in AWS IoT Greengrass Version 2, and learn how to move from version 1 to version 2:
+ [What's new in AWS IoT Greengrass Version 2](greengrass-v2-whats-new.md)
+ [Move from AWS IoT Greengrass Version 1](move-from-v1.md)

## How AWS IoT Greengrass works<a name="how-it-works"></a>

The AWS IoT Greengrass client software, also called AWS IoT Greengrass Core software, runs on Linux\-based distributions, such as Ubuntu or Raspberry Pi OS, for devices with ARM or x86 architectures\. With AWS IoT Greengrass, you can program devices to act locally on the data they generate, run predictions based on machine learning models, and filter and aggregate device data\. AWS IoT Greengrass enables local execution of AWS Lambda functions, Docker containers, native OS processes, or custom runtimes of your choice\. 

AWS IoT Greengrass provides pre\-built software modules called components that let you easily extend edge device functionality\. AWS IoT Greengrass components enable you to connect to AWS services and third\-party applications at the edge\. After you develop your IoT applications, AWS IoT Greengrass enables you to remotely deploy, configure, and manage those applications on your fleet of devices in the field\.

The following example shows how an AWS IoT Greengrass device interacts with the AWS IoT Greengrass cloud service and other AWS services in the AWS Cloud\.

![\[An overview of how an AWS IoT Greengrass device interacts with the AWS IoT Greengrass cloud service and other AWS services in the AWS Cloud.\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/how-it-works.png)

## Key concepts for AWS IoT Greengrass<a name="concept-overview"></a>

The following are essential concepts for understanding and using AWS IoT Greengrass: 

****Greengrass core device****  
A device that runs the AWS IoT Greengrass Core software\. A Greengrass core device is an AWS IoT thing\. You can add multiple core devices to AWS IoT thing groups to create groups of Greengrass core devices\.

****Greengrass component****  
A software module that is deployed to and runs on a Greengrass core device\. All software that is developed and deployed with AWS IoT Greengrass is modeled as a component\. AWS IoT Greengrass provides pre\-built public components that provide features and functionality that you can use in your applications\. You can also develop your own custom components, on your local device or in the cloud\. After you develop a custom component, you can use the AWS IoT Greengrass cloud service to deploy it to single or multiple core devices\. You can create a custom component and deploy that component to a core device\. When you do, the core device downloads the following resources to run the component:  
+ **Recipe**: A JSON or YAML file that describes the software module by defining component details, configuration, and parameters\. 
+ **Artifact**: The source code, binaries, or scripts that define the software that will run on your device\. You can create artifacts from scratch, or you can create a component using a Lambda function, a Docker container, or a custom runtime\. 
+ **Dependency**: The relationship between components that enables you to enforce automatic updates or restarts of dependent components\. For example, you can have a secure message processing component dependent on an encryption component\. This ensures that any updates to the encryption component automatically initiate an update and restart of the message processing component\. 
For more information, see [Manage AWS IoT Greengrass components](manage-components.md)\.

****Deployment****  
The process to send components and apply the desired component configuration to a destination target device, which can be a single Greengrass core device or a group of Greengrass core devices\. Deployments automatically apply any updated component configurations to the target and include any other components that are defined as dependencies\. You can also clone an existing deployment to create a new deployment that uses the same components but is deployed to a different target\. Deployments are continuous, which means that any updates you make to the components or the component configuration of a deployment automatically get sent to all destination targets\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

****AWS IoT Greengrass Core software****  
The superset of all AWS IoT Greengrass software that you install on a core device\. AWS IoT Greengrass Core software is comprised of the following:   
+ **Nucleus**: The required component includes the minimum functionality of the AWS IoT Greengrass Core software\. The nucleus manages deployments, orchestration, and lifecycle management of other components\. It also facilitates interprocess communication between AWS IoT Greengrass components locally on an individual device\. For more information, see [Greengrass nucleus](greengrass-nucleus-component.md)\. 
+ **Optional components**: Configurable components provided by AWS IoT Greengrass that enable additional features on your edge devices\. Depending on your requirements, you can choose the optional components that you want to deploy to your device, such as data streaming, local machine learning inference, or a local command line interface\. For more information, see [AWS\-provided components](public-components.md)\.
You can upgrade your AWS IoT Greengrass Core software by deploying new versions of your components to your device\.