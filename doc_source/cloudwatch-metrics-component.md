# CloudWatch metrics<a name="cloudwatch-metrics-component"></a>

The Amazon CloudWatch metrics component \(`aws.greengrass.Cloudwatch`\) publishes custom metrics from Greengrass core devices to Amazon CloudWatch\. The component enables components to publish CloudWatch metrics, which you can use to monitor and analyze the Greengrass core device's environment\. For more information, see [Using Amazon CloudWatch metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html) in the *Amazon CloudWatch User Guide*\.

To publish a CloudWatch metric with this component, publish a message to a topic where this component subscribes\. By default, this component subscribes to the `cloudwatch/metric/put` [local publish/subscribe](ipc-publish-subscribe.md) topic\. You can specify other topics, including AWS IoT Core MQTT topics, when you deploy this component\.

This component batches metrics that are in the same namespace and publishes them to CloudWatch at regular intervals\.

**Note**  
This component provides similar functionality to the CloudWatch metrics connector in AWS IoT Greengrass V1\. For more information, see [CloudWatch metrics connector](https://docs.aws.amazon.com/greengrass/latest/developerguide/cloudwatch-metrics-connector.html) in the *AWS IoT Greengrass V1 Developer Guide*\.

**Topics**
+ [Versions](#cloudwatch-metrics-component-versions)
+ [Type](#cloudwatch-metrics-component-type)
+ [Operating system](#cloudwatch-metrics-component-os-support)
+ [Requirements](#cloudwatch-metrics-component-requirements)
+ [Dependencies](#cloudwatch-metrics-component-dependencies)
+ [Configuration](#cloudwatch-metrics-component-configuration)
+ [Input data](#cloudwatch-metrics-component-input-data)
+ [Output data](#cloudwatch-metrics-component-output-data)
+ [Licenses](#cloudwatch-metrics-component-licenses)
+ [Local log file](#cloudwatch-metrics-component-log-file)
+ [Changelog](#cloudwatch-metrics-component-changelog)
+ [See also](#cloudwatch-metrics-component-see-also)

## Versions<a name="cloudwatch-metrics-component-versions"></a>

This component has the following versions:
+ 3\.0\.x
+ 2\.1\.x
+ 2\.0\.x

For information about changes in each version of the component, see the [changelog](#cloudwatch-metrics-component-changelog)\.

## Type<a name="cloudwatch-metrics-component-type"></a>

------
#### [ 3\.0\.x ]

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

------
#### [ 2\.0\.x ]

<a name="public-component-type-lambda"></a>This <a name="public-component-type-lambda-phrase"></a>component is a Lambda component \(`aws.greengrass.lambda`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component's Lambda function using the [Lambda launcher component](lambda-launcher-component.md)\.

------

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="cloudwatch-metrics-component-os-support"></a>

------
#### [ 3\.0\.x ]

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

------
#### [ 2\.0\.x ]

This component can be installed on Linux core devices only\.

------

## Requirements<a name="cloudwatch-metrics-component-requirements"></a>

This component has the following requirements:

------
#### [ 3\.0\.x ]
+ <a name="public-component-python3-requirement"></a>[Python](https://www.python.org/) version 3\.7 installed on the core device and added to the PATH environment variable\.
+ The [Greengrass device role](device-service-role.md) must allow the `cloudwatch:PutMetricData` action, as shown in the following example IAM policy\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "cloudwatch:PutMetricData"
        ],
        "Effect": "Allow",
        "Resource": "*"
      }
    ]
  }
  ```

  For more information, see [Amazon CloudWatch permissions reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/permissions-reference-cw.html) in the *Amazon CloudWatch User Guide*\.

------
#### [ 2\.0\.x \- 2\.1\.x ]
+ <a name="core-device-lambda-function-requirements"></a>Your core device must meet the requirements to run Lambda functions\. If you want the core device to run containerized Lambda functions, the device must meet the requirements to do so\. For more information, see [Lambda function requirements](setting-up.md#greengrass-v2-lambda-requirements)\.
+ <a name="public-component-python3-requirement"></a>[Python](https://www.python.org/) version 3\.7 installed on the core device and added to the PATH environment variable\.
+ The [Greengrass device role](device-service-role.md) must allow the `cloudwatch:PutMetricData` action, as shown in the following example IAM policy\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "cloudwatch:PutMetricData"
        ],
        "Effect": "Allow",
        "Resource": "*"
      }
    ]
  }
  ```

  For more information, see [Amazon CloudWatch permissions reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/permissions-reference-cw.html) in the *Amazon CloudWatch User Guide*\.
+ <a name="connector-component-legacy-subscription-router-dependency"></a>To receive output data from this component, you must merge the following configuration update for the [legacy subscription router component](legacy-subscription-router-component.md) \(`aws.greengrass.LegacySubscriptionRouter`\) when you deploy this component\. This configuration specifies the topic where this component publishes responses\.

------
#### [ Legacy subscription router v2\.1\.x ]

  ```
  {
    "subscriptions": {
      "aws-greengrass-cloudwatch": {
        "id": "aws-greengrass-cloudwatch",
        "source": "component:aws.greengrass.Cloudwatch",
        "subject": "cloudwatch/metric/put/status",
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
      "aws-greengrass-cloudwatch": {
        "id": "aws-greengrass-cloudwatch",
        "source": "arn:aws:lambda:region:aws:function:aws-greengrass-cloudwatch:version",
        "subject": "cloudwatch/metric/put/status",
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

------

### Endpoints and ports<a name="cloudwatch-metrics-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `monitoring.region.amazonaws.com`  | 443 | Yes |  Upload CloudWatch metrics\.  | 

## Dependencies<a name="cloudwatch-metrics-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#cloudwatch-metrics-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 3\.0\.0 ]

The following table lists the dependencies for version 3\.0\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <3\.0\.0 | Soft | 
| [Token exchange service](token-exchange-service-component.md) | >=0\.0\.0 | Hard | 

------
#### [ 2\.0\.8 \- 2\.1\.0 ]

The following table lists the dependencies for versions 2\.0\.8 and 2\.1\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.6\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

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

## Configuration<a name="cloudwatch-metrics-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ 3\.0\.x ]

 `PublishInterval`   
\(Optional\) The maximum number of seconds to wait before the component publishes batched metrics for a given namespace\. To configure the component to publish metrics as it receives them, which means without batching, specify `0`\.  
The component publishes to CloudWatch after it receives 20 metrics in the same namespace or after the interval that you specify\.  
The component doesn't specify the order in which events publish\.
This value can be a maximum of 900 seconds\.  
Default: 10 seconds

 `MaxMetricsToRetain`   
\(Optional\) The maximum number of metrics across all namespaces to save in memory before the component replaces them with newer metrics\.  
This limit applies when the core device doesn't have a connection to the internet, so the component buffers the metrics to publish later\. When the buffer is full, the component replaces the oldest metrics with newer ones\. Metrics in a given namespace replace only metrics in the same namespace\.  
If the host process for the component is interrupted, the component doesn't save metrics\. This can happen during a deployment or when the core device restarts, for example\.
This value must be at least 2,000 metrics\.  
Default: 5,000 metrics

`InputTopic`  
\(Optional\) The topic to which the component subscribes to receive messages\. If you specify `true` for `PubSubToIoTCore`, you can use MQTT wildcards \(\+ and \#\) in this topic\.  
Default: `cloudwatch/metric/put`

`OutputTopic`  
\(Optional\) The topic to which the component publishes status responses\.  
Default: `cloudwatch/metric/put/status`

`PubSubToIoTCore`  
\(Optional\) String value that defines whether to publish and subscribe to AWS IoT Core MQTT topics\. Supported values are `true` and `false`\.  
Default: `false`

`UseInstaller`  
\(Optional\) Boolean value that defines whether to use the installer script in this component to install this component's SDK dependencies\.  
Set this value to `false` if you want to use a custom script to install dependencies, or if you want to include runtime dependencies in a pre\-built Linux image\. To use this component, you must install the following libraries, including any dependencies, and make them available to the default Greengrass system user\.  
+ [AWS IoT Device SDK v2 for Python](https://github.com/aws/aws-iot-device-sdk-python-v2)
+ [AWS SDK for Python \(Boto3\)](http://boto.readthedocs.org/en/latest/ref/)
Default: `true`

`PublishRegion`  
\(Optional\) The AWS Region to which to publish CloudWatch metrics\. This value overrides the default Region for the core device\. This parameter is required only for cross\-Region metrics\.

`accessControl`  
\(Optional\) The object that contains the [authorization policy](interprocess-communication.md#ipc-authorization-policies) that allows the component to publish and subscribe to the specified topics\. If you specify custom values for `InputTopic` and `OutputTopic`, you must update the resource values in this object\.   
Default:   

```
{
  "aws.greengrass.ipc.pubsub": {
    "aws.greengrass.Cloudwatch:pubsub:1": {
      "policyDescription": "Allows access to subscribe to input topics.",
      "operations": [
        "aws.greengrass#SubscribeToTopic"
      ],
      "resources": [
        "cloudwatch/metric/put"
      ]
    },
    "aws.greengrass.Cloudwatch:pubsub:2": {
      "policyDescription": "Allows access to publish to output topics.",
      "operations": [
        "aws.greengrass#PublishToTopic"
      ],
      "resources": [
        "cloudwatch/metric/put/status"
      ]
    }
  },
  "aws.greengrass.ipc.mqttproxy": {
    "aws.greengrass.Cloudwatch:mqttproxy:1": {
      "policyDescription": "Allows access to subscribe to input topics.",
      "operations": [
        "aws.greengrass#SubscribeToIoTCore"
      ],
      "resources": [
        "cloudwatch/metric/put"
      ]
    },
    "aws.greengrass.Cloudwatch:mqttproxy:2": {
      "policyDescription": "Allows access to publish to output topics.",
      "operations": [
        "aws.greengrass#PublishToIoTCore"
      ],
      "resources": [
        "cloudwatch/metric/put/status"
      ]
    }
  }
}
```

**Example: Configuration merge update**  

```
{
  "PublishInterval": 0,
  "PubSubToIoTCore": true
}
```

------
#### [ 2\.0\.x \- 2\.1\.x ]

**Note**  <a name="connector-component-lambda-parameters"></a>
This component's default configuration includes Lambda function parameters\. We recommend that you edit only the following parameters to configure this component on your devices\.

 `lambdaParams`   
An object that contains the parameters for this component's Lambda function\. This object contains the following information:    
 `EnvironmentVariables`   
An object that contains the Lambda function's parameters\. This object contains the following information:    
 `PUBLISH_INTERVAL`   
\(Optional\) The maximum number of seconds to wait before the component publishes batched metrics for a given namespace\. To configure the component to publish metrics as it receives them, which means without batching, specify `0`\.  
The component publishes to CloudWatch after it receives 20 metrics in the same namespace or after the interval that you specify\.  
The component doesn't guarantee the order in which events publish\.
This value can be at most 900 seconds\.  
Default: 10 seconds  
 `MAX_METRICS_TO_RETAIN`   
\(Optional\) The maximum number of metrics across all namespaces to save in memory before the component replaces them with newer metrics\.  
This limit applies when the core device doesn't have a connection to the internet, so the component buffers the metrics to publish later\. When the buffer is full, the component replaces the oldest metrics with newer ones\. Metrics in a given namespace replace only metrics in the same namespace\.  
If the host process for the component is interrupted, the component doesn't save metrics\. This can happen during a deployment or when the core device restarts, for example\.
This value must be at least 2,000 metrics\.  
Default: 5,000 metrics  
 `PUBLISH_REGION`   
\(Optional\) The AWS Region to which to publish CloudWatch metrics\. This value overrides the default Region for the core device\. This parameter is required only for cross\-Region metrics\.

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
  "containerMode": "GreengrassContainer"
}
```

**Example: Configuration merge update \(no container mode\)**  

```
{
  "containerMode": "NoContainer"
}
```

------

## Input data<a name="cloudwatch-metrics-component-input-data"></a>

This component accepts metrics on the following topic and publishes the metrics to CloudWatch\. <a name="connector-component-local-publish"></a>By default, this component subscribes to local publish/subscribe messages\. For more information about how to publish messages to this component from your custom components, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.

Beginning with component version v3\.0\.0, you can optionally configure this component to subscribe to an MQTT topic by setting the `PubSubToIoTCore` configuration parameter to `true`\. For more information about publishing messages to an MQTT topic in your custom components, see [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.

 **Default topic:** `cloudwatch/metric/put` 

<a name="connector-component-input-properties"></a>The message accepts the following properties\. Input messages must be in JSON format\.

 `request`   
The metric in this message\.  
The request object contains the metric data to publish to CloudWatch\. The metric values must meet the specifications of the [https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricData.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricData.html) operation\.  
Type: `object` that contains the following information:    
 `namespace`   
The user\-defined namespace for the metric data in this request\. CloudWatch uses namespaces as containers for metric data points\.  
You can't specify a namespace that begins with the reserved string `AWS/`\.
Type: `string`  
Valid pattern: `[^:].*`  
 `metricData`   
The data for the metric\.  
Type: `object` that contains the following information:    
 `metricName`   
The name of the metric\.  
Type: `string`  
 `value`   
The value for the metric\.  
CloudWatch rejects values that are too small or too large\. The value must be between `8.515920e-109` and `1.174271e+108` \(Base 10\) or `2e-360` and `2e360` \(Base 2\)\. CloudWatch doesn't support special values such as `NaN`, `+Infinity`, and `-Infinity`\.
Type: `double`  
 `dimensions`   
\(Optional\) The dimensions for the metric\. Dimensions provide additional information about the metric and its data\. A metric can define up to 10 dimensions\.  
This component automatically includes a dimension named `coreName`, where the value is the name of the core device\.  
Type: `array` of objects that each contain the following information:    
 `name`   
\(Optional\) The dimension name\.  
Type: `string`  
 `value`   
\(Optional\) The dimension value\.  
Type: `string`  
 `timestamp`   
\(Optional\) The time at which the metric data was received, expressed in seconds in Unix epoch time\.  
Defaults to the time at which the component receives the message\.  
Type: `double`  
If you use between versions 2\.0\.3 and 2\.0\.7 of this component, we recommend that you retrieve the timestamp separately for each metric when you send multiple metrics from a single source\. Don't use a variable to store the timestamp\.  
 `unit`   
\(Optional\) The unit of the metric\.  
Type: `string`  
Valid values: `Seconds`, `Microseconds`, `Milliseconds`, `Bytes`, `Kilobytes`, `Megabytes`, `Gigabytes`, `Terabytes`, `Bits`, `Kilobits`, `Megabits`, `Gigabits`, `Terabits`, `Percent`, `Count`, `Bytes/Second`, `Kilobytes/Second`, `Megabytes/Second`, `Gigabytes/Second`, `Terabytes/Second`, `Bits/Second`, `Kilobits/Second`, `Megabits/Second`, `Gigabits/Second`, `Terabits/Second`, `Count/Second`, `None`  
Defaults to `None`\.

**Note**  
All quotas that apply to the CloudWatch `PutMetricData` API apply to metrics that you publish with this component\. The following quotas are especially important:  
40 KB limit on the API payload
20 metrics per API request
150 transactions per second \(TPS\) for the `PutMetricData` API
For more information, see [CloudWatch service quotas](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html) in the *CloudWatch User Guide*\.

**Example input**  

```
{
  "request": {
    "namespace": "Greengrass",
    "metricData": {
      "metricName": "latency",
      "dimensions": [
        {
          "name": "hostname",
          "value": "test_hostname"
        }
      ],
      "timestamp": 1539027324,
      "value": 123.0,
      "unit": "Seconds"
    }
  }
}
```

## Output data<a name="cloudwatch-metrics-component-output-data"></a>

 <a name="connector-component-output-data-pubsub"></a>This component publishes responses as output data on the following local publish/subscribe topic by default\. For more information about how to subscribe to messages on this topic in your custom components, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\. 

You can optionally configure this component to publish to an MQTT topic by setting the `PubSubToIoTCore` configuration parameter to `true`\. For more information about subscribing to messages on an MQTT topic in your custom components, see [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.

**Note**  
Component versions 2\.0\.x publish responses as output data on an MQTT topic by default\. You must specify the topic as the `subject` in the configuration for the [legacy subscription router component](legacy-subscription-router-component.md)\.

 **Default topic:** `cloudwatch/metric/put/status` 

**Example output: Success**  
The response includes the namespace of the metric data and the `RequestId` field from the CloudWatch response\.  

```
{
  "response": {
    "cloudwatch_rid": "70573243-d723-11e8-b095-75ff2EXAMPLE",
    "namespace": "Greengrass",
    "status": "success"
  }
}
```

**Example output: Failure**  

```
{
  "response" : {
    "namespace": "Greengrass",
    "error": "InvalidInputException",
    "error_message": "cw metric is invalid",
    "status": "fail"
  }
}
```

**Note**  <a name="connector-component-retryable-error"></a>
If the component detects an error that can be retried, such as a connection error, it retries the publish in the next batch\.

## Licenses<a name="cloudwatch-metrics-component-licenses"></a>

This component includes the following third\-party software/licensing:<a name="boto-3-licenses"></a>
+ [AWS SDK for Python \(Boto3\)](https://pypi.org/project/boto3/)/Apache License 2\.0
+ [botocore](https://pypi.org/project/botocore/)/Apache License 2\.0
+ [dateutil](https://pypi.org/project/python-dateutil/1.4/)/PSF License
+ [docutils](https://pypi.org/project/docutils/)/BSD License, GNU General Public License \(GPL\), Python Software Foundation License, Public Domain
+ [jmespath](https://pypi.org/project/jmespath/)/MIT License
+ [s3transfer](https://pypi.org/project/s3transfer/)/Apache License 2\.0
+ [urllib3](https://pypi.org/project/urllib3/)/MIT License

<a name="component-core-software-license"></a>This component is released under the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

## Local log file<a name="cloudwatch-metrics-component-log-file"></a>

This component uses the following log file\.

------
#### [ Linux ]

```
/greengrass/v2/logs/aws.greengrass.Cloudwatch.log
```

------
#### [ Windows ]

```
C:\greengrass\v2\logs\aws.greengrass.Cloudwatch.log
```

------

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux ]

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.Cloudwatch.log
  ```

------
#### [ Windows \(PowerShell\) ]

  ```
  Get-Content C:\greengrass\v2\logs\aws.greengrass.Cloudwatch.log -Tail 10 -Wait
  ```

------

## Changelog<a name="cloudwatch-metrics-component-changelog"></a>

The following table describes the changes in each version of the component\.

------
#### [ v3\.x ]


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  3\.0\.0  |  <a name="changelog-cloudwatch-metrics-3.0.0-major-version-changes"></a>This version of the CloudWatch metrics component expects different configuration parameters than version 2\.x\. If you use a non\-default configuration for version 2\.x, and you want to upgrade from v2\.x to v3\.x, you must update the component's configuration\. For more information, see [CloudWatch metrics component configuration](#cloudwatch-metrics-component-configuration)\. <a name="changelog-cloudwatch-metrics-3.0.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/cloudwatch-metrics-component.html)  | 

------
#### [ v2\.x ]


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/cloudwatch-metrics-component.html)  | 
|  2\.0\.8  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/cloudwatch-metrics-component.html)  | 
|  2\.0\.7  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.6  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.5  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.0\.4  |  Version updated for Greengrass nucleus version 2\.1\.0 release\.  | 
|  2\.0\.3  |  Initial version\.  | 

------

## See also<a name="cloudwatch-metrics-component-see-also"></a>
+ [Using Amazon CloudWatch metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html) in the *Amazon CloudWatch User Guide*
+ [PutMetricData](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricData.html) in the *Amazon CloudWatch API Reference*