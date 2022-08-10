# MQTT bridge<a name="mqtt-bridge-component"></a>

The MQTT bridge component \(`aws.greengrass.clientdevices.mqtt.Bridge`\) relays MQTT messages between client devices, local Greengrass publish/subscribe, and AWS IoT Core\. You can use this component to act on MQTT messages from client devices in custom components and sync client devices with the AWS Cloud\.

**Note**  <a name="client-device-component-context"></a>
Client devices are local IoT devices that connect to a Greengrass core device to send MQTT messages and data to process\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

You can use this component to relay messages between the following message brokers:
+ Local MQTT – The local MQTT broker handles messages between client devices and a core device\.
+ Local publish/subscribe – The local Greengrass message broker handles messages between components on a core device\. For more information about how to interact with these messages in Greengrass components, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.
+ AWS IoT Core – The AWS IoT Core MQTT broker handles messages between IoT devices and AWS Cloud destinations\. For more information about how to interact with these messages in Greengrass components, see [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.
**Note**  <a name="mqtt-bridge-component-iotcore-qos-1-note"></a>
The MQTT bridge uses QoS 1 to publish and subscribe to AWS IoT Core, even when a client device uses QoS 0 to publish and subscribe to the local MQTT broker\. As a result, you might observe additional latency when you relay MQTT messages from client devices on the local MQTT broker to AWS IoT Core\. For more information about MQTT configuration on core devices, see [Configure MQTT timeouts and cache settings](configure-greengrass-core-v2.md#configure-mqtt)\.

**Topics**
+ [Versions](#mqtt-bridge-component-versions)
+ [Type](#mqtt-bridge-component-type)
+ [Operating system](#mqtt-bridge-component-os-support)
+ [Requirements](#mqtt-bridge-component-requirements)
+ [Dependencies](#mqtt-bridge-component-dependencies)
+ [Configuration](#mqtt-bridge-component-configuration)
+ [Local log file](#mqtt-bridge-component-log-file)
+ [Changelog](#mqtt-bridge-component-changelog)

## Versions<a name="mqtt-bridge-component-versions"></a>

This component has the following versions:
+ 2\.2\.x
+ 2\.1\.x
+ 2\.0\.x

## Type<a name="mqtt-bridge-component-type"></a>

<a name="public-component-type-plugin-para1"></a>This component is a plugin component \(`aws.greengrass.plugin`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component in the same Java Virtual Machine \(JVM\) as the nucleus\. The nucleus restarts when you change this component's version on the core device\.

<a name="public-component-type-plugin-para2"></a>This component uses the same log file as the Greengrass nucleus\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="mqtt-bridge-component-os-support"></a>

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

## Requirements<a name="mqtt-bridge-component-requirements"></a>

This component has the following requirements:
+ If you configure the core device's MQTT broker component to use a port other than the default port 8883, you must use MQTT bridge v2\.1\.0 or later\. Configure it to connect on the port where the broker operates\.

## Dependencies<a name="mqtt-bridge-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#mqtt-bridge-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.2\.0 ]

The following table lists the dependencies for version 2\.2\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Client device auth](client-device-auth-component.md) | >=2\.2\.0 <2\.3\.0 | Hard | 

------
#### [ 2\.1\.1 ]

The following table lists the dependencies for version of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Client device auth](client-device-auth-component.md) | >=2\.0\.0 <2\.2\.0 | Hard | 

------
#### [ 2\.0\.0 to 2\.1\.0 ]

The following table lists the dependencies for versions 2\.0\.0 through 2\.1\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Client device auth](client-device-auth-component.md) | >=2\.0\.0 <2\.1\.0 | Hard | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="mqtt-bridge-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ 2\.2\.x ]

`mqttTopicMapping`  
<a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-description"></a>The topic mappings that you want to bridge\. This component subscribes to messages on the source topic and publishes the messages that it receives to the destination topic\. Each topic mapping defines the topic, source type, and destination type\.  
This object contains the following information:    
`topicMappingNameKey`  
<a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-name-key-description"></a>The name of this topic mapping\. Replace *topicMappingNameKey* with a name that helps you identify this topic mapping\.  
This object contains the following information:    
`topic`  
The topic or topic filter to bridge between the source and target brokers\.  
You can use the `+` and `#` MQTT topic wildcards to relay messages on all topics that match a topic filter\. For more information, see [MQTT topics](https://docs.aws.amazon.com/iot/latest/developerguide/topics.html) in the *AWS IoT Core Developer Guide*\.  
To use MQTT topic wildcards with the `Pubsub` source broker, you must use v2\.6\.0 or later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.  
`targetTopicPrefix`  
The prefix to add to the target topic when this component relays the message\.  
`source`  <a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-source"></a>
The source message broker\. Choose from the following options:  <a name="mqtt-bridge-component-configuration-topic-types"></a>
+ `LocalMqtt` – The local MQTT broker where client devices communicate\.
+ `Pubsub` – The local Greengrass publish/subscribe message broker\.
+ `IotCore` – The AWS IoT Core MQTT message broker\.
**Note**  <a name="mqtt-bridge-component-iotcore-qos-1-note"></a>
The MQTT bridge uses QoS 1 to publish and subscribe to AWS IoT Core, even when a client device uses QoS 0 to publish and subscribe to the local MQTT broker\. As a result, you might observe additional latency when you relay MQTT messages from client devices on the local MQTT broker to AWS IoT Core\. For more information about MQTT configuration on core devices, see [Configure MQTT timeouts and cache settings](configure-greengrass-core-v2.md#configure-mqtt)\.
`source` and `target` must be different\.  
`target`  <a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-target"></a>
The target message broker\. Choose from the following options:  <a name="mqtt-bridge-component-configuration-topic-types"></a>
+ `LocalMqtt` – The local MQTT broker where client devices communicate\.
+ `Pubsub` – The local Greengrass publish/subscribe message broker\.
+ `IotCore` – The AWS IoT Core MQTT message broker\.
**Note**  <a name="mqtt-bridge-component-iotcore-qos-1-note"></a>
The MQTT bridge uses QoS 1 to publish and subscribe to AWS IoT Core, even when a client device uses QoS 0 to publish and subscribe to the local MQTT broker\. As a result, you might observe additional latency when you relay MQTT messages from client devices on the local MQTT broker to AWS IoT Core\. For more information about MQTT configuration on core devices, see [Configure MQTT timeouts and cache settings](configure-greengrass-core-v2.md#configure-mqtt)\.
`source` and `target` must be different\.

`brokerUri`  <a name="mqtt-bridge-component-configuration-broker-uri"></a>
\(Optional\) The URI of the local MQTT broker\. You must specify this parameter if you configure the MQTT broker to use a different port than the default port 8883\. Use the following format, and replace *port* with the port where the MQTT broker operates: `ssl://localhost:port`\.  
Default: `ssl://localhost:8883`

**Example: Configuration merge update**  <a name="mqtt-bridge-component-configuration-example-with-prefix"></a>
The following example configuration update specifies the following:  
+ Relay messages from client devices to AWS IoT Core on topics that match the `clients/+/hello/world` topic filter\.
+ Relay messages from client devices to local publish/subscribe on topics that match the `clients/+/detections` topic filter, and add the `events/input/` prefix to the target topic\. The resulting target topic matches the `events/input/clients/+/detections` topic filter\.
+ Relay messages from client devices to AWS IoT Core on topics that match the `clients/+/status` topic filter, and add the `$aws/rules/StatusUpdateRule/` prefix to the target topic\. This example relays these messages directly to an [AWS IoT rule](https://docs.aws.amazon.com/iot/latest/developerguide/iot-rules.html) named `StatusUpdateRule` to reduce costs using [Basic Ingest](https://docs.aws.amazon.com/iot/latest/developerguide/iot-basic-ingest.html)\.

```
{
  "mqttTopicMapping": {
    "ClientDeviceHelloWorld": {
      "topic": "clients/+/hello/world",
      "source": "LocalMqtt",
      "target": "IotCore"
    },
    "ClientDeviceEvents": {
      "topic": "clients/+/detections",
      "targetTopicPrefix": "events/input/",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "ClientDeviceCloudStatusUpdate": {
      "topic": "clients/+/status",
      "targetTopicPrefix": "$aws/rules/StatusUpdateRule/"
      "source": "LocalMqtt",
      "target": "IotCore"
    }
  }
}
```

------
#### [ 2\.1\.x ]

`mqttTopicMapping`  <a name="mqtt-bridge-component-configuration-mqtt-topic-mapping"></a>
<a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-description"></a>The topic mappings that you want to bridge\. This component subscribes to messages on the source topic and publishes the messages that it receives to the destination topic\. Each topic mapping defines the topic, source type, and destination type\.  
This object contains the following information:    
`topicMappingNameKey`  
<a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-name-key-description"></a>The name of this topic mapping\. Replace *topicMappingNameKey* with a name that helps you identify this topic mapping\.  
This object contains the following information:    
`topic`  
The topic or topic filter to bridge between the source and target brokers\.  
If you specify the `LocalMqtt` or `IotCore` source broker, you can use the `+` and `#` MQTT topic wildcards to relay messages on all topics that match a topic filter\. For more information, see [MQTT topics](https://docs.aws.amazon.com/iot/latest/developerguide/topics.html) in the *AWS IoT Core Developer Guide*\.  
`source`  <a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-source"></a>
The source message broker\. Choose from the following options:  <a name="mqtt-bridge-component-configuration-topic-types"></a>
+ `LocalMqtt` – The local MQTT broker where client devices communicate\.
+ `Pubsub` – The local Greengrass publish/subscribe message broker\.
+ `IotCore` – The AWS IoT Core MQTT message broker\.
**Note**  <a name="mqtt-bridge-component-iotcore-qos-1-note"></a>
The MQTT bridge uses QoS 1 to publish and subscribe to AWS IoT Core, even when a client device uses QoS 0 to publish and subscribe to the local MQTT broker\. As a result, you might observe additional latency when you relay MQTT messages from client devices on the local MQTT broker to AWS IoT Core\. For more information about MQTT configuration on core devices, see [Configure MQTT timeouts and cache settings](configure-greengrass-core-v2.md#configure-mqtt)\.
`source` and `target` must be different\.  
`target`  <a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-target"></a>
The target message broker\. Choose from the following options:  <a name="mqtt-bridge-component-configuration-topic-types"></a>
+ `LocalMqtt` – The local MQTT broker where client devices communicate\.
+ `Pubsub` – The local Greengrass publish/subscribe message broker\.
+ `IotCore` – The AWS IoT Core MQTT message broker\.
**Note**  <a name="mqtt-bridge-component-iotcore-qos-1-note"></a>
The MQTT bridge uses QoS 1 to publish and subscribe to AWS IoT Core, even when a client device uses QoS 0 to publish and subscribe to the local MQTT broker\. As a result, you might observe additional latency when you relay MQTT messages from client devices on the local MQTT broker to AWS IoT Core\. For more information about MQTT configuration on core devices, see [Configure MQTT timeouts and cache settings](configure-greengrass-core-v2.md#configure-mqtt)\.
`source` and `target` must be different\.

`brokerUri`  <a name="mqtt-bridge-component-configuration-broker-uri"></a>
\(Optional\) The URI of the local MQTT broker\. You must specify this parameter if you configure the MQTT broker to use a different port than the default port 8883\. Use the following format, and replace *port* with the port where the MQTT broker operates: `ssl://localhost:port`\.  
Default: `ssl://localhost:8883`

**Example: Configuration merge update**  <a name="mqtt-bridge-component-configuration-example-no-prefix"></a>
The following example configuration update specifies to relay messages from client devices to AWS IoT Core on the `clients/MyClientDevice1/hello/world` and `clients/MyClientDevice2/hello/world` topics\.  

```
{
  "mqttTopicMapping": {
    "ClientDevice1HelloWorld": {
      "topic": "clients/MyClientDevice1/hello/world",
      "source": "LocalMqtt",
      "target": "IotCore"
    },
    "ClientDevice2HelloWorld": {
      "topic": "clients/MyClientDevice2/hello/world",
      "source": "LocalMqtt",
      "target": "IotCore"
    }
  }
}
```

------
#### [ 2\.0\.x ]

`mqttTopicMapping`  <a name="mqtt-bridge-component-configuration-mqtt-topic-mapping"></a>
<a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-description"></a>The topic mappings that you want to bridge\. This component subscribes to messages on the source topic and publishes the messages that it receives to the destination topic\. Each topic mapping defines the topic, source type, and destination type\.  
This object contains the following information:    
`topicMappingNameKey`  
<a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-name-key-description"></a>The name of this topic mapping\. Replace *topicMappingNameKey* with a name that helps you identify this topic mapping\.  
This object contains the following information:    
`topic`  
The topic or topic filter to bridge between the source and target brokers\.  
If you specify the `LocalMqtt` or `IotCore` source broker, you can use the `+` and `#` MQTT topic wildcards to relay messages on all topics that match a topic filter\. For more information, see [MQTT topics](https://docs.aws.amazon.com/iot/latest/developerguide/topics.html) in the *AWS IoT Core Developer Guide*\.  
`source`  <a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-source"></a>
The source message broker\. Choose from the following options:  <a name="mqtt-bridge-component-configuration-topic-types"></a>
+ `LocalMqtt` – The local MQTT broker where client devices communicate\.
+ `Pubsub` – The local Greengrass publish/subscribe message broker\.
+ `IotCore` – The AWS IoT Core MQTT message broker\.
**Note**  <a name="mqtt-bridge-component-iotcore-qos-1-note"></a>
The MQTT bridge uses QoS 1 to publish and subscribe to AWS IoT Core, even when a client device uses QoS 0 to publish and subscribe to the local MQTT broker\. As a result, you might observe additional latency when you relay MQTT messages from client devices on the local MQTT broker to AWS IoT Core\. For more information about MQTT configuration on core devices, see [Configure MQTT timeouts and cache settings](configure-greengrass-core-v2.md#configure-mqtt)\.
`source` and `target` must be different\.  
`target`  <a name="mqtt-bridge-component-configuration-mqtt-topic-mapping-target"></a>
The target message broker\. Choose from the following options:  <a name="mqtt-bridge-component-configuration-topic-types"></a>
+ `LocalMqtt` – The local MQTT broker where client devices communicate\.
+ `Pubsub` – The local Greengrass publish/subscribe message broker\.
+ `IotCore` – The AWS IoT Core MQTT message broker\.
**Note**  <a name="mqtt-bridge-component-iotcore-qos-1-note"></a>
The MQTT bridge uses QoS 1 to publish and subscribe to AWS IoT Core, even when a client device uses QoS 0 to publish and subscribe to the local MQTT broker\. As a result, you might observe additional latency when you relay MQTT messages from client devices on the local MQTT broker to AWS IoT Core\. For more information about MQTT configuration on core devices, see [Configure MQTT timeouts and cache settings](configure-greengrass-core-v2.md#configure-mqtt)\.
`source` and `target` must be different\.

**Example: Configuration merge update**  <a name="mqtt-bridge-component-configuration-example-no-prefix"></a>
The following example configuration update specifies to relay messages from client devices to AWS IoT Core on the `clients/MyClientDevice1/hello/world` and `clients/MyClientDevice2/hello/world` topics\.  

```
{
  "mqttTopicMapping": {
    "ClientDevice1HelloWorld": {
      "topic": "clients/MyClientDevice1/hello/world",
      "source": "LocalMqtt",
      "target": "IotCore"
    },
    "ClientDevice2HelloWorld": {
      "topic": "clients/MyClientDevice2/hello/world",
      "source": "LocalMqtt",
      "target": "IotCore"
    }
  }
}
```

------

## Local log file<a name="mqtt-bridge-component-log-file"></a>

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

## Changelog<a name="mqtt-bridge-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.2\.0  |  <a name="changelog-mqtt-bridge-2.2.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/mqtt-bridge-component.html)  | 
|  2\.1\.1  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/mqtt-bridge-component.html)  | 
|  2\.1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/mqtt-bridge-component.html)  | 
|  2\.0\.1  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.0  |  Initial version\.  | 
