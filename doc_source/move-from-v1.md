# Move from AWS IoT Greengrass Version 1<a name="move-from-v1"></a>

AWS IoT Greengrass Version 2 is a new major version release of the AWS IoT Greengrass Core software, APIs, and console\. You can't use the AWS IoT Greengrass Core software v1\.x with the V2 APIs, and you can't use the AWS IoT Greengrass Core software v2\.0 with the V1 APIs\. However, by using some modifications, you can run your V1 applications on AWS IoT Greengrass V2\.

**Topics**
+ [Differences between V1 and V2](#greengrass-v1-concept-differences)
+ [Run AWS IoT Greengrass V1 applications on AWS IoT Greengrass V2](#run-v1-applications)

## Differences between V1 and V2<a name="greengrass-v1-concept-differences"></a>

AWS IoT Greengrass V2 introduces new fundamental concepts for devices, fleets, and deployable software\. This section describes the V1 concepts that are different in V2\.
+ **AWS IoT Greengrass groups and deployments**

  In AWS IoT Greengrass V1, a group defines a core device, the settings and software for that core device, and the list of AWS IoT things that connect to that core device\.

  In AWS IoT Greengrass V2, you use *deployments* to define the software components and configurations that run on core devices\. Each deployment targets a single core device or an AWS IoT thing group that can contain multiple core devices\. Deployments to thing groups are continuous, so when you add a core device to a thing group, it receives the software configuration for that fleet\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

  You can also create local deployments to develop and test custom software components\. For more information, see [Create custom AWS IoT Greengrass components](create-components.md)\.
**Note**  
AWS IoT Greengrass V2 currently doesn't support connected devices, also called Greengrass devices\.
+ **AWS IoT Greengrass Core software and connectors**

  In AWS IoT Greengrass V1, the AWS IoT Greengrass Core software is a single package that contains the software and all of its features\. AWS IoT Greengrass connectors are modules that you deploy to AWS IoT Greengrass V1 core devices\.

  In AWS IoT Greengrass V2, the AWS IoT Greengrass Core software is modular, so that you can choose what to install to control the memory footprint\. The [Greengrass nucleus component](greengrass-nucleus-component.md) is the minimum required installation of the AWS IoT Greengrass Core software that handles deployments, orchestration, and lifecycle management of other components\. Features such as stream manager, secret manager, and log manager are components that you deploy only when you need those features\. AWS IoT Greengrass V2 also provides some AWS IoT Greengrass V1 connectors as components\. For more information, see [AWS\-provided components](public-components.md)\.
+ **AWS Lambda functions**

  In AWS IoT Greengrass V1, Lambda functions define the software that runs on core devices\. In each Greengrass group, you define subscriptions and local resources that the function uses\. You also define the container parameters for functions that the AWS IoT Greengrass Core software runs in a containerized Lambda runtime environment\.

  In AWS IoT Greengrass V2, *components* are the software that run on core devices\. Components can consist of any software, and each component has a *recipe* that defines the component's metadata, parameters, dependencies, and scripts to run at each step in the component lifecycle\. The recipe also defines the component's *artifacts*, which are binary files such as scripts, compiled code, and static resources\. When you deploy a component to a core device, the core device downloads the component recipe and artifacts to run the component\. For more information, see [Manage AWS IoT Greengrass components](manage-components.md)\.

  You can import Lambda functions as components that run in a Lambda runtime environment in AWS IoT Greengrass V2\. When you import the Lambda function, you specify the subscriptions, local resources, and container parameters for the function\. For more information, see [Run AWS IoT Greengrass V1 applications on AWS IoT Greengrass V2](#run-v1-applications)\.\.
+ **Subscriptions**

  In AWS IoT Greengrass V1, subscriptions specify where Lambda functions receive event messages to consume as function payloads\. Functions subscribe to local publish/subscribe messages and AWS IoT Core MQTT messages\.

  In AWS IoT Greengrass V2, components manage their own subscriptions to local publish/subscribe and AWS IoT Core MQTT messages\. In the component recipe, you define *authorization policies* to specify which topics the component can use to communicate\. In component code, you can use interprocess communication \(IPC\) for local publish/subscribe messaging and AWS IoT Core MQTT messaging\. For more information, see [Use the AWS IoT Device SDK for interprocess communication \(IPC\)Use interprocess communication \(IPC\)](interprocess-communication.md)\.
+ **Local resources**

  In AWS IoT Greengrass V1, Lambda functions run in containers that you configure to access volumes and devices on the core device's file system\.

  In AWS IoT Greengrass V2, components run outside containers, so you don't need to specify which local resources the component can access\. You can develop components that work directly with local resources on core devices\. You can also develop components that run Docker containers\. For more information, see [Run a Docker container](run-docker-container.md)\.
**Note**  
When you import a containerized Lambda function as a component, you specify the local resources that the function uses\.

## Run AWS IoT Greengrass V1 applications on AWS IoT Greengrass V2<a name="run-v1-applications"></a>

You can run most AWS IoT Greengrass V1 applications on AWS IoT Greengrass V2\. You can use AWS\-provided components that offer the same functionality as AWS IoT Greengrass connectors, and you can import Lambda functions as components that run on AWS IoT Greengrass V2\.

**Topics**
+ [Can I run my Greengrass v1\.x applications on Greengrass v2\.0?](#greengrass-v1-feature-differences)
+ [Run V1 Lambda functions](#run-v1-lambda-functions)
+ [Run AWS IoT Greengrass connectors](#run-v1-connectors)
+ [Run machine learning inference](#run-v1-machine-learning)

### Can I run my Greengrass v1\.x applications on Greengrass v2\.0?<a name="greengrass-v1-feature-differences"></a>

AWS IoT Greengrass provides features that you can use to run your AWS IoT Greengrass Core software v1\.x applications on the AWS IoT Greengrass Core software v2\.0\. However, if your v1\.x applications use any of the following listed features, you won't be able to run them on the v2\.0 software yet\.
+ Connected devices \(also called Greengrass aware devices\) and MQTT over the local network
+ Device shadows and shadow synchronization
+ Hardware security integration
+ Stream manager telemetry metrics
+ The following languages in the AWS IoT Greengrass Core SDK:
  + Node\.js
  + C
+ The C and C\+\+ Lambda function runtimes\.

You can [develop custom components](manage-components.md) to build any feature or runtime to run on Greengrass core devices\.

### Run V1 Lambda functions<a name="run-v1-lambda-functions"></a>

You can import Lambda functions as AWS IoT Greengrass V2 components\. If your components use features such as stream manager or local secrets, you must define dependencies on the AWS\-provided components that package these features' functionality\. When you deploy a component, the deployment includes the component dependencies that you specify\. You configure these dependent features when you deploy your Lambda function component\.

If your Lambda function uses features such as stream manager or local secrets, you must define dependencies on the AWS\-provided components that package these features\. When you deploy the Lambda function component, the deployment also includes the component for each feature that you define as a dependency\. In the deployment, you can configure parameters such as which secrets to deploy to the core device\. Not all V1 features require a component dependency for your Lambda function on V2\. The following list describes how to use V1 features on V2 in your Lambda function component\.
+ **Stream manager**

  If your Lambda function uses stream manager, specify `aws.greengrass.StreamManager` as a component dependency when you import the function\. When you deploy the stream manager component, specify the stream manager parameters to set for the target core devices\. For more information, see [Stream manager](stream-manager-component.md)\.
+ **Local secrets**

  If your Lambda function uses local secrets, specify `aws.greengrass.SecretManager` as a component dependency when you import the function\. When you deploy the secret manager component, specify the secret resources to deploy to the target core devices\. The core device's role alias must point to an IAM role that allows the core device to retrieve the secret resources to deploy\. For more information, see [Secret manager](secret-manager-component.md)\.
+ **Subscriptions**

  If your Lambda function publishes messages to the local publish/subscribe broker or to AWS IoT Core, specify `aws.greengrass.LegacySubscriptionRouter` as a component dependency when you import the function\. When you deploy the legacy subscription router component, specify the subscriptions that the Lambda function uses\. For more information, see [Legacy subscription router](legacy-subscription-router-component.md)\.
**Note**  <a name="legacy-subscription-router-requirement-note"></a>
This component is required only if your Lambda function uses the `publish()` function in the AWS IoT Greengrass Core SDK\. If you update your Lambda function code to use the interprocess communication \(IPC\)s interface in the AWS IoT Device SDK, you don't need to deploy the legacy subscription router component\. For more information, see the following [interprocess communication](interprocess-communication.md) services:  
[Publish/subscribe local messages](ipc-publish-subscribe.md)
[Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)
+ **Local volumes and devices**

  If your containerized Lambda function accesses local volumes or devices, specify those volumes and devices when you import the Lambda function\. This feature doesn't require a component dependency\.
+ **Access other AWS services**

  If your Lambda function uses AWS credentials to make requests to other AWS services, specify `aws.greengrass.TokenExchangeService` as a component dependency when you import the function\. The core device's role alias must point to an IAM role that allows the core device to perform the AWS operations that the Lambda function uses\. For more information, see [Token exchange service](token-exchange-service-component.md) and [Authorize core devices to interact with AWS services](device-service-role.md)\.

For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\.

### Run AWS IoT Greengrass connectors<a name="run-v1-connectors"></a>

You can deploy AWS\-provided components that offer the same functionality of AWS IoT Greengrass connectors\. When you create the deployment, you can configure the connectors' parameters\. For more information, see the following AWS IoT Greengrass V2 components that provide Greengrass connectors:
+ [CloudWatch metrics component](cloudwatch-metrics-component.md)
+ [AWS IoT Device Defender component](device-defender-component.md)
+ [Kinesis Data Firehose component](kinesis-firehose-component.md)
+ [Modbus\-RTU protocol adapter component](modbus-rtu-protocol-adapter-component.md)
+ [Amazon SNS component](sns-component.md)

AWS IoT Greengrass V2 doesn't provide a component to replace the Docker application deployment connector, but you can create components that run Docker containers from images\. For more information, see [Run a Docker container](run-docker-container.md)\.

### Run machine learning inference<a name="run-v1-machine-learning"></a>

AWS IoT Greengrass V2 provides sample Amazon SageMaker Neo DLR machine learning components and models\. You can use these features for image classification and object detection\. To use other machine learning frameworks, such as MXNet and TensorFlow, you can develop your own custom components that use these frameworks\.