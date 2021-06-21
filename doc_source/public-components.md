# AWS\-provided components<a name="public-components"></a>

AWS IoT Greengrass provides and maintains prebuilt components that you can deploy to your devices\. These components include features \(such as stream manager\), AWS IoT Greengrass V1 connectors \(such as CloudWatch metrics\), and local development tools \(such as the AWS IoT Greengrass CLI\)\. You can deploy these components to your devices for their standalone functionality, or you can use them as dependencies in your custom components\.

**Note**  <a name="component-nucleus-dependency-update-note"></a>
Several public components depend on specific minor versions of the Greengrass nucleus\. Because of this dependency, you need to update these public components when you update the Greengrass nucleus to a new minor version\. For information about the specific versions of the nucleus that each component depends on, see the corresponding component topic\. For more information about updating the nucleus, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| Component | Description | Depends on nucleus | [Component type](manage-components.md#component-types) | [Open source](open-source.md) | 
| --- | --- | --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | The nucleus of the AWS IoT Greengrass Core software\. Use this component to configure and update the software on your core devices\. | \- | Nucleus | [Yes](https://github.com/aws-greengrass/aws-greengrass-nucleus) | 
| <a name="client-device-auth-component-table-row"></a>[Client device auth](client-device-auth-component.md) | Enables local IoT devices, called client devices, to connect to the core device\. | Yes | Plugin | No | 
| [CloudWatch metrics](cloudwatch-metrics-component.md) | Publishes custom metrics to Amazon CloudWatch\. | Yes | Lambda | No | 
| [Device Defender](device-defender-component.md) | Notifies administrators of changes in the state of the Greengrass core device to identify unusual behavior\. | Yes | Lambda | No | 
| [Docker application manager](docker-application-manager-component.md) | Enables AWS IoT Greengrass to download Docker images from Docker Hub and Amazon Elastic Container Registry \(Amazon ECR\)\. | Yes | Generic | No | 
| [Greengrass CLI](greengrass-cli-component.md) | Provides a command\-line interface that you can use to create local deployments and interact with the Greengrass core device and its components\. | Yes | Plugin | [Yes](https://github.com/aws-greengrass/aws-greengrass-cli) | 
| <a name="ip-detector-component-table-row"></a>[IP detector](ip-detector-component.md) | Reports MQTT broker connectivity information to AWS IoT Greengrass, so client devices can discover how to connect\. | Yes | Plugin | No | 
| [Kinesis Data Firehose](kinesis-firehose-component.md) | Publishes data through Amazon Kinesis Data Firehose delivery streams to destinations in the AWS Cloud\. | Yes | Lambda | No | 
| [Lambda launcher](lambda-launcher-component.md) | Handles processes and environment configuration for Lambda functions\. | No | Generic | No | 
| [Lambda manager](lambda-manager-component.md) | Handles interprocess communication and scaling for Lambda functions\. | Yes | Plugin | No | 
| [Lambda runtimes](lambda-runtimes-component.md) | Provides artifacts for each Lambda runtime\. | No | Generic | No | 
| [Local debug console](local-debug-console-component.md) | Provides a local console that you can use to debug and manage the Greengrass core device and its components\. | Yes | Plugin | [Yes](https://github.com/aws-greengrass/aws-greengrass-localdebugconsole) | 
| [Legacy subscription router](legacy-subscription-router-component.md) | Manages subscriptions for Lambda functions that run on AWS IoT Greengrass V1\. | Yes | Generic | No | 
| [Log manager](log-manager-component.md) | Collects and uploads logs on the Greengrass core device\. | Yes | Plugin | [Yes](https://github.com/aws-greengrass/aws-greengrass-log-manager) | 
| [Machine learning components](machine-learning-components.md) | Provides machine learning models and sample inference code that you can use to perform machine learning inference on Greengrass core devices\. | See [Machine learning components](machine-learning-components.md)\. | 
| [Modbus\-RTU protocol adapter](modbus-rtu-protocol-adapter-component.md) | Polls information from local Modbus RTU devices\. | Yes | Lambda | No | 
| [SageMaker Edge Manager](sagemaker-edge-manager-component.md) | Deploys the Amazon SageMaker Edge Manager agent on the Greengrass core device\. | Yes | Generic | No | 
| <a name="mqtt-bridge-component-table-row"></a>[MQTT bridge](mqtt-bridge-component.md) | Relays MQTT messages between client devices, local AWS IoT Greengrass publish/subscribe, and AWS IoT Core\. | No | Plugin | No | 
| <a name="mqtt-broker-moquette-component-table-row"></a>[MQTT broker \(Moquette\)](mqtt-broker-moquette-component.md) | Handles MQTT messages between client devices and the core device\. | No | Plugin | No | 
| [Secret manager](secret-manager-component.md) | Deploys secrets from AWS Secrets Manager secrets so that you can securely use credentials, such as passwords, in custom components on the Greengrass core device\. | Yes | Plugin | [Yes](https://github.com/aws-greengrass/aws-greengrass-secret-manager) | 
| [Secure tunneling](secure-tunneling-component.md) | Enables AWS IoT secure tunneling connections that you can use to establish bidrectional communications with Greengrass core devices that are behind restricted firewalls\. | Yes | Generic | No | 
| [Shadow manager](shadow-manager-component.md) | Enables interaction with shadows on the core device\. It manages shadow document storage and also the synchronization of local shadow states with the AWS IoT Device Shadow service\. | Yes | Plugin | No | 
| [Amazon SNS](sns-component.md) | Publishes messages to Amazon SNS topics\. | Yes | Lambda | No | 
| [Stream manager](stream-manager-component.md) | Streams high\-volume data from local sources to the AWS Cloud\. | Yes | Plugin | No | 
| [Token exchange service](token-exchange-service-component.md) | Provides AWS credentials that you can use to interact with AWS services\. | No | Generic | No | 