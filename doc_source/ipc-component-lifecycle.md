# Interact with component lifecycle<a name="ipc-component-lifecycle"></a>

The component lifecycle IPC service lets you do the following:
+ Update component state on the core device
+ Subscribe to component state updates
+ Prevent the nucleus from stopping the component to apply an update

**Topics**
+ [Operations](#ipc-component-lifecycle-operations)

## Operations<a name="ipc-component-lifecycle-operations"></a>

Use the following operations to manage component lifecycle\.

**Topics**
+ [UpdateState](#ipc-operation-updatestate)
+ [SubscribeToComponentUpdates](#ipc-operation-subscribetocomponentupdates)
+ [DeferComponentUpdate](#ipc-operation-defercomponentupdate)

### UpdateState<a name="ipc-operation-updatestate"></a>

Update the state of the component on the core device\.

#### Request<a name="ipc-operation-updatestate-request"></a>

This operation's request has the following parameters:

`state`  
The state to set\. This enum, `LifecycleState`, has the following values:  
+ `RUNNING`
+ `ERRORED`

#### Response<a name="ipc-operation-updatestate-response"></a>

This operation doesn't provide any information in its response\.

### SubscribeToComponentUpdates<a name="ipc-operation-subscribetocomponentupdates"></a>

Subscribe to receive notifications before the AWS IoT Greengrass Core software updates a component\. The notification specifies whether or not the nucleus will restart as part of the update\.

The nucleus sends update notifications only if the deployment's component update policy specifies to notify components\. The default behavior is to notify components\. For more information, see [Create deployments](create-deployments.md) and the [ComponentUpdatePolicy](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ComponentUpdatePolicy.html) object that you can provide when you call the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation\.

**Important**  
Local deployments don't notify components before updates\.

#### Request<a name="ipc-operation-subscribetocomponentupdates-request"></a>

This operation's request doesn't have any parameters\.

#### Response<a name="ipc-operation-subscribetocomponentupdates-response"></a>

This operation's response has the following information:

`messages`  
The stream of notification messages\. This object, `ComponentUpdatePolicyEvents`, contains the following information:    
`preUpdateEvent`  
\(Optional\) An event that indicates that the nucleus wants to update a component\. You can respond with the [DeferComponentUpdate](#ipc-operation-defercomponentupdate) operation to acknowledge or defer the update until your component is ready to restart\. This object, `PreComponentUpdateEvent`, contains the following information:    
`deploymentId`  
The ID of the AWS IoT Greengrass deployment that updates the component\.  
`isGgcRestarting`  
Whether or not the nucleus needs to restart to apply the update\.  
`postUpdateEvent`  
\(Optional\) An event that indicates that the nucleus updated a component\. This object, `PostComponentUpdateEvent`, contains the following information:    
`deploymentId`  
The ID of the AWS IoT Greengrass deployment that updated the component\.

### DeferComponentUpdate<a name="ipc-operation-defercomponentupdate"></a>

Acknowledge or defer a component update that you discover with [SubscribeToComponentUpdates](#ipc-operation-subscribetocomponentupdates)\. You specify the amount of time to wait before the nucleus checks again if your component is ready to let the component update proceed\. You can also use this operation to tell the nucleus that your component is ready for the update\.

If a component doesn't respond to the component update notification, the nucleus waits the amount of time that you specify in the deployment's component update policy\. After that timeout, the nucleus proceeds with the deployment\. The default component update timeout is 60 seconds\. For more information, see [Create deployments](create-deployments.md) and the [ComponentUpdatePolicy](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ComponentUpdatePolicy.html) object that you can provide when you call the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation\.

#### Request<a name="ipc-operation-defercomponentupdate-request"></a>

This operation's request has the following parameters:

`deploymentId`  
The ID of the AWS IoT Greengrass deployment to defer\.

`message`  
\(Optional\) The name of the component for which to defer updates\.  
Defaults to the name of the component that makes the request\.

`recheckAfterMs`  
The amount of time in milliseconds for which to defer the update\. The nucleus waits for this amount of time and then sends another `PreComponentUpdateEvent` that you can discover with [SubscribeToComponentUpdates](#ipc-operation-subscribetocomponentupdates)\.  
Specify `0` to acknowledge the update\. This tells the nucleus that your component is ready for the update\.  
Defaults to zero milliseconds, which means to acknowledge the update\.

#### Response<a name="ipc-operation-defercomponentupdate-response"></a>

This operation doesn't provide any information in its response\.