# Interact with component lifecycle<a name="ipc-component-lifecycle"></a>

Use the component lifecycle IPC service to:
+ Update the component state on the core device\.
+ Subscribe to component state updates\.
+ Prevent the nucleus from stopping the component to apply an update during a deployment\.
+ Pause and resume component processes\.

**Topics**
+ [Minimum SDK versions](#ipc-component-lifecycle-sdk-versions)
+ [Authorization](#ipc-component-lifecycle-authorization)
+ [UpdateState](#ipc-operation-updatestate)
+ [SubscribeToComponentUpdates](#ipc-operation-subscribetocomponentupdates)
+ [DeferComponentUpdate](#ipc-operation-defercomponentupdate)
+ [PauseComponent](#ipc-operation-pausecomponent)
+ [ResumeComponent](#ipc-operation-resumecomponent)

## Minimum SDK versions<a name="ipc-component-lifecycle-sdk-versions"></a>

The following table lists the minimum versions of the AWS IoT Device SDK that you must use to interact with component lifecycle\.


| SDK | Minimum version | 
| --- | --- | 
|  [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2)  |  v1\.2\.10  | 
|  [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2)  |  v1\.5\.3  | 
|  [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2)  |  Linux: v1\.13\.0; Windows: v1\.14\.6  | 

## Authorization<a name="ipc-component-lifecycle-authorization"></a>

To pause or resume other components from a custom component, you must define authorization policies that allows your component to manage other components\. For information about defining authorization policies, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies)\.

Authorization policies for component lifecycle management have the following properties\.

**IPC service identifier:** `aws.greengrass.ipc.lifecycle`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#PauseComponent`  |  Allows a component to pause the components that you specify\.  |  A component name, or `*` to allow access to all components\.  | 
|  `aws.greengrass#ResumeComponent`  |  Allows a component to resume the components that you specify\.  |  A component name, or `*` to allow access to all components\.  | 
|  `*`  |  Allows a component to pause and resume the components that you specify\.  |  A component name, or `*` to allow access to all components\.  | 

**Example authorization policy**  
The following example authorization policy allows a component to pause and resume all components\.  

```
{
  "accessControl": {
    "aws.greengrass.ipc.lifecycle": {
      "com.example.MyLocalLifecycleComponent:lifecycle:1": {
        "policyDescription": "Allows access to pause/resume all components.",
        "operations": [
          "aws.greengrass#PauseComponent",
          "aws.greengrass#ResumeComponent"
        ],
        "resources": [
          "*"
        ]
      }
    }
  }
}
```

## UpdateState<a name="ipc-operation-updatestate"></a>

Update the state of the component on the core device\.

### Request<a name="ipc-operation-updatestate-request"></a>

This operation's request has the following parameters:

`state`  
The state to set\. This enum, `LifecycleState`, has the following values:  
+ `RUNNING`
+ `ERRORED`

### Response<a name="ipc-operation-updatestate-response"></a>

This operation doesn't provide any information in its response\.

## SubscribeToComponentUpdates<a name="ipc-operation-subscribetocomponentupdates"></a>

Subscribe to receive notifications before the AWS IoT Greengrass Core software updates a component\. The notification specifies whether or not the nucleus will restart as part of the update\.

The nucleus sends update notifications only if the deployment's component update policy specifies to notify components\. The default behavior is to notify components\. For more information, see [Create deployments](create-deployments.md) and the [DeploymentComponentUpdatePolicy](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_DeploymentComponentUpdatePolicy.html) object that you can provide when you call the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation\.

**Important**  
Local deployments don't notify components before updates\.

<a name="ipc-subscribe-operation-note"></a>This operation is a subscription operation where you subscribe to a stream of event messages\. To use this operation, define a stream response handler with functions that handle event messages, errors, and stream closure\. For more information, see [Subscribe to IPC event streams](interprocess-communication.md#ipc-subscribe-operations)\.

**Event message type:** `ComponentUpdatePolicyEvents`

### Request<a name="ipc-operation-subscribetocomponentupdates-request"></a>

This operation's request doesn't have any parameters\.

### Response<a name="ipc-operation-subscribetocomponentupdates-response"></a>

This operation's response has the following information:

`messages`  
The stream of notification messages\. This object, `ComponentUpdatePolicyEvents`, contains the following information:    
`preUpdateEvent` \(Python: `pre_update_event`\)  
\(Optional\) An event that indicates that the nucleus wants to update a component\. You can respond with the [DeferComponentUpdate](#ipc-operation-defercomponentupdate) operation to acknowledge or defer the update until your component is ready to restart\. This object, `PreComponentUpdateEvent`, contains the following information:    
`deploymentId` \(Python: `deployment_id`\)  
The ID of the AWS IoT Greengrass deployment that updates the component\.  
`isGgcRestarting` \(Python: `is_ggc_restarting`\)  
Whether or not the nucleus needs to restart to apply the update\.  
`postUpdateEvent` \(Python: `post_update_event`\)  
\(Optional\) An event that indicates that the nucleus updated a component\. This object, `PostComponentUpdateEvent`, contains the following information:    
`deploymentId` \(Python: `deployment_id`\)  
The ID of the AWS IoT Greengrass deployment that updated the component\.

## DeferComponentUpdate<a name="ipc-operation-defercomponentupdate"></a>

Acknowledge or defer a component update that you discover with [SubscribeToComponentUpdates](#ipc-operation-subscribetocomponentupdates)\. You specify the amount of time to wait before the nucleus checks again if your component is ready to let the component update proceed\. You can also use this operation to tell the nucleus that your component is ready for the update\.

If a component doesn't respond to the component update notification, the nucleus waits the amount of time that you specify in the deployment's component update policy\. After that timeout, the nucleus proceeds with the deployment\. The default component update timeout is 60 seconds\. For more information, see [Create deployments](create-deployments.md) and the [DeploymentComponentUpdatePolicy](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_DeploymentComponentUpdatePolicy.html) object that you can provide when you call the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation\.

### Request<a name="ipc-operation-defercomponentupdate-request"></a>

This operation's request has the following parameters:

`deploymentId` \(Python: `deployment_id`\)  
The ID of the AWS IoT Greengrass deployment to defer\.

`message`  
\(Optional\) The name of the component for which to defer updates\.  
Defaults to the name of the component that makes the request\.

`recheckAfterMs` \(Python: `recheck_after_ms`\)  
The amount of time in milliseconds for which to defer the update\. The nucleus waits for this amount of time and then sends another `PreComponentUpdateEvent` that you can discover with [SubscribeToComponentUpdates](#ipc-operation-subscribetocomponentupdates)\.  
Specify `0` to acknowledge the update\. This tells the nucleus that your component is ready for the update\.  
Defaults to zero milliseconds, which means to acknowledge the update\.

### Response<a name="ipc-operation-defercomponentupdate-response"></a>

This operation doesn't provide any information in its response\.

## PauseComponent<a name="ipc-operation-pausecomponent"></a>

This feature is available for v2\.4\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 

Pauses a component's processes on the core device\. To resume a component, use the [ResumeComponent](#ipc-operation-resumecomponent) operation\.

You can pause only generic components\. If you try to pause any other type of component, this operation throws an `InvalidRequestError`\.

**Note**  
This operation can't pause containerized processes, such as Docker containers\. To pause and resume a Docker container, you can use the [docker pause](https://docs.docker.com/engine/reference/commandline/pause/) and [docker resume](https://docs.docker.com/engine/reference/commandline/unpause/) commands\.

This operation doesn't pause component dependencies or components that depend on the component that you pause\. Consider this behavior when you pause a component that is a dependency of another component, because the dependent component might encounter issues when its dependency is paused\.

When you restart or shut down a paused component, such as through a deployment, the Greengrass nucleus resumes the component and runs its shutdown lifecycle\.

**Important**  
To use this operation, you must define an authorization policy that grants permission to use this operation\. For more information, see [Authorization](#ipc-component-lifecycle-authorization)\.

### Minimum SDK versions<a name="ipc-operation-pausecomponent-sdk-versions"></a>

The following table lists the minimum versions of the AWS IoT Device SDK that you must use to pause and resume components\.


| SDK | Minimum version | 
| --- | --- | 
|  [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2)  |  v1\.4\.3  | 
|  [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2)  |  v1\.6\.2  | 
|  [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2)  |  v1\.13\.1  | 

### Request<a name="ipc-operation-defercomponentupdate-request"></a>

This operation's request has the following parameters:

`componentName` \(Python: `component_name`\)  
The name of the component to pause, which must be a generic component\. For more information, see [Component types](develop-greengrass-components.md#component-types)\.

### Response<a name="ipc-operation-defercomponentupdate-response"></a>

This operation doesn't provide any information in its response\.

## ResumeComponent<a name="ipc-operation-resumecomponent"></a>

This feature is available for v2\.4\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 

Resumes a component's processes on the core device\. To pause a component, use the [PauseComponent](#ipc-operation-pausecomponent) operation\.

You can resume only paused components\. If you try to resume a component that isn't paused, this operation throws an `InvalidRequestError`\.

**Important**  
To use this operation, you must define an authorization policy that grants permission to do so\. For more information, see [Authorization](#ipc-component-lifecycle-authorization)\.

### Minimum SDK versions<a name="ipc-operation-resumecomponent-sdk-versions"></a>

The following table lists the minimum versions of the AWS IoT Device SDK that you must use to pause and resume components\.


| SDK | Minimum version | 
| --- | --- | 
|  [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2)  |  v1\.4\.3  | 
|  [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2)  |  v1\.6\.2  | 
|  [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2)  |  v1\.13\.1  | 

### Request<a name="ipc-operation-defercomponentupdate-request"></a>

This operation's request has the following parameters:

`componentName` \(Python: `component_name`\)  
The name of the component to resume\.

### Response<a name="ipc-operation-defercomponentupdate-response"></a>

This operation doesn't provide any information in its response\.