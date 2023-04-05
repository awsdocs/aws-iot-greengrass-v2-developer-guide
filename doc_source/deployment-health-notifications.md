# Get deployment and component health status notifications<a name="deployment-health-notifications"></a>

Amazon EventBridge event rules provide you with notifications about state changes for your Greengrass deployments received by your devices and for installed components on your device\. EventBridge delivers a near real\-time stream of system events that describes changes in AWS resources\. AWS IoT Greengrass sends these events to EventBridge on a *best\-effort* basis\. This means that AWS IoT Greengrass attempts to send all events to EventBridge but, in some rare cases, an event might not be delivered\. Additionally, AWS IoT Greengrass might send multiple copies of a given event, which means that your event listeners might not receive the events in the order that the events occurred\. 

**Note**  
Amazon EventBridge is an event bus service that you can use to connect your applications with data from a variety of sources, such as [Greengrass core devices](telemetry.md) and deployment and component notifications\. For more information, see [What is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/what-is-amazon-eventbridge.html) in the *Amazon EventBridge User Guide*\.

**Topics**
+ [Deployment status change event](#deployment.events-message-format)
+ [Component status change event](#component.events-message-format)
+ [Prerequisites for creating EventBridge rules](#create-events-rule-prereqs)
+ [Configure device health notifications \(console\)](#create-events-rule-console)
+ [Configure device health notifications \(CLI\)](#create-events-rule-cli)
+ [Configure device health notifications \(AWS CloudFormation\)](#create-events-rule-cloudformation)
+ [See also](#deployment-notifications-see-also)

## Deployment status change event<a name="deployment.events-message-format"></a>

AWS IoT Greengrass emits an event when a deployment enters the following states: `FAILED`, `SUCCEEDED` and `COMPLETED`\. You can create an EventBridge rule that runs for all state transitions or transitions to states you specify\. When a deployment enters a state that initiates a rule, EventBridge invokes the target actions defined in the rule\. This allows you to send notifications, capture event information, take corrective action, or initiate other events in response to a state change\. For example, you can create rules for the following use cases:
+ Initiate post\-deployment operations, such as downloading assets and notifying personnel\.
+ Send notifications upon a successful or failed deployment\.
+ Publish custom metrics about deployment events\.

The [event](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CloudWatchEventsandEventPatterns.html) for a deployment state change uses the following format:

```
{
    "version":"0",
    "id":" cd4d811e-ab12-322b-8255-EXAMPLEb1bc8",
    "detail-type":"Greengrass V2 Effective Deployment Status Change",
    "source":"aws.greengrass",
    "account":"123456789012",
    "region":"us-west-2",
    "time":"2018-03-22T00:38:11Z",
    "resources":["arn:aws:greengrass:us-east-1:123456789012:coreDevices:MyGreengrassCore"],
    "detail":{    
        "deployment-id": "4f38f1a7-3dd0-42a1-af48-EXAMPLE09681",
        "coreDeviceExecutionStatus": "FAILED|SUCCEEDED|COMPLETED",
        "statusDetails": {
          "errorStack": ["DEPLOYMENT_FAILURE", "ARTIFACT_DOWNLOAD_ERROR", "S3_ERROR", "S3_ACCESS_DENIED", "S3_HEAD_OBJECT_ACCESS_DENIED"],
          "errorTypes": ["DEPENDENCY_ERROR", "PERMISSION_ERROR"],
        },
        "reason": "S3_HEAD_OBJECT_ACCESS_DENIED: FAILED_NO_STATE_CHANGE: Failed to download artifact name: 's3://pentest27/nucleus/281/aws.greengrass.nucleus.zip' for component aws.greengrass.Nucleus-2.8.1, reason: S3 HeadObject returns 403 Access Denied. Ensure the IAM role associated with the core device has a policy granting s3:GetObject. null (Service: S3, Status Code: 403, Request ID: HR94ZNT2161DAR58, Extended Request ID: wTX4DDI+qigQt3uzwl9rlnQiYlBgwvPm/KJFWeFAn9t1mnGXTms/luLCYANgq08RIH+x2H+hEKc=)"
     }
}
```

You can create rules and events that will update you on the status of a deployment\. An event is initiated when a deployment completes as either `FAILED`, `SUCCEEDED`, OR `COMPLETED`\. If the deployment failed on the core device, you will receive a detailed response that explains why the deployment failed\. For more information about deployment error codes, see [Detailed deployment error codes](troubleshooting-deployment.md)\.

**Deployment states**  
+ `FAILED`\. The deployment failed\.
+ `SUCCEEDED`\. The deployment targeted to a thing group successfully completed\.
+ `COMPLETED`\. The deployment targeted to a thing successfully completed\.

It's possible that events might be duplicated or out of order\. To determine the order of events, use the `time` property\.

For a full list of error codes in `errorStacks` and `errorTypes`, you can visit \[new error codes and error types site link will go here\]\.

## Component status change event<a name="component.events-message-format"></a>

AWS IoT Greengrass emits an event when a component enters the following states: `ERRORED` and `BROKEN`\. Greengrass will also emit an event when a deployment completes\. You can create an EventBridge rule that runs for all state transitions or transitions to states you specify\. When an installed component enters a state that initiates a rule, EventBridge invokes the target actions defined in the rule\. This allows you to send notifications, capture event information, take corrective action, or initiate other events in response to a state change\.

The [event](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CloudWatchEventsandEventPatterns.html) for a component state change uses the following format:

```
{
    "version":"0",
    "id":" cd4d811e-ab12-322b-8255-EXAMPLEb1bc8",
    "detail-type":"Greengrass V2 Installed Component Status Change",
    "source":"aws.greengrass",
    "account":"123456789012",
    "region":"us-west-2",
    "time":"2018-03-22T00:38:11Z",
    "resources":["arn:aws:greengrass:us-east-1:123456789012:coreDevices:MyGreengrassCore"],
    "detail": {
       "components": [
          {
            "componentName": "MyComponent",
            "componentVersion": "1.0.0",
            "root": true,
            "lifecycleState": "ERRORED|BROKEN",
            "lifecycleStatusCodes": ["STARTUP_ERROR"],
            "lifecycleStateDetails": "An error occurred during startup. The startup script exited with code 1."
          }
        ]
    }
}
```

You can create rules and events that will update you on the status of an installed component\. An event is initiated when a component changes state on the device\. You will receive a detailed response that explains why a component is errored or broken\. You will also receive a status code that will indicate a reason for the failure\. For more information about component status codes, see [Detailed component status codes](troubleshooting-component.md)\.

## Prerequisites for creating EventBridge rules<a name="create-events-rule-prereqs"></a>

Before you create an EventBridge rule for AWS IoT Greengrass, do the following:
+ Familiarize yourself with events, rules, and targets in EventBridge\.
+ Create and configure the targets invoked by your EventBridge rules\. Rules can invoke many types of targets, including:
  + Amazon Simple Notification Service \(Amazon SNS\)
  + AWS Lambda functions
  + Amazon Kinesis Video Streams
  + Amazon Simple Queue Service \(Amazon SQS\) queues

For more information, see [What is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/what-is-amazon-eventbridge.html) and [Getting started with Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eventbridge-getting-set-up.html) in the *Amazon EventBridge User Guide*\.

## Configure device health notifications \(console\)<a name="create-events-rule-console"></a>

Use the following steps to create an EventBridge rule that publishes an Amazon SNS topic when the deployment state changes for a group\. This allows web servers, email addresses, and other topic subscribers to respond to the event\. For more information, see [Creating a EventBridge rule that triggers on an event from an AWS resource](https://docs.aws.amazon.com/eventbridge/latest/userguide/create-eventbridge-rule.html) in the *Amazon EventBridge User Guide*\.

1. Open the [Amazon EventBridge console](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to match events that come from your account, select **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account's default event bus\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. For **Event source**, choose **AWS events**\.

1. For **Event pattern**, choose **AWS services**\.

1. For **AWS service**, choose Greengrass\.

1. For **Event type**, choose from the following:
   + For deployment events, choose **Greengrass V2 Effective Deployment Status Change**\.
   + For component events, choose **Greengrass V2 Installed Component Status Change**\.

1. Choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, configure your target\. This example uses an Amazon SNS topic, but you can configure other target types to send notifications\.

   1. For **Target**, choose **SNS topic**\.

   1. For **Topic**, choose your target topic\.

   1. Choose **Next**\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.

## Configure device health notifications \(CLI\)<a name="create-events-rule-cli"></a>

Use the following steps to create an EventBridge rule that publishes an Amazon SNS topic when there is a Greengrass status change event\. This allows web servers, email addresses, and other topic subscribers to respond to the event\.

1. Create the rule\.
   + For deployment status change events\.

     ```
     aws events put-rule \
       --name TestRule \
       --event-pattern "{\"source\": [\"aws.greengrass\"], \"detail-type\": [\"Greengrass V2 Effective Deployment Status Change\"]}"
     ```
   + For component status change events\.

     ```
     aws events put-rule \
       --name TestRule \
       --event-pattern "{\"source\": [\"aws.greengrass\"], \"detail-type\": [\"Greengrass V2 Installed Component Status Change\"]}"
     ```

   Properties that are omitted from the pattern are ignored\.

1. Add the topic as a rule target\.
   + Replace *topic\-arn* with the ARN of your Amazon SNS topic\.

   ```
   aws events put-targets \
     --rule TestRule \
     --targets "Id"="1","Arn"="topic-arn"
   ```
**Note**  
To allow Amazon EventBridge to call your target topic, you must add a resource\-based policy to your topic\. For more information, see [Amazon SNS permissions](https://docs.aws.amazon.com/eventbridge/latest/userguide/resource-based-policies-eventbridge.html#sns-permissions) in the *Amazon EventBridge User Guide*\.

For more information, see [Events and event patterns in EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eventbridge-and-event-patterns.html) in the *Amazon EventBridge User Guide*\.

## Configure device health notifications \(AWS CloudFormation\)<a name="create-events-rule-cloudformation"></a>

Use AWS CloudFormation templates to create EventBridge rules that send notifications about state changes for your Greengrass group deployments\. For more information, see [Amazon EventBridge resource type reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_Events.html) in the *AWS CloudFormation User Guide*\.

## See also<a name="deployment-notifications-see-also"></a>
+ [Check device deployment status](check-deployment-status.md#check-device-deployment-status)
+ [What is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/what-is-amazon-eventbridge.html) in the *Amazon EventBridge User Guide*