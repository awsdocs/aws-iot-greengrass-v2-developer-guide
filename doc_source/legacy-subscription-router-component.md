# Legacy subscription router<a name="legacy-subscription-router-component"></a>

The legacy subscription router \(`aws.greengrass.LegacySubscriptionRouter`\) manages subscriptions on the Greengrass core device\. Subscriptions are a feature of AWS IoT Greengrass V1 that define the topics that Lambda functions can use for MQTT messaging on a core device\. For more information, see [Managed subscriptions in the MQTT messaging workflow](https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-sec.html#gg-msg-workflow) in the *AWS IoT Greengrass V1 Developer Guide*\.

You can use this component to enable subscriptions for connector components and Lambda function components that use the AWS IoT Greengrass Core SDK\.

**Note**  <a name="legacy-subscription-router-requirement-note"></a>
This component is required only if your Lambda function uses the `publish()` function in the AWS IoT Greengrass Core SDK\. If you update your Lambda function code to use the interprocess communication \(IPC\)s interface in the AWS IoT Device SDK, you don't need to deploy the legacy subscription router component\. For more information, see the following [interprocess communication](interprocess-communication.md) services:  
[Publish/subscribe local messages](ipc-publish-subscribe.md)
[Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)

**Topics**
+ [Versions](#legacy-subscription-router-component-versions)
+ [Dependencies](#legacy-subscription-router-component-dependencies)
+ [Configuration](#legacy-subscription-router-component-configuration)
+ [Changelog](#legacy-subscription-router-component-changelog)

## Versions<a name="legacy-subscription-router-component-versions"></a>

This component has the following versions:
+ 2\.1\.x
+ 2\.0\.x

## Dependencies<a name="legacy-subscription-router-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. You must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#legacy-subscription-router-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.1\.x ]

The following table lists the dependencies for version 2\.1\.x and later versions of this component\.


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

## Configuration<a name="legacy-subscription-router-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ v2\.1\.x ]

`subscriptions`  
<a name="legacy-subscription-router-component-configuration-subscriptions-intro"></a>\(Optional\) The subscriptions to enable on the core device\. This is an object, where each key is a unique ID, and each value is an object that defines the subscription for that connector\. You must configure a subscription when you deploy a V1 connector component or a Lambda function that uses the AWS IoT Greengrass Core SDK\.  
Each subscription object contains the following information:    
`id`  <a name="legacy-subscription-router-component-configuration-subscription-id"></a>
The unique ID of this subscription\. This ID must match the key for this subscription object\.  
`source`  
The Lambda function that uses the AWS IoT Greengrass Core SDK to publish MQTT messages on the topics that you specify in `subject`\. Specify one of the following:  
+ <a name="legacy-subscription-router-component-configuration-subscription-source-target-component-name"></a>The name of a Lambda function component on the core device\. Specify the component name with the `component:` prefix, such as **component:com\.example\.HelloWorldLambda**\.
+ <a name="legacy-subscription-router-component-configuration-subscription-source-target-function-arn"></a>The Amazon Resource Name \(ARN\) of a Lambda function on the core device\.
**Important**  <a name="legacy-subscription-router-function-version-requirement"></a>
If you specify an ARN, you must configure the subscription each time that you deploy a new version of the Lambda function\. This component won't route the messages until the version matches the subscription\.  
The ARN must include the version of the function to import\. You can't use version aliases like `$LATEST`\.
To deploy a subscription for a V1 connector component, specify the name of the component or the ARN of the connector component's Lambda function\.  
`subject`  <a name="legacy-subscription-router-component-configuration-subscription-subject"></a>
The MQTT topic or topic filter on which the source and target can publish and receive messages\. This value supports the `+` and `#` topic wildcards\.  
`target`  
<a name="legacy-subscription-router-component-configuration-subscription-target-intro"></a>The target that receives the MQTT messages on the topics that you specify in `subject`\. The subscription specifies that the `source` function publishes MQTT messages to AWS IoT Core or to a Lambda function on the core device\. Specify one of the following:  
+ <a name="legacy-subscription-router-component-configuration-subscription-target-cloud"></a>`cloud`\. The `source` function publishes MQTT messages to AWS IoT Core\.
+ <a name="legacy-subscription-router-component-configuration-subscription-source-target-component-name"></a>The name of a Lambda function component on the core device\. Specify the component name with the `component:` prefix, such as **component:com\.example\.HelloWorldLambda**\.
+ <a name="legacy-subscription-router-component-configuration-subscription-source-target-function-arn"></a>The Amazon Resource Name \(ARN\) of a Lambda function on the core device\.
**Important**  <a name="legacy-subscription-router-function-version-requirement"></a>
If you specify an ARN, you must configure the subscription each time that you deploy a new version of the Lambda function\. This component won't route the messages until the version matches the subscription\.  
The ARN must include the version of the function to import\. You can't use version aliases like `$LATEST`\.
Default: No subscriptions

**Example subscription to AWS IoT Core**  
The following example specifies that the `com.example.HelloWorldLambda` Lambda function component publishes MQTT message to AWS IoT Core on the `hello/world` topic\.  

```
{
  "subscriptions": {
    "Greengrass_HelloWorld_to_cloud": {
      "id": "Greengrass_HelloWorld_to_cloud",
      "source": "component:com.example.HelloWorldLambda",
      "subject": "hello/world",
      "target": "cloud"
    }
  }
}
```

**Example subscription to another Lambda function**  
The following example specifies that the `com.example.HelloWorldLambda` Lambda function component publishes MQTT messages to the `com.example.MessageRelay` Lambda function component on the `hello/world` topic\.  

```
{
  "subscriptions": {
    "Greengrass_HelloWorld_to_cloud": {
      "id": "Greengrass_HelloWorld_to_cloud",
      "source": "component:com.example.HelloWorldLambda",
      "subject": "hello/world",
      "target": "component:com.example.MessageRelay"
    }
  }
}
```

------
#### [ v2\.0\.x ]

`subscriptions`  
<a name="legacy-subscription-router-component-configuration-subscriptions-intro"></a>\(Optional\) The subscriptions to enable on the core device\. This is an object, where each key is a unique ID, and each value is an object that defines the subscription for that connector\. You must configure a subscription when you deploy a V1 connector component or a Lambda function that uses the AWS IoT Greengrass Core SDK\.  
Each subscription object contains the following information:    
`id`  <a name="legacy-subscription-router-component-configuration-subscription-id"></a>
The unique ID of this subscription\. This ID must match the key for this subscription object\.  
`source`  
The Lambda function that uses the AWS IoT Greengrass Core SDK to publish MQTT messages on the topics that you specify in `subject`\. Specify the following:  
+ <a name="legacy-subscription-router-component-configuration-subscription-source-target-function-arn"></a>The Amazon Resource Name \(ARN\) of a Lambda function on the core device\.
**Important**  <a name="legacy-subscription-router-function-version-requirement"></a>
If you specify an ARN, you must configure the subscription each time that you deploy a new version of the Lambda function\. This component won't route the messages until the version matches the subscription\.  
The ARN must include the version of the function to import\. You can't use version aliases like `$LATEST`\.
To deploy a subscription for a V1 connector component, specify the ARN of the connector component's Lambda function\.  
`subject`  <a name="legacy-subscription-router-component-configuration-subscription-subject"></a>
The MQTT topic or topic filter on which the source and target can publish and receive messages\. This value supports the `+` and `#` topic wildcards\.  
`target`  
<a name="legacy-subscription-router-component-configuration-subscription-target-intro"></a>The target that receives the MQTT messages on the topics that you specify in `subject`\. The subscription specifies that the `source` function publishes MQTT messages to AWS IoT Core or to a Lambda function on the core device\. Specify one of the following:  
+ <a name="legacy-subscription-router-component-configuration-subscription-target-cloud"></a>`cloud`\. The `source` function publishes MQTT messages to AWS IoT Core\.
+ <a name="legacy-subscription-router-component-configuration-subscription-source-target-function-arn"></a>The Amazon Resource Name \(ARN\) of a Lambda function on the core device\.
**Important**  <a name="legacy-subscription-router-function-version-requirement"></a>
If you specify an ARN, you must configure the subscription each time that you deploy a new version of the Lambda function\. This component won't route the messages until the version matches the subscription\.  
The ARN must include the version of the function to import\. You can't use version aliases like `$LATEST`\.
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

------

## Changelog<a name="legacy-subscription-router-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  Version  |  Changes  | 
| --- | --- | 
|  2\.1\.0  |  <a name="changelog-legacy-subscription-router-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/legacy-subscription-router-component.html)  | 
|  2\.0\.3  |  Initial version\.  | 