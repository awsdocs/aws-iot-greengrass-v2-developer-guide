# AWS\-provided components<a name="public-components"></a>

AWS IoT Greengrass provides and maintains public components that you can deploy to your devices\. These components include features \(such as stream manager\), AWS IoT Greengrass V1 connectors \(such as CloudWatch metrics\), and local development tools \(such as the AWS IoT Greengrass CLI\)\. You can deploy these components to your devices for their standalone functionality, or you can use them as dependencies in your custom components\.


| Component | Description | Component name | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | The nucleus of the AWS IoT Greengrass Core software\. Use this component to configure and update the software on your core devices\. | aws\.greengrass\.Nucleus | 
| [CloudWatch metrics](cloudwatch-metrics-component.md) | Publishes custom metrics to Amazon CloudWatch\. | aws\.greengrass\.CloudWatch | 
| [Device Defender](device-defender-component.md) | Notifies administrators of changes in the state of the Greengrass core device to identify unusual behavior\. | aws\.greengrass\.DeviceDefender | 
| [Greengrass CLI](greengrass-cli-component.md) | Provides a command\-line interface that you can use to create local deployments and interact with the Greengrass core device and its components\. | aws\.greengrass\.Cli | 
| [Kinesis Data Firehose](kinesis-firehose-component.md) | Publishes data through Amazon Kinesis Data Firehose delivery streams to destinations in the AWS Cloud\. | aws\.greengrass\.KinesisFirehose | 
| [Lambda launcher](lambda-launcher-component.md) | Handles processes and environment configuration for Lambda functions\. | aws\.greengrass\.LambdaLauncher | 
| [Lambda manager](lambda-manager-component.md) | Handles interprocess communication and scaling for Lambda functions\. | aws\.greengrass\.LambdaManager | 
| [Lambda runtimes](lambda-runtimes-component.md) | Provides artifacts for each Lambda runtime\. | aws\.greengrass\.LambdaRuntimes | 
| [Local debug console](local-debug-console-component.md) | Provides a local console that you can use to debug and manage the Greengrass core device and its components\. | aws\.greengrass\.LocalDebugConsole | 
| [Legacy subscription router](legacy-subscription-router-component.md) | Manages subscriptions for Lambda functions that run on AWS IoT Greengrass V1\. | aws\.greengrass\.LegacySubscriptionRouter | 
| [Log manager](log-manager-component.md) | Collects and uploads logs on the Greengrass core device\. | aws\.greengrass\.LogManager | 
| [Modbus\-RTU protocol adapter](modbus-rtu-protocol-adapter-component.md) | Polls information from local Modbus RTU devices\. | aws\.greengrass\.Modbus | 
| [Secret manager](secret-manager-component.md) | Deploys secrets from AWS Secrets Manager secrets so that you can securely use credentials, such as passwords, in custom components on the Greengrass core device\. | aws\.greengrass\.SecretManager | 
| [Secure tunneling](secure-tunneling-component.md) | Enables AWS IoT secure tunneling connections that you can use to establish bidrectional communications with Greengrass core devices that are behind restricted firewalls\. | aws\.greengrass\.SecureTunneling | 
| [Amazon SNS](sns-component.md) | Publishes messages to Amazon SNS topics\. | aws\.greengrass\.SNS | 
| [Stream manager](stream-manager-component.md) | Streams high\-volume data from local sources to the AWS Cloud\. | aws\.greengrass\.StreamManager | 
| [Token exchange service](token-exchange-service-component.md) | Provides AWS credentials that you can use to interact with AWS services\. | aws\.greengrass\.TokenExchangeService | 