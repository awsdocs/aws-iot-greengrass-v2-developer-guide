# Migrate from AWS IoT Greengrass Version 1<a name="migrate-from-v1"></a>

AWS IoT Greengrass Version 2 is a major version release of the AWS IoT Greengrass Core software, APIs, and console\. AWS IoT Greengrass V2 introduces several improvements to AWS IoT Greengrass V1, such as modular applications, deployments to large fleets of devices, and support for additional platforms\.

**Note**  
AWS IoT Greengrass Version 1 no longer receives feature updates, and will receive only security patches and bug fixes until June 30, 2023\. For more information, see the [AWS IoT Greengrass V1 maintenance policy](https://docs.aws.amazon.com/greengrass/v1/developerguide/maintenance-policy.html)\. If you use AWS IoT Greengrass V1, we strongly recommend that you migrate to AWS IoT Greengrass V2\.

Follow instructions in this guide to migrate from AWS IoT Greengrass V1 to AWS IoT Greengrass V2\.

## Can I run my V1 applications on V2?<a name="run-v1-applications-on-v2"></a>

Most V1 applications can run on V2 core devices without needing to change the application code\. If your V1 applications use any of the following features, you won't be able to run them on V2 yet\.
+ The C and C\+\+ Lambda function runtimes
+ Stream manager telemetry metrics
+ On\-disk storage cache for outbound MQTT messages

If your V1 applications use either of the following features, you must modify your application code to use the AWS IoT Device SDK V2 to run the applications on AWS IoT Greengrass V2\.
+ Interact with the local shadow service
+ Publish messages to local connected devices \(Greengrass devices\)

**Note**  
The AWS IoT Device SDK for JavaScript V2 doesn't currently support AWS IoT Greengrass V2\. If your V1 applications use either of these features and the AWS IoT Greengrass Core SDK for Node\.js, you can't run them on V2 yet\.

## Migration overview<a name="migration-overview"></a>

At a high level, you can use the following procedure to upgrade core devices from AWS IoT Greengrass V1 to AWS IoT Greengrass V2\. The exact procedure that you follow depends on the specific requirements for your environment\.

![\[An overview of how to migrate from AWS IoT Greengrass V1 to AWS IoT Greengrass V2.\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/images/migration-workflow.png)

1. 

**[Understand the differences between V1 and V2](greengrass-v1-concept-differences.md)**

   AWS IoT Greengrass V2 introduces new fundamental concepts for device fleets and deployable software, and V2 simplifies several concepts from V1\.

   The AWS IoT Greengrass V2 cloud service and AWS IoT Greengrass Core software v2\.x aren't backward compatible with the AWS IoT Greengrass V1 cloud service and AWS IoT Greengrass Core software v1\.x\. As a result, AWS IoT Greengrass V1 over\-the\-air \(OTA\) updates can't upgrade core devices from V1 to V2\.

1. 

**[Validate that V1 core devices can run V2](validate-v1-core-devices.md)**

   Validate that a V1 core device can run the AWS IoT Greengrass Core software v2\.x and AWS IoT Greengrass V2 features\. AWS IoT Greengrass V2 has different device requirements than AWS IoT Greengrass V1\.

1. 

**[Set up a new device to test V1 applications on V2](set-up-v2-test-device.md)**

   To minimize risk to your devices in production, create a new device to test your V1 applications on V2\. After you install the AWS IoT Greengrass Core software v2\.x, you can create and deploy AWS IoT Greengrass V2 components to migrate and test your AWS IoT Greengrass V1 applications\.

1. 

**[Upgrade V1 core devices to run V2](upgrade-v1-core-devices.md)**

   Upgrade an existing V1 core device to run the AWS IoT Greengrass Core software v2\.x and AWS IoT Greengrass V2 components\. To migrate a fleet of devices from V1 to V2, you repeat this step for each device in the fleet\.