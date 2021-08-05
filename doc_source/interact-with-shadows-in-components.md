# Interact with shadows in components<a name="interact-with-shadows-in-components"></a>

You can develop custom components, including Lambda function components, that use the local shadow service to read and modify local shadow documents and perform local state management of connected devices\. 

Custom components interact with the local shadow service using the AWS IoT Greengrass Core IPC libraries in the AWS IoT Device SDK\. The [shadow manager](shadow-manager-component.md) component enables the local shadow service on your core device\.

To deploy the shadow manager component to a Greengrass core device, [create a deployment](create-deployments.md) that includes the `aws.greengrass.ShadowManager` component\.

**Note**  
By default, deploying the shadow manager component enables local shadow operations only\. To enable AWS IoT Greengrass to sync shadow state information for core device shadows or any shadows for connected devices to the corresponding cloud shadow documents in AWS IoT Core, you must create a configuration update for the shadow manager component that includes the `synchronize` parameter\. For more information, see [Sync local device shadows with AWS IoT Core](sync-shadows-with-iot-core.md)\.

**Topics**
+ [Retrieve and modify shadow states](#interact-shadow-states)
+ [React to shadow state changes](#react-shadow-events)

## Retrieve and modify shadow states<a name="interact-shadow-states"></a>

The shadow IPC operations retrieve and update state information in local shadow documents\. The shadow manager component handles the storage of these shadow documents on your core device\.

**To modify local shadow states**

1. Add access control policies to the recipe for your custom component to allow the component to receive messages on local shadow topics\. For example, the following example access control policy allows the component to create, update, and delete the classic device shadow and the named shadow `myNamedShadow` for the device `MyThingName`\.

   ```
   {
     "accessControl": {
       "aws.greengrass.ShadowManager": {
         "policyId1": {
           "policyDescription": "Allows access to shadows",
           "operations": [
             "aws.greengrass#GetThingShadow",
             "aws.greengrass#UpdateThingShadow",
             "aws.greengrass#DeleteThingShadow"
           ],
           "resources": [
             "$aws/things/MyThingName/shadow",
             "$aws/things/MyThingName/shadow/name/myNamedShadow"
           ]
         }    
       }
     }
   }
   ```

1. Use the shadow IPC operations to retrieve and modify shadow state information\. For more information about using shadow IPC operations in component code, see [Interact with local shadows](ipc-local-shadows.md)\.

## React to shadow state changes<a name="react-shadow-events"></a>

Greengrass components use the local publish/subscribe interface to communicate on a core device\. To enable a custom component to react to shadow state changes, you can subscribe to the local publish/subscribe topics\. This allows the component to receive messages on the local shadow topics, and then act on those messages\. 

Local shadow topics use the same format as the AWS IoT device shadow MQTT topics\. For more information about shadow topics, see [Device Shadow MQTT topics](https://docs.aws.amazon.com/iot/latest/developerguide/device-shadow-mqtt.html) in the *AWS IoT Developer Guide*\.

**To react to local shadow state changes**

1. Add access control policies to the recipe for your custom component to allow the component to receive messages on local shadow topics\. For example, the following example access control policy allows the custom `com.example.MyShadowReactiveComponent` to receive messages on the `/update/delta` topic for the classic device shadow and the named shadow `myNamedShadow` for the device `MyThingName`\.

   ```
   {
     "accessControl": {
       "aws.greengrass.ipc.pubsub": {
         "com.example.MyShadowReactivenComponent:pubsub:1": {
           "policyDescription": "Allows access to shadow pubsub topics",
           "operations": [
             "aws.greengrass#SubscribeToTopic"
           ],
           "resources": [
             "$aws/things/MyThingName/shadow/update/delta",
             "$aws/things/MyThingName/shadow/name/myNamedShadow/update/delta"
           ]
         }
       }
     }
   }
   ```

1. To initiate a custom action in a component, use `SubscribeToTopic` IPC operations to subscribe to the shadow topics on which you want to receive messages\. For more information about using local publish/subscribe IPC operations in component code, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.

1. To invoke a Lambda function, use the event source configuration to provide the name of the shadow topic and specify that it's a local publish/subscribe topic\. For information about creating Lambda function components, see [Run AWS Lambda functions](run-lambda-functions.md)\.