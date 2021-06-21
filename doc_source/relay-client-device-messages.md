# Relay MQTT messages between client devices and AWS IoT Core<a name="relay-client-device-messages"></a>

You can relay MQTT messages and other data between client devices and AWS IoT Core\. Client devices connect to the MQTT broker component that runs on the core device\. By default, core devices don't relay MQTT messages or data between client devices and AWS IoT Core\. Client devices can communicate only with each other over MQTT by default\.

To relay MQTT messages between client devices and AWS IoT Core, configure the [MQTT bridge component](mqtt-bridge-component.md) to do the following:
+ Relay messages from client devices to AWS IoT Core\.
+ Relay messages from AWS IoT Core to client devices\.

**Note**  <a name="mqtt-bridge-component-iotcore-qos-1-note"></a>
The MQTT bridge uses QoS 1 to publish and subscribe to AWS IoT Core, even when a client device uses QoS 0 to publish and subscribe to the local MQTT broker\. As a result, you might observe additional latency when you relay MQTT messages from client devices on the local MQTT broker to AWS IoT Core\. For more information about MQTT configuration on core devices, see [Configure MQTT timeouts and cache settings](configure-greengrass-core-v2.md#configure-mqtt)\.

**Topics**
+ [Configure and deploy the MQTT bridge component](#deploy-mqtt-bridge-iot-core)
+ [Relay MQTT messages](#relay-mqtt-messages)

## Configure and deploy the MQTT bridge component<a name="deploy-mqtt-bridge-iot-core"></a>

The MQTT bridge component consumes a list of topic mappings that each specify a message source and a message destination\. To relay messages between client devices and AWS IoT Core, deploy the MQTT bridge component, and specify each source and destination topic in the component configuration\.

<a name="create-mqtt-bridge-deployment-info"></a>To deploy the MQTT bridge component to a core device or group of core devices, [create a deployment](create-deployments.md) that includes the `aws.greengrass.clientdevices.mqtt.Bridge` component\. Specify the topic mappings, `mqttTopicMapping` in the MQTT bridge component configuration in the deployment\.

The following example defines a deployment that configures the MQTT bridge component to relay messages on topics that match the `clients/+/hello/world` topic filter from client devices to AWS IoT Core\. The `merge` configuration update requires a serialized JSON object\. For more information, see [Update component configurations](update-component-configurations.md)\.

------
#### [ Console ]

```
{
  "mqttTopicMapping": {
    "HelloWorldIotCore": {
      "topic": "clients/+/hello/world",
      "source": "LocalMqtt",
      "target": "IotCore"
    }
  }
}
```

------
#### [ AWS CLI ]

```
{
  "components": {
    "aws.greengrass.clientdevices.mqtt.Bridge": {
      "version": "2.0.0",
      "configurationUpdate": {
        "merge": "{\"mqttTopicMapping\":{\"HelloWorldIotCore\":{\"topic"\:\"clients/+/hello/world\",\"source\":\"LocalMqtt\",\"target\":\"IotCore\"}}}"
      }
    }
    ...
  }
}
```

------

## Relay MQTT messages<a name="relay-mqtt-messages"></a>

To relay MQTT messages between client devices and AWS IoT Core, [configure and deploy the MQTT Bridge component](#deploy-mqtt-bridge-iot-core) and specify the topics to relay\.

**Example: Relay messages on a topic from client devices to AWS IoT Core**  
The following MQTT bridge component configuration specifies relaying messages on topics that match the `clients/+/hello/world/event` topic filter from client devices to AWS IoT Core\.  

```
{
  "mqttTopicMapping": {
    "HelloWorldEvent": {
      "topic": "clients/+/hello/world/event",
      "source": "LocalMqtt",
      "target": "IotCore"
    }
  }
}
```

**Example: Relay messages on a topic from AWS IoT Core to client devices**  
The following MQTT bridge component configuration specifies relaying messages on topics that match the `clients/+/hello/world/event/response` topic filter from AWS IoT Core to client devices\.  

```
{
  "mqttTopicMapping": {
    "HelloWorldEventConfirmation": {
      "topic": "clients/+/hello/world/event/response",
      "source": "IotCore",
      "target": "LocalMqtt"
    }
  }
}
```