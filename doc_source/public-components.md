# AWS\-provided components<a name="public-components"></a>

AWS IoT Greengrass provides and maintains public components that you can deploy to your devices\. These components include features \(such as stream manager\), AWS IoT Greengrass V1 connectors \(such as CloudWatch metrics\), and local development tools \(such as the AWS IoT Greengrass CLI\)\. You can deploy these components to your devices for their standalone functionality, or you can use them as dependencies in your custom components\.

**Note**  
Several public components depend on specific minor versions of the Greengrass nucleus\. Because of this dependency, you need to update these public components when you update the Greengrass nucleus to a new minor version\. For information about the specific versions of the nucleus that each component depends on, see the corresponding component topic\. For more information about updating the nucleus, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.

The license for each open source component can be [found in the respective GitHub repository](https://github.com/search?q=org%3Aaws-greengrass++filename%3ALICENSE&type=Repositories&ref=advsearch&l=&l=), your use of the AWS Greengrass Core and any other components not subject to an open source agreement is governed by the [AWS Greengrass Core Software License](https://s3-us-west-2.amazonaws.com/greengrass-release-license/greengrass-license-v1.pdf


| Component | Description | Depends on nucleus | Open source | 
| --- | --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | The nucleus of the AWS IoT Greengrass Core software\. Use this component to configure and update the software on your core devices\. | \- | [Yes](https://github.com/aws-greengrass/aws-greengrass-nucleus) | 
| [CloudWatch metrics](cloudwatch-metrics-component.md) | Publishes custom metrics to Amazon CloudWatch\. | Yes | No | 
| [Device Defender](device-defender-component.md) | Notifies administrators of changes in the state of the Greengrass core device to identify unusual behavior\. | Yes | No | 
| [Docker application manager](docker-application-manager-component.md) | Enables AWS IoT Greengrass to download Docker images from Docker Hub and Amazon Elastic Container Registry \(Amazon ECR\)\. | Yes | No | 
| [Greengrass CLI](greengrass-cli-component.md) | Provides a command\-line interface that you can use to create local deployments and interact with the Greengrass core device and its components\. | Yes | [Yes](https://github.com/aws-greengrass/aws-greengrass-cli) | 
| [Kinesis Data Firehose](kinesis-firehose-component.md) | Publishes data through Amazon Kinesis Data Firehose delivery streams to destinations in the AWS Cloud\. | Yes | No | 
| [Lambda launcher](lambda-launcher-component.md) | Handles processes and environment configuration for Lambda functions\. | No | No | 
| [Lambda manager](lambda-manager-component.md) | Handles interprocess communication and scaling for Lambda functions\. | Yes | No | 
| [Lambda runtimes](lambda-runtimes-component.md) | Provides artifacts for each Lambda runtime\. | No | No | 
| [Local debug console](local-debug-console-component.md) | Provides a local console that you can use to debug and manage the Greengrass core device and its components\. | Yes | [Yes](https://github.com/aws-greengrass/aws-greengrass-localdebugconsole) | 
| [Legacy subscription router](legacy-subscription-router-component.md) | Manages subscriptions for Lambda functions that run on AWS IoT Greengrass V1\. | Yes | No | 
| [Log manager](log-manager-component.md) | Collects and uploads logs on the Greengrass core device\. | Yes | [Yes](https://github.com/aws-greengrass/aws-greengrass-log-manager) | 
| [Machine learning components](machine-learning-components.md) | Provides machine learning models and sample inference code that you can use to perform machine learning inference on Greengrass core devices\. | See [Machine learning components](machine-learning-components.md)\. | 
| [Modbus\-RTU protocol adapter](modbus-rtu-protocol-adapter-component.md) | Polls information from local Modbus RTU devices\. | Yes | No | 
| [Secret manager](secret-manager-component.md) | Deploys secrets from AWS Secrets Manager secrets so that you can securely use credentials, such as passwords, in custom components on the Greengrass core device\. | Yes | [Yes](https://github.com/aws-greengrass/aws-greengrass-secret-manager) | 
| [Secure tunneling](secure-tunneling-component.md) | Enables AWS IoT secure tunneling connections that you can use to establish bidrectional communications with Greengrass core devices that are behind restricted firewalls\. | Yes | No | 
| [Amazon SNS](sns-component.md) | Publishes messages to Amazon SNS topics\. | Yes | No | 
| [Stream manager](stream-manager-component.md) | Streams high\-volume data from local sources to the AWS Cloud\. | Yes | No | 
| [Token exchange service](token-exchange-service-component.md) | Provides AWS credentials that you can use to interact with AWS services\. | No | No | 
