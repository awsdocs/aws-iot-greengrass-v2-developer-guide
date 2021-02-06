# What is AWS IoT Greengrass?<a name="what-is-iot-greengrass"></a>

AWS IoT Greengrass is an open source Internet of Things \(IoT\) edge runtime and cloud service that helps you build, deploy and manage IoT applications on your devices\. You can use AWS IoT Greengrass to build software that enables your devices to act locally on the data that they generate, run predictions based on machine learning models, and filter and aggregate device data\. AWS IoT Greengrass enables your devices to collect and analyze data closer to where that data is generated, react autonomously to local events, and communicate securely with other devices on the local network\. Greengrass devices can also communicate securely with AWS IoT Core and export IoT data to the AWS Cloud\. You can use AWS IoT Greengrass to build edge applications using pre\-built software modules, called components, that can connect your edge devices to AWS services or third\-party services\. You can also use AWS IoT Greengrass to package and run your software using Lambda functions, Docker containers, native operating system processes, or custom runtimes of your choice\. 

The following example shows how an AWS IoT Greengrass device interacts with the AWS Cloud\.

![\[An overview of how an AWS IoT Greengrass device interacts with the AWS Cloud.\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/greengrass-overview.png)

## For first\-time users of AWS IoT Greengrass<a name="first-time-user"></a>

If you're new to AWS IoT Greengrass, we recommend that you review the following sections in this topic:
+ [How AWS IoT Greengrass works](how-it-works.md)
+ [Key concepts for AWS IoT Greengrass](how-it-works.md#concept-overview)

Next, follow the [getting started tutorial](getting-started.md) to try out the basic features of AWS IoT Greengrass\. In this tutorial, you install the AWS IoT Greengrass Core software on a device, develop a Hello World component, and package that component for deployment\.

## For existing users of AWS IoT Greengrass<a name="existing-users"></a>

For current users of AWS IoT Greengrass, we recommend the following topics to help you understand the new features in AWS IoT Greengrass Version 2, and learn how to move from version 1 to version 2:
+ [What's new in AWS IoT Greengrass Version 2](greengrass-v2-whats-new.md)
+ [Move from AWS IoT Greengrass Version 1](move-from-v1.md)