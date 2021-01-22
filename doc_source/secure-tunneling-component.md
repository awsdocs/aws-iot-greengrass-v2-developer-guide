# Secure tunneling<a name="secure-tunneling-component"></a>

The secure tunneling component \(`aws.greengrass.SecureTunneling`\) enables you to use AWS IoT secure tunneling to establish secure bidirectional communication with a Greengrass core device that is behind restricted firewalls\. 

For example, a Greengrass core device is behind a firewall that prohibits all incoming connections, which blocks SSH connections to the device\. Secure tunneling uses MQTT over WebSockets to make these connections so that you can open an SSH connection to the device by using a tunnel that is managed by AWS IoT\. For more information about using AWS IoT secure tunneling to connect to remote devices, see [AWS IoT secure tunneling](https://docs.aws.amazon.com/iot/latest/developerguide/secure-tunneling.html) in the *AWS IoT Developer Guide*\.

By default, this component subscribes to the `things/greengrass-core-device/tunnels/notify` MQTT topic for secure tunneling notifications for the IoT thing that corresponds to your Greengrass core device\. 



This component has the following versions:
+ 1\.0\.x

## Requirements<a name="secure-tunneling-component-requirements"></a>

This component has the following requirements:
+ [Python](https://www.python.org/) 3\.5 or later installed on the Greengrass core device and added to the PATH environment variable\.
+ `libcrypto.so.1.1` installed on the Greengrass core device and added to the PATH environment variable\.
+ The Greengrass core device must allow outbound traffic on port 443\. 
+ The Greengrass core device must have enabled the service that you want to use to communicate with the device\. For example, to open an SSH connection to the device, the device must have SSH enabled\. 

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