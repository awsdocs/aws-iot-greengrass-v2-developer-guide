# Interact with and sync client device shadows<a name="work-with-client-device-shadows"></a>

You can use the [shadow manager component](shadow-manager-component.md) to manage local shadows, including client device shadows\. You can use shadow manager to do the following:
+ Interact with client device shadows in Greengrass components\.
+ Sync client device shadows with AWS IoT Core\.

**Note**  
The shadow manager component doesn't sync shadows with AWS IoT Core by default\. You must configure the shadow manager component to specify which client device shadows to sync\.

**Topics**
+ [Prerequisites](#client-device-shadows-prerequisites)
+ [Enable shadow manager to communicate with client devices](#enable-shadow-manager-client-devices)
+ [Interact with client device shadows in components](#interact-with-client-device-shadows)
+ [Sync client device shadows with AWS IoT Core](#sync-client-device-shadows-with-iot-core)

## Prerequisites<a name="client-device-shadows-prerequisites"></a>

To interact with client device shadows and sync client device shadows with AWS IoT Core, a core device must meet the following requirements:
+ The core device must run the following components, in addition to the [Greengrass components for client device support](connect-client-devices.md#cloud-discovery-components):
  + [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later
  + [Shadow manager](shadow-manager-component.md) v2\.2\.0 or later
  + [MQTT bridge](mqtt-bridge-component.md) v2\.2\.0 or later
+ The [client device auth](client-device-auth-component.md) component must be configured to allow client devices to communicate on [device shadow topics](https://docs.aws.amazon.com/iot/latest/developerguide/reserved-topics.html#reserved-topics-shadow)\.

## Enable shadow manager to communicate with client devices<a name="enable-shadow-manager-client-devices"></a>

By default, the shadow manager component doesn't manage client device shadows\. To enable this feature, you must relay MQTT messages between client devices and the shadow manager component\. Client devices use MQTT messages to receive and send device shadow updates\. The shadow manager component subscribes to the local Greengrass publish/subscribe interface, so you can configure the [MQTT bridge component](mqtt-bridge-component.md) to relay MQTT messages on [device shadow topics](https://docs.aws.amazon.com/iot/latest/developerguide/reserved-topics.html#reserved-topics-shadow)\.

The MQTT bridge component consumes a list of topic mappings that each specify a message source and a message destination\. To enable the shadow manager component to manage client device shadows, deploy the MQTT bridge component, and specify the shadow topics for the client device shadows\. You must configure the bridge to relay messages in both directions between local MQTT and local publish/subscribe\.

<a name="create-mqtt-bridge-deployment-info"></a>To deploy the MQTT bridge component to a core device or group of core devices, [create a deployment](create-deployments.md) that includes the `aws.greengrass.clientdevices.mqtt.Bridge` component\. Specify the topic mappings, `mqttTopicMapping`, in the MQTT bridge component configuration in the deployment\.

Use the following examples to configure the MQTT bridge component to enable communication between client devices and the shadow manager component\.

**Note**  
You can use these configuration examples in the AWS IoT Greengrass console\. If you use the AWS IoT Greengrass API, the `merge` configuration update requires a serialized JSON object, so you must serialize the following JSON objects into strings\. For more information, see [Update component configurations](update-component-configurations.md)\.

**Example: Manage all client device shadows**  
The following MQTT bridge configuration example enables shadow manager to manage all shadows for all client devices\.  

```
{
  "mqttTopicMapping": {
    "ShadowsLocalMqttToPubsub": {
      "topic": "$aws/things/+/shadow/#",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "ShadowsPubsubToLocalMqtt": {
      "topic": "$aws/things/+/shadow/#",
      "source": "Pubsub",
      "target": "LocalMqtt"
    }
  }
}
```

**Example: Manage shadows for a client device**  
The following MQTT bridge configuration example enables shadow manager to manage all shadows for a client device named `MyClientDevice`\.  

```
{
  "mqttTopicMapping": {
    "ShadowsLocalMqttToPubsub": {
      "topic": "$aws/things/MyClientDevice/shadow/#",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "ShadowsPubsubToLocalMqtt": {
      "topic": "$aws/things/MyClientDevice/shadow/#",
      "source": "Pubsub",
      "target": "LocalMqtt"
    }
  }
}
```

**Example: Manage a named shadow for all client devices**  
The following MQTT bridge configuration example enables shadow manager to manage a shadow named `DeviceConfiguration` for all client devices\.  

```
{
  "mqttTopicMapping": {
    "ShadowsLocalMqttToPubsub": {
      "topic": "$aws/things/+/shadow/name/DeviceConfiguration/#",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "ShadowsPubsubToLocalMqtt": {
      "topic": "$aws/things/+/shadow/name/DeviceConfiguration/#",
      "source": "Pubsub",
      "target": "LocalMqtt"
    }
  }
}
```

**Example: Manage all client devices' unnamed shadows**  
The following MQTT bridge configuration example enables shadow manager to manage unnamed shadows, but not named shadows, for all client devices\.  

```
{
  "mqttTopicMapping": {
    "DeleteShadowLocalMqttToPubsub": {
      "topic": "$aws/things/+/shadow/delete",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "DeleteShadowPubsubToLocalMqtt": {
      "topic": "$aws/things/+/shadow/delete/#",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "GetShadowLocalMqttToPubsub": {
      "topic": "$aws/things/+/shadow/get",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "GetShadowPubsubToLocalMqtt": {
      "topic": "$aws/things/+/shadow/get/#",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "UpdateShadowLocalMqttToPubsub": {
      "topic": "$aws/things/+/shadow/update",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "UpdateShadowPubsubToLocalMqtt": {
      "topic": "$aws/things/+/shadow/update/#",
      "source": "Pubsub",
      "target": "LocalMqtt"
    }
  }
}
```

## Interact with client device shadows in components<a name="interact-with-client-device-shadows"></a>

You can develop custom components that use the local shadow service to read and modify client devices' local shadow documents\. For more information, see [Interact with shadows in components](interact-with-shadows-in-components.md)\.

## Sync client device shadows with AWS IoT Core<a name="sync-client-device-shadows-with-iot-core"></a>

You can configure the shadow manager component to synchronize local client device shadow states with AWS IoT Core\. For more information, see [Sync local device shadows with AWS IoT Core](sync-shadows-with-iot-core.md)\.