# Shadow manager<a name="shadow-manager-component"></a>

The shadow manager component \(`aws.greengrass.ShadowManager`\) enables the local shadow service on your core device\. The local shadow service allows components to use interprocess communication to [interact with local shadows](ipc-local-shadows.md)\. The shadow manager component manages the storage of local shadow documents, and also handles synchronization of local shadow states with the AWS IoT Device Shadow service\.

For more information about how Greengrass core devices can interact with shadows, see [Interact with device shadows](interact-with-shadows.md)\.

**Topics**
+ [Versions](#shadow-manager-component-versions)
+ [Type](#shadow-manager-component-type)
+ [Operating system](#shadow-manager-component-os-support)
+ [Requirements](#shadow-manager-component-requirements)
+ [Dependencies](#shadow-manager-component-dependencies)
+ [Configuration](#shadow-manager-component-configuration)
+ [Local log file](#shadow-manager-component-log-file)
+ [Changelog](#shadow-manager-component-changelog)

## Versions<a name="shadow-manager-component-versions"></a>

This component has the following versions:
+ 2\.3\.x
+ 2\.2\.x
+ 2\.1\.x
+ 2\.0\.x

## Type<a name="shadow-manager-component-type"></a>

<a name="public-component-type-plugin-para1"></a>This component is a plugin component \(`aws.greengrass.plugin`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component in the same Java Virtual Machine \(JVM\) as the nucleus\. The nucleus restarts when you change this component's version on the core device\.

<a name="public-component-type-plugin-para2"></a>This component uses the same log file as the Greengrass nucleus\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="shadow-manager-component-os-support"></a>

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

## Requirements<a name="shadow-manager-component-requirements"></a>

This component has the following requirements:
+ \(Optional\) To sync shadows to the AWS IoT Device Shadow service, the Greengrass core device's AWS IoT policy must allow the following AWS IoT Core shadow policy actions:
  + `iot:GetThingShadow`
  + `iot:UpdateThingShadow`
  + `iot:DeleteThingShadow`

  For more information about these AWS IoT Core policies, see [AWS IoT Core policy actions](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policy-actions.html) in the *AWS IoT Developer Guide*\.

  For more information about the minimal AWS IoT policy, see [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy)

## Dependencies<a name="shadow-manager-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#shadow-manager-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.3\.0 and 2\.3\.1 ]

The following table lists the dependencies for versions 2\.3\.0 and 2\.3\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.5\.0 <3\.0\.0 | Soft | 

------
#### [ 2\.2\.3 and 2\.2\.4 ]

The following table lists the dependencies for versions 2\.2\.3 and 2\.2\.4 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <3\.0\.0 | Soft | 

------
#### [ 2\.2\.2 ]

The following table lists the dependencies for version 2\.2\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.9\.0 | Soft | 

------
#### [ 2\.2\.1 ]

The following table lists the dependencies for version 2\.2\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.8\.0 | Soft | 

------
#### [ 2\.1\.1 and 2\.2\.0 ]

The following table lists the dependencies for versions 2\.1\.1 and 2\.2\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.7\.0 | Soft | 

------
#### [ 2\.0\.5 \- 2\.1\.0 ]

The following table lists the dependencies for versions 2\.0\.5 through 2\.1\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.6\.0 | Soft | 

------
#### [ 2\.0\.3 and 2\.0\.4 ]

The following table lists the dependencies for versions 2\.0\.3 and 2\.0\.4 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.5\.0 | Soft | 

------
#### [ 2\.0\.1 and 2\.0\.2 ]

The following table lists the dependencies for versions 2\.0\.1 and 2\.0\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.4\.0 | Soft | 

------
#### [ 2\.0\.0 ]

The following table lists the dependencies for version 2\.0\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.3\.0 | Soft | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="shadow-manager-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ 2\.3\.x ]

`strategy`  <a name="shadow-manager-component-configuration-strategy"></a>
\(Optional\) The strategy that this component uses to sync shadows between AWS IoT Core and the core device\.  
This object contains the following information\.    
`type`  
\(Optional\) The type of strategy that this component uses to sync shadows between AWS IoT Core and the core device\. Choose from the following options:  
+ `realTime` – Sync shadows with AWS IoT Core each time a shadow update occurs\.
+ `periodic` – Sync shadows with AWS IoT Core on a regular interval that you specify with the `delay` configuration parameter\.
Default: `realTime`  
`delay`  
\(Optional\) The interval in seconds where this component syncs shadows with AWS IoT Core, when you specify the `periodic` sync strategy\.  
This parameter is required if you specify the `periodic` sync strategy\.

`synchronize`  <a name="shadow-manager-component-configuration-synchronize"></a>
<a name="shadow-manager-component-configuration-strategy-description"></a>\(Optional\) The synchronization settings that determine how shadows are synced with the AWS Cloud\.   
You must create a configuration update with this property to sync shadows with the AWS Cloud\.
This object contains the following information\.    
`coreThing`  <a name="shadow-manager-component-configuration-synchronize-core-thing"></a>
\(Optional\) The core device shadows to sync\. This object contains the following information\.    
`classic`  
\(Optional\) By default, the shadow manager syncs the local state of the classic shadow for your core device with the AWS Cloud\. If you don't want to sync the classic device shadow, set this to `false`\.  
Default: `true`  
`namedShadows`  
\(Optional\) The list of named core device shadows to sync\. You must specify the exact names of the shadows\.  
The AWS IoT Greengrass service uses the `AWSManagedGreengrassV2Deployment` named shadow to manage deployments that target individual core devices\. This named shadow is reserved for use by the AWS IoT Greengrass service\. Do not update or delete this named shadow\.  
`shadowDocuments`  <a name="shadow-manager-component-configuration-synchronize-shadow-documents"></a>
\(Optional\) The list of additional device shadows to sync\. Each object in this list contains the following information\.     
`thingName`  
The thing name of the device for which to sync shadows\.   
`classic`  
\(Optional\) If you don't want to sync the classic device shadow for the `thingName` device, set this to `false`\.  
Default: `true`  
`namedShadows`  
\(Optional\) The list of named device shadows that you want to sync\. You must specify the exact names of the shadows\.  
`direction`  
\(Optional\) The direction to sync shadows between the local shadow service and the AWS Cloud\. You can configure this option to reduce bandwidth and connections to the AWS Cloud\. Choose from the following options:  
+ `betweenDeviceAndCloud` – Synchronize shadows between the local shadow service and the AWS Cloud\.
+ `deviceToCloud` – Send shadow updates from the local shadow service to the AWS Cloud, and ignore shadow updates from the AWS Cloud\.
+ `cloudToDevice` – Receive shadow updates from the AWS Cloud, and don't send shadow updates from the local shadow service to the AWS Cloud\.
Default: `BETWEEN_DEVICE_AND_CLOUD`

`rateLimits`  <a name="shadow-manager-component-configuration-rate-limits"></a>
\(Optional\) The settings that determine the rate limits for shadow service requests\.  
This object contains the following information\.    
`maxOutboundSyncUpdatesPerSecond`  
\(Optional\) The maximum number of sync requests per second that the device transmits\.   
Default: 100 requests/second  
`maxTotalLocalRequestsRate`  
\(Optional\) The maximum number of local IPC requests per second that are sent to the core device\.   
Default: 200 requests/second  
`maxLocalRequestsPerSecondPerThing`  
\(Optional\) The maximum number of local IPC requests per second that are sent for each connected IoT thing\.   
Default: 20 requests/second for each thing
These rate limits parameters define the maximum number of requests per second for the local shadow service\. The maximum number of requests per second for the AWS IoT Device Shadow service depends on your AWS Region\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.

`shadowDocumentSizeLimitBytes`  <a name="shadow-manager-component-configuration-shadow-document-size-limit-bytes"></a>
\(Optional\) The maximum allowed size of each JSON state document for local shadows\.   
If you increase this value, you must also increase the resource limit for the JSON state document for cloud shadows\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.  
Default: 8192 bytes  
Maximum: 30720 bytes

**Example: Configuration merge update**  
The following example shows a sample configuration merge update with all available configuration parameters for the shadow manager component\.  

```
{
  "strategy": {
    "type": "periodic",
    "delay": 300
  },
  "synchronize": {
    "coreThing": {
      "classic": true,
      "namedShadows": [
        "MyCoreShadowA",
        "MyCoreShadowB"
      ]
    },
    "shadowDocuments": [
      {
        "thingName": "MyDevice1",
        "classic": false,
        "namedShadows": [
          "MyShadowA",
          "MyShadowB"
        ]
      },
      {
        "thingName": "MyDevice2",
        "classic": true,
        "namedShadows": []
      }
    ],
    "direction": "betweenDeviceAndCloud"
  },
  "rateLimits": {       
    "maxOutboundSyncUpdatesPerSecond": 100,
    "maxTotalLocalRequestsRate": 200,
    "maxLocalRequestsPerSecondPerThing": 20
  },
  "shadowDocumentSizeLimitBytes": 8192
}
```

------
#### [ 2\.2\.x ]

`strategy`  <a name="shadow-manager-component-configuration-strategy"></a>
\(Optional\) The strategy that this component uses to sync shadows between AWS IoT Core and the core device\.  
This object contains the following information\.    
`type`  
\(Optional\) The type of strategy that this component uses to sync shadows between AWS IoT Core and the core device\. Choose from the following options:  
+ `realTime` – Sync shadows with AWS IoT Core each time a shadow update occurs\.
+ `periodic` – Sync shadows with AWS IoT Core on a regular interval that you specify with the `delay` configuration parameter\.
Default: `realTime`  
`delay`  
\(Optional\) The interval in seconds where this component syncs shadows with AWS IoT Core, when you specify the `periodic` sync strategy\.  
This parameter is required if you specify the `periodic` sync strategy\.

`synchronize`  <a name="shadow-manager-component-configuration-synchronize"></a>
<a name="shadow-manager-component-configuration-strategy-description"></a>\(Optional\) The synchronization settings that determine how shadows are synced with the AWS Cloud\.   
You must create a configuration update with this property to sync shadows with the AWS Cloud\.
This object contains the following information\.    
`coreThing`  <a name="shadow-manager-component-configuration-synchronize-core-thing"></a>
\(Optional\) The core device shadows to sync\. This object contains the following information\.    
`classic`  
\(Optional\) By default, the shadow manager syncs the local state of the classic shadow for your core device with the AWS Cloud\. If you don't want to sync the classic device shadow, set this to `false`\.  
Default: `true`  
`namedShadows`  
\(Optional\) The list of named core device shadows to sync\. You must specify the exact names of the shadows\.  
The AWS IoT Greengrass service uses the `AWSManagedGreengrassV2Deployment` named shadow to manage deployments that target individual core devices\. This named shadow is reserved for use by the AWS IoT Greengrass service\. Do not update or delete this named shadow\.  
`shadowDocuments`  <a name="shadow-manager-component-configuration-synchronize-shadow-documents"></a>
\(Optional\) The list of additional device shadows to sync\. Each object in this list contains the following information\.     
`thingName`  
The thing name of the device for which to sync shadows\.   
`classic`  
\(Optional\) If you don't want to sync the classic device shadow for the `thingName` device, set this to `false`\.  
Default: `true`  
`namedShadows`  
\(Optional\) The list of named device shadows that you want to sync\. You must specify the exact names of the shadows\.  
`direction`  
\(Optional\) The direction to sync shadows between the local shadow service and the AWS Cloud\. You can configure this option to reduce bandwidth and connections to the AWS Cloud\. Choose from the following options:  
+ `betweenDeviceAndCloud` – Synchronize shadows between the local shadow service and the AWS Cloud\.
+ `deviceToCloud` – Send shadow updates from the local shadow service to the AWS Cloud, and ignore shadow updates from the AWS Cloud\.
+ `cloudToDevice` – Receive shadow updates from the AWS Cloud, and don't send shadow updates from the local shadow service to the AWS Cloud\.
Default: `BETWEEN_DEVICE_AND_CLOUD`

`rateLimits`  <a name="shadow-manager-component-configuration-rate-limits"></a>
\(Optional\) The settings that determine the rate limits for shadow service requests\.  
This object contains the following information\.    
`maxOutboundSyncUpdatesPerSecond`  
\(Optional\) The maximum number of sync requests per second that the device transmits\.   
Default: 100 requests/second  
`maxTotalLocalRequestsRate`  
\(Optional\) The maximum number of local IPC requests per second that are sent to the core device\.   
Default: 200 requests/second  
`maxLocalRequestsPerSecondPerThing`  
\(Optional\) The maximum number of local IPC requests per second that are sent for each connected IoT thing\.   
Default: 20 requests/second for each thing
These rate limits parameters define the maximum number of requests per second for the local shadow service\. The maximum number of requests per second for the AWS IoT Device Shadow service depends on your AWS Region\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.

`shadowDocumentSizeLimitBytes`  <a name="shadow-manager-component-configuration-shadow-document-size-limit-bytes"></a>
\(Optional\) The maximum allowed size of each JSON state document for local shadows\.   
If you increase this value, you must also increase the resource limit for the JSON state document for cloud shadows\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.  
Default: 8192 bytes  
Maximum: 30720 bytes

**Example: Configuration merge update**  
The following example shows a sample configuration merge update with all available configuration parameters for the shadow manager component\.  

```
{
  "strategy": {
    "type": "periodic",
    "delay": 300
  },
  "synchronize": {
    "coreThing": {
      "classic": true,
      "namedShadows": [
        "MyCoreShadowA",
        "MyCoreShadowB"
      ]
    },
    "shadowDocuments": [
      {
        "thingName": "MyDevice1",
        "classic": false,
        "namedShadows": [
          "MyShadowA",
          "MyShadowB"
        ]
      },
      {
        "thingName": "MyDevice2",
        "classic": true,
        "namedShadows": []
      }
    ],
    "direction": "betweenDeviceAndCloud"
  },
  "rateLimits": {       
    "maxOutboundSyncUpdatesPerSecond": 100,
    "maxTotalLocalRequestsRate": 200,
    "maxLocalRequestsPerSecondPerThing": 20
  },
  "shadowDocumentSizeLimitBytes": 8192
}
```

------
#### [ 2\.1\.x ]

`strategy`  <a name="shadow-manager-component-configuration-strategy"></a>
\(Optional\) The strategy that this component uses to sync shadows between AWS IoT Core and the core device\.  
This object contains the following information\.    
`type`  
\(Optional\) The type of strategy that this component uses to sync shadows between AWS IoT Core and the core device\. Choose from the following options:  
+ `realTime` – Sync shadows with AWS IoT Core each time a shadow update occurs\.
+ `periodic` – Sync shadows with AWS IoT Core on a regular interval that you specify with the `delay` configuration parameter\.
Default: `realTime`  
`delay`  
\(Optional\) The interval in seconds where this component syncs shadows with AWS IoT Core, when you specify the `periodic` sync strategy\.  
This parameter is required if you specify the `periodic` sync strategy\.

`synchronize`  <a name="shadow-manager-component-configuration-synchronize"></a>
<a name="shadow-manager-component-configuration-strategy-description"></a>\(Optional\) The synchronization settings that determine how shadows are synced with the AWS Cloud\.   
You must create a configuration update with this property to sync shadows with the AWS Cloud\.
This object contains the following information\.    
`coreThing`  <a name="shadow-manager-component-configuration-synchronize-core-thing"></a>
\(Optional\) The core device shadows to sync\. This object contains the following information\.    
`classic`  
\(Optional\) By default, the shadow manager syncs the local state of the classic shadow for your core device with the AWS Cloud\. If you don't want to sync the classic device shadow, set this to `false`\.  
Default: `true`  
`namedShadows`  
\(Optional\) The list of named core device shadows to sync\. You must specify the exact names of the shadows\.  
The AWS IoT Greengrass service uses the `AWSManagedGreengrassV2Deployment` named shadow to manage deployments that target individual core devices\. This named shadow is reserved for use by the AWS IoT Greengrass service\. Do not update or delete this named shadow\.  
`shadowDocuments`  <a name="shadow-manager-component-configuration-synchronize-shadow-documents"></a>
\(Optional\) The list of additional device shadows to sync\. Each object in this list contains the following information\.     
`thingName`  
The thing name of the device for which to sync shadows\.   
`classic`  
\(Optional\) If you don't want to sync the classic device shadow for the `thingName` device, set this to `false`\.  
Default: `true`  
`namedShadows`  
\(Optional\) The list of named device shadows that you want to sync\. You must specify the exact names of the shadows\.

`rateLimits`  <a name="shadow-manager-component-configuration-rate-limits"></a>
\(Optional\) The settings that determine the rate limits for shadow service requests\.  
This object contains the following information\.    
`maxOutboundSyncUpdatesPerSecond`  
\(Optional\) The maximum number of sync requests per second that the device transmits\.   
Default: 100 requests/second  
`maxTotalLocalRequestsRate`  
\(Optional\) The maximum number of local IPC requests per second that are sent to the core device\.   
Default: 200 requests/second  
`maxLocalRequestsPerSecondPerThing`  
\(Optional\) The maximum number of local IPC requests per second that are sent for each connected IoT thing\.   
Default: 20 requests/second for each thing
These rate limits parameters define the maximum number of requests per second for the local shadow service\. The maximum number of requests per second for the AWS IoT Device Shadow service depends on your AWS Region\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.

`shadowDocumentSizeLimitBytes`  <a name="shadow-manager-component-configuration-shadow-document-size-limit-bytes"></a>
\(Optional\) The maximum allowed size of each JSON state document for local shadows\.   
If you increase this value, you must also increase the resource limit for the JSON state document for cloud shadows\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.  
Default: 8192 bytes  
Maximum: 30720 bytes

**Example: Configuration merge update**  
The following example shows a sample configuration merge update with all available configuration parameters for the shadow manager component\.  

```
{
  "strategy": {
    "type": "periodic",
    "delay": 300
  },
  "synchronize": {
    "coreThing": {
      "classic": true,
      "namedShadows": [
        "MyCoreShadowA",
        "MyCoreShadowB"
      ]
    },
    "shadowDocuments": [
      {
        "thingName": "MyDevice1",
        "classic": false,
        "namedShadows": [
          "MyShadowA",
          "MyShadowB"
        ]
      },
      {
        "thingName": "MyDevice2",
        "classic": true,
        "namedShadows": []
      }
    ]
  },
  "rateLimits": {       
    "maxOutboundSyncUpdatesPerSecond": 100,
    "maxTotalLocalRequestsRate": 200,
    "maxLocalRequestsPerSecondPerThing": 20
  },
  "shadowDocumentSizeLimitBytes": 8192
}
```

------
#### [ 2\.0\.x ]

`synchronize`  <a name="shadow-manager-component-configuration-synchronize"></a>
<a name="shadow-manager-component-configuration-strategy-description"></a>\(Optional\) The synchronization settings that determine how shadows are synced with the AWS Cloud\.   
You must create a configuration update with this property to sync shadows with the AWS Cloud\.
This object contains the following information\.    
`coreThing`  <a name="shadow-manager-component-configuration-synchronize-core-thing"></a>
\(Optional\) The core device shadows to sync\. This object contains the following information\.    
`classic`  
\(Optional\) By default, the shadow manager syncs the local state of the classic shadow for your core device with the AWS Cloud\. If you don't want to sync the classic device shadow, set this to `false`\.  
Default: `true`  
`namedShadows`  
\(Optional\) The list of named core device shadows to sync\. You must specify the exact names of the shadows\.  
The AWS IoT Greengrass service uses the `AWSManagedGreengrassV2Deployment` named shadow to manage deployments that target individual core devices\. This named shadow is reserved for use by the AWS IoT Greengrass service\. Do not update or delete this named shadow\.  
`shadowDocuments`  <a name="shadow-manager-component-configuration-synchronize-shadow-documents"></a>
\(Optional\) The list of additional device shadows to sync\. Each object in this list contains the following information\.     
`thingName`  
The thing name of the device for which to sync shadows\.   
`classic`  
\(Optional\) If you don't want to sync the classic device shadow for the `thingName` device, set this to `false`\.  
Default: `true`  
`namedShadows`  
\(Optional\) The list of named device shadows that you want to sync\. You must specify the exact names of the shadows\.

`rateLimits`  <a name="shadow-manager-component-configuration-rate-limits"></a>
\(Optional\) The settings that determine the rate limits for shadow service requests\.  
This object contains the following information\.    
`maxOutboundSyncUpdatesPerSecond`  
\(Optional\) The maximum number of sync requests per second that the device transmits\.   
Default: 100 requests/second  
`maxTotalLocalRequestsRate`  
\(Optional\) The maximum number of local IPC requests per second that are sent to the core device\.   
Default: 200 requests/second  
`maxLocalRequestsPerSecondPerThing`  
\(Optional\) The maximum number of local IPC requests per second that are sent for each connected IoT thing\.   
Default: 20 requests/second for each thing
These rate limits parameters define the maximum number of requests per second for the local shadow service\. The maximum number of requests per second for the AWS IoT Device Shadow service depends on your AWS Region\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.

`shadowDocumentSizeLimitBytes`  <a name="shadow-manager-component-configuration-shadow-document-size-limit-bytes"></a>
\(Optional\) The maximum allowed size of each JSON state document for local shadows\.   
If you increase this value, you must also increase the resource limit for the JSON state document for cloud shadows\. For more information, see the limits for the [AWS IoT Device Shadow Service API](https://docs.aws.amazon.com/general/latest/gr/iot-core.html#device-shadow-limits) in the *Amazon Web Services General Reference*\.  
Default: 8192 bytes  
Maximum: 30720 bytes

**Example: Configuration merge update**  
The following example shows a sample configuration merge update with all available configuration parameters for the shadow manager component\.  

```
{
  "synchronize": {
    "coreThing": {
      "classic": true,
      "namedShadows": [
        "MyCoreShadowA",
        "MyCoreShadowB"
      ]
    },
    "shadowDocuments": [
      {
        "thingName": "MyDevice1",
        "classic": false,
        "namedShadows": [
          "MyShadowA",
          "MyShadowB"
        ]
      },
      {
        "thingName": "MyDevice2",
        "classic": true,
        "namedShadows": []
      }
    ]
  },
  "rateLimits": {       
    "maxOutboundSyncUpdatesPerSecond": 100,
    "maxTotalLocalRequestsRate": 200,
    "maxLocalRequestsPerSecondPerThing": 20
  },
  "shadowDocumentSizeLimitBytes": 8192
}
```

------

## Local log file<a name="shadow-manager-component-log-file"></a>

This component uses the same log file as the [Greengrass nucleus](greengrass-nucleus-component.md) component\.

------
#### [ Linux ]

```
/greengrass/v2/logs/greengrass.log
```

------
#### [ Windows ]

```
C:\greengrass\v2\logs\greengrass.log
```

------

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux ]

  ```
  sudo tail -f /greengrass/v2/logs/greengrass.log
  ```

------
#### [ Windows \(PowerShell\) ]

  ```
  Get-Content C:\greengrass\v2\logs\greengrass.log -Tail 10 -Wait
  ```

------

## Changelog<a name="shadow-manager-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  Version  |  Changes  | 
| --- | --- | 
|  2\.3\.1  | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/shadow-manager-component.html) | 
|  2\.3\.0  | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/shadow-manager-component.html) | 
|  2\.2\.4  | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/shadow-manager-component.html) | 
|  2\.2\.3  | Version updated for Greengrass nucleus version 2\.9\.0 release\. | 
|  2\.2\.2  | Version updated for Greengrass nucleus version 2\.8\.0 release\. | 
|  2\.2\.1  |  Version updated for Greengrass nucleus version 2\.7\.0 release\.  | 
|  2\.2\.0  |  <a name="changelog-shadow-manager-2.2.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/shadow-manager-component.html)  | 
|  2\.1\.1  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/shadow-manager-component.html)  | 
|  2\.1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/shadow-manager-component.html)  | 
|  2\.0\.6  |  This version contains bug fixes and improvements\.  | 
|  2\.0\.5  |  Version updated for Greengrass nucleus version 2\.5\.0 release\.  | 
|  2\.0\.4  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/shadow-manager-component.html)  | 
|  2\.0\.3  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.2  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/shadow-manager-component.html)  | 
|  2\.0\.1  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.0  |  Initial version\.  | 