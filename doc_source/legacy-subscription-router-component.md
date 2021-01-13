# Legacy subscription router<a name="legacy-subscription-router-component"></a>

The legacy subscription router \(`aws.greengrass.LegacySubscriptionRouter`\) manages subscriptions on the Greengrass core device\. Subscriptions are a feature of AWS IoT Greengrass V1 that define the topics that Lambda functions can use for MQTT messaging on a core device\. For more information, see [Managed subscriptions in the MQTT messaging workflow](https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-sec.html#gg-msg-workflow) in the *AWS IoT Greengrass V1 Developer Guide*\.

You can use this component to enable subscriptions for connector components and Lambda function components that use the AWS IoT Greengrass Core SDK\.

**Note**  <a name="legacy-subscription-router-requirement-note"></a>
This component is required only if your Lambda function uses the `publish()` function in the AWS IoT Greengrass Core SDK\. If you update your Lambda function code to use the V2 SDK, you don't need to deploy the legacy subscription router component\. For more information, see the following [interprocess communication](interprocess-communication.md) services:  
[Publish/subscribe local messages](ipc-publish-subscribe.md)
[Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)

This component has the following versions:
+ 2\.0\.x

## Configuration<a name="legacy-subscription-router-component-configuration"></a>

This component provides the following configuration parameters that you can customize\.

`subscriptions`  
\(Optional\) The subscriptions to enable on the core device\. This is an object, where each key is a unique ID, and each value is an object that defines the subscription for that connector\. You must configure the subscription when you deploy a V1 connector component\.  
Each subscription object contains the following information:    
`id`  
The unique ID of this subscription\. This ID must match the key for this subscription object\.  
`source`  
The subscription source that publishes MQTT messages on the topics that you specify in `subject`\. When you deploy V1 connector components, this value is the Amazon Resource Name \(ARN\) of the Lambda function that contains the connector software\.  
If the version of the Lambda function changes, you must configure the subscription with the new version of the function\. Otherwise, this component won't route the messages until the version matches the subscription\.  
You must specify an ARN that includes the version of the function to import\. You can't use version aliases like `$LATEST`\.  
`subject`  
The MQTT topic or topic filter on which the source and target can publish and receive messages\. This value supports the `+` and `#` topic wildcards\.  
`target`  
The target that receives the MQTT messages on the topics that you specify in `subject`\. Specify one of the following:  
+ `cloud`\. The subscription specifies that the `source` function publishes MQTT messages to AWS IoT Core\.
+ The ARN of a Lambda function on the core device\. The subscription specifies that the `source` function publishes MQTT messages to this target Lambda function\.
**Important**  <a name="legacy-subscription-router-function-version-requirement"></a>
If the version of the Lambda function changes, you must configure the subscription with the new version of the function\. Otherwise, this component won't route the messages until the version matches the subscription\.  
You must specify an ARN that includes the version of the function to import\. You can't use version aliases like `$LATEST`\.
Default: No subscriptions

**Example subscription to AWS IoT Core**  
The following example specifies that the `Greengrass_HelloWorld` function publishes MQTT message to AWS IoT Core on the `hello/world` topic\.  

```
"subscriptions": {
  "Greengrass_HelloWorld_to_cloud": {
    "id": "Greengrass_HelloWorld_to_cloud",
    "source": "arn:aws:lambda:us-west-2:123456789012:function:Greengrass_HelloWorld:5",
    "subject": "hello/world",
    "target": "cloud"
  }
}
```

**Example subscription to another Lambda function**  
The following example specifies that the `Greengrass_HelloWorld` function publishes MQTT messages to the `Greengrass_MessageRelay` on the `hello/world` topic\.  

```
"subscriptions": {
  "Greengrass_HelloWorld_to_cloud": {
    "id": "Greengrass_HelloWorld_to_cloud",
    "source": "arn:aws:lambda:us-west-2:123456789012:function:Greengrass_HelloWorld:5",
    "subject": "hello/world",
    "target": "arn:aws:lambda:us-west-2:123456789012:function:Greengrass_MessageRelay:5"
  }
}
```