# Kinesis Data Firehose<a name="kinesis-firehose-component"></a>

The Kinesis Data Firehose component \(`aws.greengrass.KinesisFirehose`\) publishes data through Amazon Kinesis Data Firehose delivery streams to destinations, such as Amazon S3, Amazon Redshift, and Amazon OpenSearch Service\. For more information, see [What is Amazon Kinesis Data Firehose?](https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html) in the *Amazon Kinesis Data Firehose Developer Guide*\.

To publish to a Kinesis delivery stream with this component, publish a message to a topic where this component subscribes\. By default, this component subscribes to the `kinesisfirehose/message` and `kinesisfirehose/message/binary/#` [local publish/subscribe](ipc-publish-subscribe.md) topics\. You can specify other topics, including AWS IoT Core MQTT topics, when you deploy this component\.

**Note**  
This component provides similar functionality to the Kinesis Data Firehose connector in AWS IoT Greengrass V1\. For more information, see [Kinesis Data Firehose connector](https://docs.aws.amazon.com/greengrass/latest/developerguide/kinesis-firehose-connector.html) in the *AWS IoT Greengrass V1 Developer Guide*\.

**Topics**
+ [Versions](#kinesis-firehose-component-versions)
+ [Type](#kinesis-firehose-component-type)
+ [Requirements](#kinesis-firehose-component-requirements)
+ [Dependencies](#kinesis-firehose-component-dependencies)
+ [Configuration](#kinesis-firehose-component-configuration)
+ [Input data](#kinesis-firehose-component-input-data)
+ [Output data](#kinesis-firehose-component-output-data)
+ [Local log file](#kinesis-firehose-component-log-file)
+ [Licenses](#kinesis-firehose-component-licenses)
+ [Changelog](#kinesis-firehose-component-changelog)
+ [See also](#kinesis-firehose-component-see-also)

## Versions<a name="kinesis-firehose-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="kinesis-firehose-component-type"></a>

<a name="public-component-type-lambda"></a>This component is a Lambda component \(`aws.greengrass.lambda`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component's Lambda function using the [Lambda launcher component](lambda-launcher-component.md)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Requirements<a name="kinesis-firehose-component-requirements"></a>

This component has the following requirements:
+ <a name="core-device-lambda-function-requirements"></a>Your core device must meet the requirements to run Lambda functions\. If you want the core device to run containerized Lambda functions, the device must meet the requirements to do so\. For more information, see [Requirements to run Lambda functions](setting-up.md#greengrass-v2-lambda-requirements)\.
+ <a name="public-component-python3-requirement"></a>[Python](https://www.python.org/) version 3\.7 installed on the core device and added to the PATH environment variable\.
+ The [Greengrass device role](device-service-role.md) must allow the `firehose:PutRecord` and `firehose:PutRecordBatch` actions, as shown in the following example IAM policy\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "firehose:PutRecord",
          "firehose:PutRecordBatch"
        ],
        "Effect": "Allow",
        "Resource": [
          "arn:aws:firehose:region:account-id:deliverystream/stream-name"
        ]
      }
    ]
  }
  ```

  You can dynamically override the default delivery stream in the input message payload for this component\. If your application uses this feature, the IAM policy must include all target streams as resources\. You can grant granular or conditional access to resources \(for example, by using a wildcard `*` naming scheme\)\.
+ <a name="connector-component-legacy-subscription-router-dependency"></a>To receive output data from this component, you must merge the following configuration update for the [legacy subscription router component](legacy-subscription-router-component.md) when you deploy this component\. The legacy subscription router component \(`aws.greengrass.LegacySubscriptionRouter`\) is a dependency of this component\. This configuration specifies the topic where this component publishes responses\.

------
#### [ Legacy subscription router v2\.1\.x ]

  ```
  {
    "subscriptions": {
      "aws-greengrass-kinesisfirehose": {
        "id": "aws-greengrass-kinesisfirehose",
        "source": "component:aws.greengrass.KinesisFirehose",
        "subject": "kinesisfirehose/message/status",
        "target": "cloud"
      }
    }
  }
  ```

------
#### [ Legacy subscription router v2\.0\.x ]

  ```
  {
    "subscriptions": {
      "aws-greengrass-kinesisfirehose": {
        "id": "aws-greengrass-kinesisfirehose",
        "source": "arn:aws:lambda:region:aws:function:aws-greengrass-kinesisfirehose:version",
        "subject": "kinesisfirehose/message/status",
        "target": "cloud"
      }
    }
  }
  ```<a name="connector-component-legacy-subscription-router-dependency-replace"></a>
  + Replace *region* with the AWS Region that you use\.
  + Replace *version* with the version of the Lambda function that this component runs\. To find the Lambda function version, you must view the recipe for the version of this component that you want to deploy\. Open this component's details page in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass), and look for the **Lambda function** key\-value pair\. This key\-value pair contains the name and version of the Lambda function\.

**Important**  <a name="connector-component-legacy-subscription-router-dependency-note"></a>
You must update the Lambda function version on the legacy subscription router every time you deploy this component\. This ensures that you use the correct Lambda function version for the component version that you deploy\.

------

  <a name="connector-component-create-deployments"></a>For more information, see [Create deployments](create-deployments.md)\.

### Endpoints and ports<a name="kinesis-firehose-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `firehose.region.amazonaws.com`  | 443 | Yes |  Upload data to Kinesis Data Firehose\.  | 

## Dependencies<a name="kinesis-firehose-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#kinesis-firehose-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.0\.7 ]

The following table lists the dependencies for version 2\.0\.7 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.5\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

------
#### [ 2\.0\.6 ]

The following table lists the dependencies for version 2\.0\.6 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.4\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

------
#### [ 2\.0\.5 ]

The following table lists the dependencies for version 2\.0\.5 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.3\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

------
#### [ 2\.0\.4 ]

The following table lists the dependencies for version 2\.0\.4 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.2\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

------
#### [ 2\.0\.3 ]

The following table lists the dependencies for version 2\.0\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.3 <2\.1\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | >=1\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | >=1\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | >=1\.0\.0  | Hard | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="kinesis-firehose-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

**Note**  <a name="connector-component-lambda-parameters"></a>
This component's default configuration includes Lambda function parameters\. We recommend that you edit only the following parameters to configure this component on your devices\.

`lambdaParams`  
An object that contains the parameters for this component's Lambda function\. This object contains the following information:    
`EnvironmentVariables`  
An object that contains the Lambda function's parameters\. This object contains the following information:    
`DEFAULT_DELIVERY_STREAM_ARN`  
The ARN of the default Kinesis Data Firehose delivery stream where the component sends data\. You can override the destination stream with the `delivery_stream_arn` property in the input message payload\.  
The core device role must allow the required actions on all target delivery streams\. For more information, see [Requirements](#kinesis-firehose-component-requirements)\.  
`PUBLISH_INTERVAL`  
\(Optional\) The maximum number of seconds to wait before the component publishes batched data to Kinesis Data Firehose\. To configure the component to publish metrics as it receives them, which means without batching, specify `0`\.  
This value can be at most 900 seconds\.  
Default: 10 seconds  
`DELIVERY_STREAM_QUEUE_SIZE`  
\(Optional\) The maximum number of records to retain in memory before the component rejects new records for the same delivery stream\.  
This value must be at least 2,000 records\.  
Default: 5,000 records

`containerMode`  <a name="connector-component-container-mode-parameter"></a>
\(Optional\) The containerization mode for this component\. Choose from the following options:  <a name="lambda-function-component-container-mode-parameter"></a>
+ `NoContainer` – The component doesn't run in an isolated runtime environment\.
+ `GreengrassContainer` – The component runs in an isolated runtime environment inside the AWS IoT Greengrass container\.
Default: `GreengrassContainer`

`containerParams`  
<a name="connector-component-container-params-description"></a>\(Optional\) An object that contains the container parameters for this component\. The component uses these parameters if you specify `GreengrassContainer` for `containerMode`\.  
This object contains the following information:    
`memorySize`  
<a name="connector-component-container-params-memory-size-description"></a>\(Optional\) The amount of memory \(in kilobytes\) to allocate to the component\.  
Defaults to 64 MB \(65,535 KB\)\.

`pubsubTopics`  <a name="connector-component-pubsub-topics-parameter"></a>
\(Optional\) An object that contains the topics where the component subscribes to receive messages\. You can specify each topic and whether the component subscribes to MQTT topics from AWS IoT Core or local publish/subscribe topics\.  
This object contains the following information:    
`0` – This is an array index as a string\.  
An object that contains the following information:    
`type`  
\(Optional\) The type of publish/subscribe messaging that this component uses to subscribe to messages\. Choose from the following options:  
+ `Pubsub` – Subscribe to local publish/subscribe messages\. If you choose this option, the topic can't contain MQTT wildcards\. For more information about how to send messages from custom component when you specify this option, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.
+ `IotCore` – Subscribe to AWS IoT Core MQTT messages\. If you choose this option, the topic can contain MQTT wildcards\. For more information about how to send messages from custom components when you specify this option, see [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.
Default: `Pubsub`  
`topic`  
\(Optional\) The topic to which the component subscribes to receive messages\. If you specify `IotCore` for `type`, you can use MQTT wildcards \(`+` and `#`\) in this topic\.

**Example: Configuration merge update \(container mode\)**  

```
{
  "lambdaExecutionParameters": {
    "EnvironmentVariables": {
      "DEFAULT_DELIVERY_STREAM_ARN": "arn:aws:firehose:us-west-2:123456789012:deliverystream/mystream"
    }
  },
  "containerMode": "GreengrassContainer"
}
```

**Example: Configuration merge update \(no container mode\)**  

```
{
  "lambdaExecutionParameters": {
    "EnvironmentVariables": {
      "DEFAULT_DELIVERY_STREAM_ARN": "arn:aws:firehose:us-west-2:123456789012:deliverystream/mystream"
    }
  },
  "containerMode": "NoContainer"
}
```

## Input data<a name="kinesis-firehose-component-input-data"></a>

This component accepts stream content on the following topics and sends the content to the target delivery stream\. The component accepts two types of input data:
+ JSON data on the `kinesisfirehose/message` topic\.
+ Binary data on the `kinesisfirehose/message/binary/#` topic\.

**Default topic for JSON data \(local publish/subscribe\):** `kinesisfirehose/message`  
<a name="connector-component-input-properties"></a>The message accepts the following properties\. Input messages must be in JSON format\.    
`request`  
The data to send to the delivery stream and the target delivery stream, if different from the default stream\.  
Type: `object` that contains the following information:    
`data`  
The data to send to the delivery stream\.  
Type: `string`  
`delivery_stream_arn`  
\(Optional\) The ARN of the target Kinesis Data Firehose delivery stream\. Specify this property to override the default delivery stream\.  
Type: `string`  
`id`  <a name="connector-component-input-property-id"></a>
An arbitrary ID for the request\. Use this property to map an input request to an output response\. When you specify this property, the component sets the `id` property in the response object to this value\.  
Type: `string`

**Example input**  

```
{
  "request": {
    "delivery_stream_arn": "arn:aws:firehose:region:account-id:deliverystream/stream2-name",
    "data": "Data to send to the delivery stream."
  },
  "id": "request123"
}
```

**Default topic for binary data \(local publish/subscribe\):** `kinesisfirehose/message/binary/#`  
Use this topic to send a message that contains binary data\. The component doesn't parse binary data\. The component streams the data as is\.  
To map the input request to an output response, replace the `#` wildcard in the message topic with an arbitrary request ID\. For example, if you publish a message to `kinesisfirehose/message/binary/request123`, the `id` property in the response object is set to `request123`\.  
If you don't want to map a request to a response, you can publish your messages to `kinesisfirehose/message/binary/`\. Be sure to include the trailing slash \(`/`\)\.

## Output data<a name="kinesis-firehose-component-output-data"></a>

<a name="connector-component-output-data"></a>This component publishes responses as output data on the following MQTT topic by default\. You must specify this topic as the `subject` in the configuration for the [legacy subscription router component](legacy-subscription-router-component.md)\. For more information about how to subscribe to messages on this topic in your custom components, see [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.

**Default topic \(AWS IoT Core MQTT\):** `kinesisfirehose/message/status`

**Example output**  
The response contains the status of each data record sent in the batch\.  

```
{
  "response": [
    {
      "ErrorCode": "error",
      "ErrorMessage": "test error",
      "id": "request123",
      "status": "fail"
    },
    {
      "firehose_record_id": "xyz2",
      "id": "request456",
      "status": "success"
    },
    {
      "firehose_record_id": "xyz3",
      "id": "request890",
      "status": "success"
    }
  ]
}
```

**Note**  <a name="connector-component-retryable-error"></a>
If the component detects a retryable error, such as a connection error, it retries the publish in the next batch\.

## Local log file<a name="kinesis-firehose-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/aws.greengrass.KinesisFirehose.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.KinesisFirehose.log
  ```

## Licenses<a name="kinesis-firehose-component-licenses"></a>

This component includes the following third\-party software/licensing:<a name="boto-3-licenses"></a>
+ [AWS SDK for Python \(Boto3\)](https://pypi.org/project/boto3/)/Apache License 2\.0
+ [botocore](https://pypi.org/project/botocore/)/Apache License 2\.0
+ [dateutil](https://pypi.org/project/python-dateutil/1.4/)/PSF License
+ [docutils](https://pypi.org/project/docutils/)/BSD License, GNU General Public License \(GPL\), Python Software Foundation License, Public Domain
+ [jmespath](https://pypi.org/project/jmespath/)/MIT License
+ [s3transfer](https://pypi.org/project/s3transfer/)/Apache License 2\.0
+ [urllib3](https://pypi.org/project/urllib3/)/MIT License

<a name="component-core-software-license"></a>This component is released under the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

## Changelog<a name="kinesis-firehose-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.7  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.6  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.5  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.0\.4  |  Version updated for Greengrass nucleus version 2\.1\.0 release\.  | 
|  2\.0\.3  |  Initial version\.  | 

## See also<a name="kinesis-firehose-component-see-also"></a>
+ [What is Amazon Kinesis Data Firehose?](https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html) in the *Amazon Kinesis Data Firehose Developer Guide*