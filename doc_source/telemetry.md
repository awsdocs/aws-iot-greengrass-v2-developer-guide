# Gather system health telemetry data from AWS IoT Greengrass core devices<a name="telemetry"></a>

System health telemetry data is diagnostic data that can help you monitor the performance of critical operations on your Greengrass core devices\. You can create projects and applications to retrieve, analyze, transform, and report telemetry data from your edge devices\. Domain experts, such as process engineers, can use these applications to gain insights into fleet health\.

You can use the following methods to gather telemetry data from your Greengrass core devices:
+ **Nucleus telemetry emitter component**—The [nucleus telemetry emitter component](nucleus-emitter-component.md) \(`aws.greengrass.telemetry.NucleusEmitter`\) on a Greengrass core device publishes telemetry data to the `$local/greengrass/telemetry` topic by default\. You can use the data that is published to this topic to act locally on your core device, even when your device has limited connectivity to the cloud\. Optionally, you can also configure the component to publish telemetry data to an AWS IoT Core MQTT topic of your choice\.

  You must deploy the nucleus emitter component to a core device to publish telemetry data\. There are no costs associated with publishing telemetry data to the local topic\. However, the use of an MQTT topic to publish data to the AWS Cloud is subject to [AWS IoT Core pricing](http://aws.amazon.com/iot-core/pricing/)\.

  <a name="greengrass-software-catalog-influxdb-telemetry-publisher"></a>AWS IoT Greengrass provides several [community components](greengrass-software-catalog.md) to help you analyze and visualize telemetry data locally on your core device using InfluxDB and Grafana\. These components use telemetry data from the nucleus emitter component\. For more information, see the README for the [InfluxDB publisher component](https://github.com/awslabs/aws-greengrass-labs-telemetry-influxdbpublisher)\.
+ **Telemetry agent**—The telemetry agent on Greengrass core devices collects local telemetry data and publishes it to Amazon EventBridge without requiring any customer interaction\. Core devices publish telemetry data to EventBridge on a best effort basis\. For example, core devices might fail to deliver telemetry data while offline\. 

  The telemetry agent feature is enabled by default for all Greengrass core devices\. You automatically start to receive data as soon as you set up a Greengrass core device\. Aside from your data link costs, the data transfer from the core device to AWS IoT Core is without charge\. This is because the agent publishes to an AWS reserved topic\. However, depending on your use case, you might incur costs when you receive or process the data\.
**Note**  
Amazon EventBridge is an event bus service that you can use to connect your applications with data from a variety of sources, such as Greengrass core devices\. For more information, see [What is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/what-is-amazon-eventbridge.html) in the *Amazon EventBridge User Guide*\.

To ensure that the the AWS IoT Greengrass Core software functions properly, AWS IoT Greengrass uses the data for development and quality improvement purposes\. This feature also helps inform new and enhanced edge capabilities\. AWS IoT Greengrass retains telemetry data for up to seven days\.

This section describes how to configure and use the telemetry agent\. For information about configuring the nucleus telemetry emitter component, see [Nucleus telemetry emitter](nucleus-emitter-component.md)\.

**Topics**
+ [Telemetry metrics](#telemetry-metrics)
+ [Configure telemetry agent settings](#configure-telemetry-agent-settings)
+ [Subscribe to telemetry data in EventBridge](#subscribe-for-telemetry-data)

## Telemetry metrics<a name="telemetry-metrics"></a>

The following table describes the metrics that are published by the telemetry agent\.


| Name | Description | Source | 
| --- | --- | --- | 
|  `SystemMemUsage`  |  The amount of memory currently in use by all applications on the Greengrass core device, including the operating system\.  |  System  | 
|  `CpuUsage`  |  The amount of CPU currently in use by all applications on the Greengrass core device, including the operating system\.  |  System  | 
|  `TotalNumberOfFDs`  |  The number of file descriptors stored by the operating system of the Greengrass core device\. One file descriptor uniquely identifies one open file\.  |  System  | 
|  `NumberOfComponentsRunning`  |  The number of components that are running on the Greengrass core device\.  |  Greengrass nucleus  | 
|  `NumberOfComponentsErrored`  |  The number of components that are in error state on the Greengrass core device\.  |  Greengrass nucleus  | 
|  `NumberOfComponentsInstalled`  |  The number of components that are installed on the Greengrass core device\.  |  Greengrass nucleus  | 
|  `NumberOfComponentsStarting`  |  The number of components that are starting on the Greengrass core device\.  |  Greengrass nucleus  | 
|  `NumberOfComponentsNew`  |  The number of components that are new on the Greengrass core device\.  |  Greengrass nucleus  | 
|  `NumberOfComponentsStopping`  |  The number of components that are stopping on the Greengrass core device\.  |  Greengrass nucleus  | 
|  `NumberOfComponentsFinished`  |  The number of components that are finished on the Greengrass core device\.  |  Greengrass nucleus  | 
|  `NumberOfComponentsBroken`  |  The number of components that are broken on the Greengrass core device\.  |  Greengrass nucleus  | 
|  `NumberOfComponentsStateless`  |  The number of components that are stateless on the Greengrass core device\.  |  Greengrass nucleus  | 

## Configure telemetry agent settings<a name="configure-telemetry-agent-settings"></a>

The telemetry agent uses the following default settings:
+ The telemetry agent aggregates telemetry data every hour\.
+ The telemetry agent publishes a telemetry message every 24 hours\.

The telemetry agent publishes data using the MQTT protocol with a quality of service \(QoS\) level of 0, which means that it doesn't confirm delivery or retry publishing attempts\. Telemetry messages share an MQTT connection with other messages for subscriptions destined for AWS IoT Core\.

Aside from your data link costs, the data transfer from the core to AWS IoT Core is without charge\. This is because the agent publishes to an AWS reserved topic\. However, depending on your use case, you might incur costs when you receive or process the data\.

You can enable or disable the telemetry agent feature for each Greengrass core device\. You can also configure the intervals over which the core device aggregates and publishes data\. To configure telemetry, customize the [telemetry configuration parameter](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-telemetry) when you deploy the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

## Subscribe to telemetry data in EventBridge<a name="subscribe-for-telemetry-data"></a>

You can create rules in Amazon EventBridge that define how to process telemetry data published from the telemetry agent on the Greengrass core device\. When EventBridge receives the data, it invokes the target actions defined in your rules\. For example, you can create event rules that send notifications, store event information, take corrective action, or invoke other events\.

### Telemetry events<a name="events-message-format"></a>

Telemetry events use the following format\.

```
{
  "version": "0",
  "id": "a09d303e-2f6e-3d3c-a693-8e33f4fe3955",
  "detail-type": "Greengrass Telemetry Data",
  "source": "aws.greengrass",
  "account": "123456789012",
  "time": "2020-11-30T20:45:53Z",
  "region": "us-east-1",
  "resources": [],
  "detail": {
    "ThingName": "MyGreengrassCore",
    "Schema": "2020-07-30",
    "ADP": [
      {
        "TS": 1602186483234,
        "NS": "SystemMetrics",
        "M": [
          {
            "N": "TotalNumberOfFDs",
            "Sum": 6447.0,
            "U": "Count"
          },
          {
            "N": "CpuUsage",
            "Sum": 15.458333333333332,
            "U": "Percent"
          },
          {
            "N": "SystemMemUsage",
            "Sum": 10201.0,
            "U": "Megabytes"
          }
        ]
      },
      {
        "TS": 1602186483234,
        "NS": "GreengrassComponents",
        "M": [
          {
            "N": "NumberOfComponentsStopping",
            "Sum": 0.0,
            "U": "Count"
          },
          {
            "N": "NumberOfComponentsStarting",
            "Sum": 0.0,
            "U": "Count"
          },
          {
            "N": "NumberOfComponentsBroken",
            "Sum": 0.0,
            "U": "Count"
          },
          {
            "N": "NumberOfComponentsFinished",
            "Sum": 1.0,
            "U": "Count"
          },
          {
            "N": "NumberOfComponentsInstalled",
            "Sum": 0.0,
            "U": "Count"
          },
          {
            "N": "NumberOfComponentsRunning",
            "Sum": 7.0,
            "U": "Count"
          },
          {
            "N": "NumberOfComponentsNew",
            "Sum": 0.0,
            "U": "Count"
          },
          {
            "N": "NumberOfComponentsErrored",
            "Sum": 0.0,
            "U": "Count"
          },
          {
            "N": "NumberOfComponentsStateless",
            "Sum": 0.0,
            "U": "Count"
          }
        ]
      }
    ]
  }
}
```<a name="telemetry-agent-adp-array-config"></a>

The `ADP` array contains a list of aggregated data points that have the following properties:

`TS`  
The timestamp of when the data was gathered\.

`NS`  
The metric namespace\.

`M`  
The list of metrics\. A metric contains the following properties:    
`N`  
The name of the metric\.  
`Sum`  
The sum of the metric's values in this telemetry event\.  
`U`  
The unit of the metric value\.

For more information about each metric, see [Telemetry metrics](#telemetry-metrics)\.

### Prerequisites to create EventBridge rules<a name="create-events-rule-prerequisites-telemetry"></a>

Before you create an EventBridge rule for AWS IoT Greengrass, you should do the following:
+ Familiarize yourself with events, rules, and targets in EventBridge\.
+ Create and configure the [targets](https://docs.aws.amazon.com/eventbridge/latest/userguide/eventbridge-targets.html) invoked by your EventBridge rules\. Rules can invoke many types of targets, such as Amazon Kinesis streams, AWS Lambda functions, Amazon SNS topics, and Amazon SQS queues\.

  Your EventBridge rule, and the associated targets must be in the AWS Region where you created your Greengrass resources\. For more information, see [Service endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/aws-service-information.html) in the *AWS General Reference*\.

For more information, see [What is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/what-is-amazon-eventbridge.html) and [Getting started with Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eventbridge-getting-set-up.html) in the *Amazon EventBridge User Guide*\.

### Create an event rule to get telemetry data \(console\)<a name="create-telemetry-event-rule-console"></a>

Use the following steps to use the AWS Management Console to create an EventBridge rule that receives telemetry data published by the Greengrass core device\. This allows web servers, email addresses, and other topic subscribers to respond to the event\. For more information, see [Creating a EventBridge rule that triggers on an event from an AWS resource](https://docs.aws.amazon.com/eventbridge/latest/userguide/create-eventbridge-rule.html) in the *Amazon EventBridge User Guide*\.

1. Open the [Amazon EventBridge console](https://console.aws.amazon.com/events/), and choose **Create rule**\.

1. Under **Name and description**, enter a name and description for the rule\.

1. Under **Define pattern**, configure the rule pattern\.

   1. Choose **Event pattern**\.

   1. Choose **Pre\-defined pattern by service**\.

   1. For **Service provider**, choose **AWS**\.

   1. For **Service name**, choose **Greengrass**\.

   1. For **Event type**, select **Greengrass Telemetry Data**\.

1. Under **Select event bus**, keep the default event bus options\.

1. Under **Select targets**, configure your target\. The following example uses an Amazon SQS queue, but you can configure other target types\.

   1. For **Target**, choose **SQS queue**\.

   1. For **Queue\***, choose your target queue\.

1. Under **Tags \- optional**, define tags for the rule or leave the fields empty\.

1. Choose **Create**\.

### Create an event rule to get telemetry data \(CLI\)<a name="create-telemetry-event-rule-cli"></a>

Use the following steps to use the AWS CLI to create an EventBridge rule that receives telemetry data published by Greengrass core devices\. This allows web servers, email addresses, and other topic subscribers to respond to the event\.

1. Create the rule\.
   + Replace *thing\-name* with the thing name of the core device\.

------
#### [ Linux or Unix ]

   ```
   aws events put-rule \
     --name MyGreengrassTelemetryEventRule \
     --event-pattern "{\"source\": [\"aws.greengrass\"], \"detail\": {\"ThingName\": [\"thing-name\"]}}"
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws events put-rule ^
     --name MyGreengrassTelemetryEventRule ^
     --event-pattern "{\"source\": [\"aws.greengrass\"], \"detail\": {\"ThingName\": [\"thing-name\"]}}"
   ```

------
#### [ PowerShell ]

   ```
   aws events put-rule `
     --name MyGreengrassTelemetryEventRule `
     --event-pattern "{\"source\": [\"aws.greengrass\"], \"detail\": {\"ThingName\": [\"thing-name\"]}}"
   ```

------

   Properties that are omitted from the pattern are ignored\.

1. Add the topic as a rule target\. The following example uses Amazon SQS but you can configure other target types\.
   + Replace *queue\-arn* with the ARN of your Amazon SQS queue\.

------
#### [ Linux or Unix ]

   ```
   aws events put-targets \
     --rule MyGreengrassTelemetryEventRule \
     --targets "Id"="1","Arn"="queue-arn"
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws events put-targets ^
     --rule MyGreengrassTelemetryEventRule ^
     --targets "Id"="1","Arn"="queue-arn"
   ```

------
#### [ PowerShell ]

   ```
   aws events put-targets `
     --rule MyGreengrassTelemetryEventRule `
     --targets "Id"="1","Arn"="queue-arn"
   ```

------
**Note**  
To allow Amazon EventBridge to invoke your target queue, you must add a resource\-based policy to your topic\. For more information, see [Amazon SQS permissions](https://docs.aws.amazon.com/eventbridge/latest/userguide/resource-based-policies-eventbridge.html#sqs-permissions) in the *Amazon EventBridge User Guide*\.

For more information, see [Events and event patterns in EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eventbridge-and-event-patterns.html) in the *Amazon EventBridge User Guide*\.