# Shadow manager<a name="shadow-manager-component"></a>

The shadow manager component \(`aws.greengrass.ShadowManager`\) enables the local shadow service on your core device\. The local shadow service allows components to use interprocess communication to [interact with local shadows](ipc-local-shadows.md)\. The shadow manager component manages the storage of local shadow documents, and also handles synchronization of local shadow states with the AWS IoT Device Shadow service\.

For more information about how AWS IoT Greengrass devices can interact with shadows, see [Interact with device shadows](interact-with-shadows.md)\.

## Versions<a name="shadow-manager-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="shadow-manager-component-type"></a>

<a name="public-component-type-plugin-para1"></a>This component is a plugin component \(`aws.greengrass.plugin`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component in the same Java Virtual Machine \(JVM\) as the nucleus\. The nucleus restarts when you install or restart this component\.

<a name="public-component-type-plugin-para2"></a>This component uses the same log file as the Greengrass nucleus\. For more information, see [View AWS IoT Greengrass Core software logs](troubleshooting.md#view-greengrass-core-logs)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](manage-components.md#component-types)\.

## Requirements<a name="shadow-manager-component-requirements"></a>

This component has the following requirements:
+ The Greengrass core device's AWS IoT policy configured to allow the following AWS IoT Core shadow policy actions that are required to sync shadows to the AWS IoT Device Shadow service\.
  + `iot:GetThingShadow`
  + `iot:UpdateThingShadow`
  + `iot:DeleteThingShadow`

  For more information about these AWS IoT Core policies, see [AWS IoT Core policy actions](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policy-actions.html) in the *AWS IoT Developer Guide*\.

  For more information about the minimal AWS IoT policy, see [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy)

## Dependencies<a name="shadow-manager-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#shadow-manager-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for version 2\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | \~2\.2\.0 | Soft | 

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="shadow-manager-component-config"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`synchronize`  
\(Optional\) The synchronization settings that determine how shadows are synced with the AWS Cloud\.   
You must create a configuration update with this property if you want to enable syncing shadows with the AWS Cloud\.
This object contains the following information\.    
`coreThing`  
The core device shadows to sync\. This object contains the following information\.    
`classic`  
By default, the shadow manager syncs the local state of the classic shadow for your core device with the AWS Cloud\. If you don't want to sync the classic device shadow, set this to `false`\.  
Default: `true`  
`namedShadows`  
The list of named core device shadows that you want to sync\.   
`shadowDocuments`  
The list of additional device shadows to sync\. Each object in this list contains the following information\.     
`thingName`  
The thing name of the device for which to sync shadows\.   
`classic`  
If you don't want to sync the classic device shadow for the `thingName` device, set this to `false`\.  
Default: `true`  
`namedShadows`  
The list of named device shadows that you want to sync\. 

`rateLimits`  
The settings that determine the rate limits for shadow service requests\.  
This object contains the following information\.    
`maxOutboundSyncUpdatesPerSecond`  
The maximum number of sync requests per second that the device transmits\.   
Default: 100 requests/second  
`maxTotalLocalRequestsRate`  
The maximum number of local IPC requests per second that are sent to the core device\.   
Default: 200 requests/second  
`maxLocalRequestsPerSecondPerThing`  
The maximum number of local IPC requests per second that are sent for each connected IoT thing\.   
Default: 20 requests/second for each thing
These rate limits parameters define the maximum number of requests per second for the local shadow service\. The maximum number of requests per second for the AWS IoT Device Shadow service depends on your AWS Region\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.

`shadowDocumentSizeLimitBytes`  
The maximum allowed size of each JSON state document for local shadows\.   
If you increase this value, you must also increase the resource limit for the JSON state document for cloud shadows\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.  
Default: 8192 bytes  
Maximum: 30720 bytes

**Example: Configuration merge update**  
The following example shows a sample configuration merge update with all available configuration parameters for the shadow manager component\.  

```
{
  "synchronize":{
    "coreThing":{
      "classic":true,
      "namedShadows":[
        "MyCoreShadowA",
        "MyCoreShadowB"
      ]
    },
    "shadowDocuments":[
      {
        "thingName":"MyDevice1",
        "classic":false,
        "namedShadows":[
          "MyShadowA",
          "MyShadowB"
        ]
      },
      {
        "thingName":"MyDevice2",
        "classic":true,
        "namedShadows":[ ]
      }
    ]
  },
  "rateLimits": {       
          "maxOutboundSyncUpdatesPerSecond":100,
          "maxTotalLocalRequestsRate":200,
          "maxLocalRequestsPerSecondPerThing":20
   },
  "shadowDocumentSizeLimitBytes":8192
}
```

## Changelog<a name="shadow-manager-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  Version  |  Changes  | 
| --- | --- | 
|  2\.0\.0  |  Initial version\.  | 