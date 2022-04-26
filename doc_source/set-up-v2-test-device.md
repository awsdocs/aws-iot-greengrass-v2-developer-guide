# Set up a new V2 core device to test V1 applications<a name="set-up-v2-test-device"></a>

Set up a new AWS IoT Greengrass V2 core device to deploy and test AWS\-provided components and AWS Lambda functions for your AWS IoT Greengrass V1 applications\. You can also use this V2 core device to develop and test additional custom Greengrass components that run native processes on core devices\. After you test your applications on a V2 core device, you can upgrade your existing V1 core devices to V2 and deploy the V2 components that provide your V1 functionality\.



## Step 1: Install AWS IoT Greengrass V2 on a new device<a name="install-v2-test-device"></a>

Install the AWS IoT Greengrass Core software v2\.x on a new device\. You can follow the [getting started tutorial](getting-started.md) to set up a device and learn how to develop and deploy components\. This tutorial uses [automatic provisioning](quick-installation.md) to quickly set up a device\. When you install the AWS IoT Greengrass Core software v2\.x, specify the `--deploy-dev-tools` argument to deploy the [Greengrass CLI](greengrass-cli-component.md), so you can develop, test, and debug components directly on the device\. For more information about other installation options, including how to install the AWS IoT Greengrass Core software behind a proxy or using a hardware security module \(HSM\), see [Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\.

### \(Optional\) Enable logging to Amazon CloudWatch Logs<a name="enable-cloudwatch-logging-v2"></a>

To enable a V2 core device to upload logs to Amazon CloudWatch Logs, you can deploy the AWS\-provided [log manager component](log-manager-component.md)\. You can use CloudWatch Logs to view component logs, so you can debug and troubleshoot without access to the core device's file system\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

## Step 2: Create and deploy AWS IoT Greengrass V2 components to migrate AWS IoT Greengrass V1 applications<a name="run-v1-applications"></a>

You can run most AWS IoT Greengrass V1 applications on AWS IoT Greengrass V2\. You can import Lambda functions as components that run on AWS IoT Greengrass V2, and you can use [AWS\-provided components](public-components.md) that offer the same functionality as AWS IoT Greengrass connectors\.

You can also develop custom components to build any feature or runtime to run on Greengrass core devices\. For information about how to develop and test components locally, see [Create AWS IoT Greengrass components](create-components.md)\.

**Topics**
+ [Import V1 Lambda functions](#run-v1-lambda-functions)
+ [Use V1 connectors](#use-v1-connectors)
+ [Run Docker containers](#run-v1-docker-containers)
+ [Run machine learning inference](#run-v1-machine-learning)
+ [Connect V1 Greengrass devices](#connect-v1-greengrass-devices)
+ [Enable the local shadow service](#enable-shadow-service)
+ [Integrate with AWS IoT SiteWise](#integrate-with-iot-sitewise)

### Import V1 Lambda functions<a name="run-v1-lambda-functions"></a>

You can import Lambda functions as AWS IoT Greengrass V2 components\. Choose from the following approaches:
+ Import V1 Lambda functions directly as Greengrass components\.
+ Update your Lambda functions to use the Greengrass libraries in the AWS IoT Device SDK v2, and then import the Lambda functions as Greengrass components\.
+ Create custom components that use non\-Lambda code and the AWS IoT Device SDK v2 to implement the same functionality as your Lambda functions\.

If your Lambda function uses features, such as stream manager or local secrets, you must define dependencies on the AWS\-provided components that package these features\. When you deploy the Lambda function component, the deployment also includes the component for each feature that you define as a dependency\. In the deployment, you can configure parameters, such as which secrets to deploy to the core device\. Not all V1 features require a component dependency for your Lambda function on V2\. The following list describes how to use V1 features in your V2 Lambda function component\.
+ **Access other AWS services**

  If your Lambda function uses AWS credentials to make requests to other AWS services, the core device's token exchange role must allow the core device to perform the AWS operations that the Lambda function uses\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.
+ **Stream manager**

  If your Lambda function uses stream manager, specify `aws.greengrass.StreamManager` as a component dependency when you import the function\. When you deploy the stream manager component, specify the stream manager parameters to set for the target core devices\. The core device's token exchange role must allow the core device to access the AWS Cloud destinations that you use with stream manager\. For more information, see [Stream manager](stream-manager-component.md)\.
+ **Local secrets**

  If your Lambda function uses local secrets, specify `aws.greengrass.SecretManager` as a component dependency when you import the function\. When you deploy the secret manager component, specify the secret resources to deploy to the target core devices\. The core device's token exchange role must allow the core device to retrieve the secret resources to deploy\. For more information, see [Secret manager](secret-manager-component.md)\.

  When you deploy your Lambda function component, configure it to have an [IPC authorization policy](interprocess-communication.md#ipc-authorization-policies) that grants permission to use the [GetSecretValue IPC operation](ipc-secret-manager.md) in the AWS IoT Device SDK V2\.
+ **Local shadows**

  If your Lambda function interacts with local shadows, you must update the Lambda function code to use the AWS IoT Device SDK V2\. You must also specify `aws.greengrass.ShadowManager` as a component dependency when you import the function\. For more information, see [Interact with device shadows](interact-with-shadows.md)\.

  When you deploy your Lambda function component, configure it to have an [IPC authorization policy](interprocess-communication.md#ipc-authorization-policies) that grants permission to use the [shadow IPC operations](ipc-local-shadows.md) in the AWS IoT Device SDK V2\.
+ **Subscriptions**
  + If your Lambda function subscribes to messages from a cloud source, specify those subscriptions as event sources when you import the function\.
  + If your Lambda function subscribes to messages from another Lambda function, or if your Lambda function publishes messages to AWS IoT Core or other Lambda functions, configure and deploy the [legacy subscription router component](legacy-subscription-router-component.md) when you deploy your Lambda function\. When you deploy the legacy subscription router component, specify the subscriptions that the Lambda function uses\.
**Note**  <a name="legacy-subscription-router-requirement-note"></a>
The legacy subscription router component is required only if your Lambda function uses the `publish()` function in the AWS IoT Greengrass Core SDK\. If you update your Lambda function code to use the interprocess communication \(IPC\) interface in the AWS IoT Device SDK V2, you don't need to deploy the legacy subscription router component\. For more information, see the following [interprocess communication](interprocess-communication.md) services:  
[Publish/subscribe local messages](ipc-publish-subscribe.md)
[Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)
  + If your Lambda function subscribes to messages from local connected devices, specify those subscriptions as event sources when you import the function\. You must also configure and deploy the [MQTT bridge component](mqtt-bridge-component.md) to relay messages from the connected devices to the local publish/subscribe topics that you specify as event sources\.
  + If your Lambda function publishes messages to local connected devices, you must update the Lambda function code to use the AWS IoT Device SDK V2 to [publish local publish/subscribe messages](ipc-publish-subscribe.md)\. You must also configure and deploy the [MQTT bridge component](mqtt-bridge-component.md) to relay messages from the local publish/subscribe message broker to the connected devices\.
+ **Local volumes and devices**

  If your containerized Lambda function accesses local volumes or devices, specify those volumes and devices when you import the Lambda function\. This feature doesn't require a component dependency\.

For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\.

### Use V1 connectors<a name="use-v1-connectors"></a>

You can deploy AWS\-provided components that offer the same functionality of some AWS IoT Greengrass connectors\. When you create the deployment, you can configure the connectors' parameters\. 

The following AWS IoT Greengrass V2 components provide Greengrass V1 connector functionality:
+ [CloudWatch metrics component](cloudwatch-metrics-component.md)
+ [AWS IoT Device Defender component](device-defender-component.md)
+ [Kinesis Data Firehose component](kinesis-firehose-component.md)
+ [Modbus\-RTU protocol adapter component](modbus-rtu-protocol-adapter-component.md)
+ [Amazon SNS component](sns-component.md)

### Run Docker containers<a name="run-v1-docker-containers"></a>

AWS IoT Greengrass V2 doesn't provide a component to directly replace the V1 Docker application deployment connector\. However, you can use the Docker application manager component to download Docker images, and then create custom components that run Docker containers from the downloaded images\. For more information, see [Run a Docker container](run-docker-container.md) and [Docker application manager](docker-application-manager-component.md)\.

### Run machine learning inference<a name="run-v1-machine-learning"></a>

AWS IoT Greengrass V2 provides an Amazon SageMaker Edge Manager component that installs the Amazon SageMaker Edge Manager agent and enables you to use SageMaker Neo\-compiled models as model components on Greengrass core devices\. AWS IoT Greengrass V2 also provides components that install [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) and [TensorFlow Lite](https://www.tensorflow.org/lite/guide/python) on your device\. You can use the corresponding DLR and TensorFlow Lite model and inference components to perform sample image classification and object detection inference\. To use other machine learning frameworks, such as MXNet and TensorFlow, you can develop your own custom components that use these frameworks\.

### Connect V1 Greengrass devices<a name="connect-v1-greengrass-devices"></a>

Connected devices in AWS IoT Greengrass V1 are called client devices in AWS IoT Greengrass V2\. AWS IoT Greengrass V2 support for client devices is backward\-compatible with AWS IoT Greengrass V1, so you can connect V1 client devices to V2 core devices without changing their application code\. To enable client devices to connect to a V2 core device, deploy Greengrass components that enable client device support, and associate the client devices to the core device\. To relay messages between client devices, the AWS IoT Core cloud service, and Greengrass components \(including Lambda functions\), deploy and configure the [MQTT bridge component](mqtt-bridge-component.md)\. You can deploy the [IP detector component](ip-detector-component.md) to automatically detect connectivity information, or you can manually manage endpoints\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

### Enable the local shadow service<a name="enable-shadow-service"></a>

In AWS IoT Greengrass V2, the local shadow service is implemented by the AWS\-provided shadow manager component\. AWS IoT Greengrass V2 also includes support for named shadows\. To enable your components to interact with local shadows and to sync shadow states to AWS IoT Core, configure and deploy the shadow manager component, and use the shadow IPC operations in your component code\. For more information, see [Interact with device shadows](interact-with-shadows.md)\. 

### Integrate with AWS IoT SiteWise<a name="integrate-with-iot-sitewise"></a>

If you use your V1 core device as an AWS IoT SiteWise gateway, [follow instructions](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/configure-gateway-ggv2.html) to set up your new V2 core device as an AWS IoT SiteWise gateway\. AWS IoT SiteWise provides an installation script that deploys the AWS IoT SiteWise components for you\.

## Step 3: Test your AWS IoT Greengrass V2 applications<a name="test-v2-features"></a>

After you create and deploy V2 components to your new V2 core device, verify that your applications meet your expectations\. You can check the device's logs to view your components' standard output \(stdout\) and standard error \(stderr\) messages\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

If you deployed the [Greengrass CLI](greengrass-cli-component.md) to the core device, you can use it to debug components and their configurations\. For more information, see [Greengrass CLI commands](gg-cli-reference.md)\.

After you verify that your applications work on a V2 core device, you can deploy your application's Greengrass components to other core devices\. If you developed custom components that run native processes or Docker containers, you must first [publish those components](publish-components.md) to the AWS IoT Greengrass service to deploy them to other core devices\.