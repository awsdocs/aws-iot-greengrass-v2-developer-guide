# Secure tunneling<a name="secure-tunneling-component"></a>

The secure tunneling component \(`aws.greengrass.SecureTunneling`\) enables you to use AWS IoT secure tunneling to establish secure bidirectional communication with a Greengrass core device that is behind restricted firewalls\. 

For example, a Greengrass core device is behind a firewall that prohibits all incoming connections, which blocks SSH connections to the device\. Secure tunneling uses MQTT over WebSockets to make these connections so that you can open an SSH connection to the device by using a tunnel that is managed by AWS IoT\. For more information about using AWS IoT secure tunneling to connect to remote devices, see [AWS IoT secure tunneling](https://docs.aws.amazon.com/iot/latest/developerguide/secure-tunneling.html) in the *AWS IoT Developer Guide*\.

By default, this component subscribes to the `things/greengrass-core-device/tunnels/notify` MQTT topic for secure tunneling notifications for the IoT thing that corresponds to your Greengrass core device\. 



**Topics**
+ [Versions](#secure-tunneling-component-versions)
+ [Type](#secure-tunneling-component-type)
+ [Requirements](#secure-tunneling-component-requirements)
+ [Dependencies](#secure-tunneling-component-dependencies)
+ [Configuration](#secure-tunneling-component-configuration)
+ [Licenses](#secure-tunneling-component-licenses)
+ [See also](#secure-tunneling-component-see-also)
+ [Changelog](#secure-tunneling-component-changelog)

## Versions<a name="secure-tunneling-component-versions"></a>

This component has the following versions:
+ 1\.0\.x

## Type<a name="secure-tunneling-component-type"></a>

<a name="public-component-type-generic"></a>This component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](manage-components.md#component-types)\.

## Requirements<a name="secure-tunneling-component-requirements"></a>

To deploy a component, you must meet the requirements for the component and its [dependencies](#secure-tunneling-component-dependencies)\. This component has the following requirements:
+ [Python](https://www.python.org/) 3\.5 or later installed on the Greengrass core device and added to the PATH environment variable\.
+ `libcrypto.so.1.1` installed on the Greengrass core device and added to the PATH environment variable\.
+ The Greengrass core device must allow outbound traffic on port 443\. 
+ The Greengrass core device must have enabled the service that you want to use to communicate with the device\. For example, to open an SSH connection to the device, the device must have SSH enabled\. 

## Dependencies<a name="secure-tunneling-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. You must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#secure-tunneling-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ >=1\.0\.1 ]

The following table lists the dependencies for version 2\.1\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.0 <2\.2\.0  | Soft | 

------
#### [ 1\.0\.x ]

The following table lists the dependencies for version 1\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.3 <2\.1\.0  | Soft | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="secure-tunneling-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`OS_DIST_INFO`  
\(Optional\) The operating system of your core device\. By default, the component attempts to automatically identify the operating system running on your core device\. If the component fails to start with the default value, then use this value to specify the operating system\. For a list of supported operating systems for this component, see [Requirements](setting-up.md#greengrass-v2-requirements)\.  
This value can be one of the following: `auto`, `ubuntu`, `amzn2`, `raspberrypi`\.  
Default: `auto`

`accessControl`  
The object that contains the [authorization policy](interprocess-communication.md#ipc-authorization-policies) that allows the component to subscribe to the secure tunneling notifications topic\.   
Do not modify this configuration parameter if your deployment targets a thing group\. If your deployment targets an individual core device, and you want to restrict this component's subscription to the topic for that device, then in the `resources` value in the authorization policy, replace the MQTT topic wildcard \(`+`\) with the thing name for that core device\. 
Default:   

```
aws.greengrass.ipc.mqttproxy:
  aws.iot.SecureTunneling:mqttproxy:1:
    policyDescription: "Access to tunnel notification pubsub topic"
      operations:
        - "aws.greengrass#SubscribeToIoTCore"
      resources:
        - "$aws/things/+/tunnels/notify"
```

## Licenses<a name="secure-tunneling-component-licenses"></a>

This component includes the following third\-party software/licensing:
+ [AWS IoT Device Client](https://github.com/awslabs/aws-iot-device-client)/Apache License 2/0
+ [AWS IoT Device SDK for Java](https://github.com/aws/aws-greengrass-core-sdk-java/)//Apache License 2\.0
+ [gson](https://github.com/google/gson)/Apache License 2\.0
+ [log4j](https://logging.apache.org/log4j/2.x/)/Apache License 2\.0
+ [slf4j](http://www.slf4j.org/)/Apache License 2\.0

## See also<a name="secure-tunneling-component-see-also"></a>
+ [AWS IoT secure tunneling](https://docs.aws.amazon.com/iot/latest/developerguide/secure-tunneling.html) in the *AWS IoT Developer Guide*\.

## Changelog<a name="secure-tunneling-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  1\.0\.1  |  Version updated for Greengrass nucleus version 2\.1\.0 release\.  | 
|  1\.0\.0  |  Initial version\.  | 