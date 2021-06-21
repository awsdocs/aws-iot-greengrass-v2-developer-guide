# Sync and interact with client device shadows<a name="work-with-client-device-shadows"></a>

You can use the [shadow manager component](shadow-manager-component.md) to manage local shadows, including client device shadows\. You can deploy and configure the shadow manager component to do the following:
+ Sync client device shadows with AWS IoT Core\. For more information, see [Sync local device shadows with AWS IoT Core](sync-shadows-with-iot-core.md)\.
+ Interact with client device shadows in Greengrass components\. For more information, see [Interact with shadows in components](interact-with-shadows-in-components.md)\.

**Note**  
The shadow manager component doesn't sync shadows with AWS IoT Core by default\. You must configure the shadow manager component to specify which client device shadows to sync\.

By default, the shadow manager component doesn't manage client device shadows\. To enable this feature, you must relay MQTT messages from client devices to the shadow manager component\. Client devices use MQTT messages to receive and send device shadow updates\. The shadow manager component subscribes to the local Greengrass publish/subscribe interface, so you can configure the [MQTT bridge component](mqtt-bridge-component.md) to relay MQTT messages on device shadow topics\. For more information, see [Shadow topics](https://docs.aws.amazon.com/iot/latest/developerguide/reserved-topics.html#reserved-topics-shadow) in the *AWS IoT Core Developer Guide*\.

## Configure and deploy the MQTT bridge component<a name="deploy-mqtt-bridge-shadows"></a>

The MQTT bridge component consumes a list of topic mappings that each specify a message source and a message destination\. To enable the shadow manager component to manage client device shadows, deploy the MQTT bridge component, and specify the shadow topics for the client device shadows to manage\. You must configure the bridge to relay messages in both directions between local MQTT and local publish/subscribe\.

<a name="create-mqtt-bridge-deployment-info"></a>To deploy the MQTT bridge component to a core device or group of core devices, [create a deployment](create-deployments.md) that includes the `aws.greengrass.clientdevices.mqtt.Bridge` component\. Specify the topic mappings, `mqttTopicMapping` in the MQTT bridge component configuration in the deployment\.

**Note**  
You can use these configuration examples in the AWS IoT Greengrass console\. If you use the AWS IoT Greengrass API, the `merge` configuration update requires a serialized JSON object, so you must serialize the following JSON objects into strings\. For more information, see [Update component configurations](update-component-configurations.md)\.

### MQTT bridge configuration to manage unnamed shadows<a name="mqtt-bridge-component-configuration-sync-shadows"></a>

To use shadow manager to manage unnamed shadows for a client device, specify each of the following topics for each client device that you want to sync\. Replace *thingName* in the following MQTT bridge component configuration with the name of the client device to manage\.

```
{
  "mqttTopicMapping": {
    "DeleteShadow": {
      "topic": "$aws/things/thingName/shadow/delete",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "DeleteShadowAccepted": {
      "topic": "$aws/things/thingName/shadow/delete/accepted",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "DeleteShadowRejected": {
      "topic": "$aws/things/thingName/shadow/delete/rejected",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "GetShadow": {
      "topic": "$aws/things/thingName/shadow/get",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "GetShadowAccepted": {
      "topic": "$aws/things/thingName/shadow/get/accepted",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "GetShadowRejected": {
      "topic": "$aws/things/thingName/shadow/get/rejected",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "UpdateShadow": {
      "topic": "$aws/things/thingName/shadow/update",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "UpdateShadowAccepted": {
      "topic": "$aws/things/thingName/shadow/update/accepted",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "UpdateShadowRejected": {
      "topic": "$aws/things/thingName/shadow/update/rejected",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "UpdateShadowResponseDelta": {
      "topic": "$aws/things/thingName/shadow/update/delta",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "UpdateShadowResponseDocuments": {
      "topic": "$aws/things/thingName/shadow/update/documents",
      "source": "Pubsub",
      "target": "LocalMqtt"
    }
  }
}
```

### MQTT bridge configuration to manage named shadows<a name="mqtt-bridge-component-configuration-sync-named-shadows"></a>

To use shadow manager to manage named shadows for a client device, specify each of the following topics for each named shadow that you want to sync\. Replace *thingName* and *shadowName* in the following MQTT bridge component configuration with the name of client device and shadow to manage\.

```
{
  "mqttTopicMapping": {
    "DeleteNamedShadow": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/delete",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "DeleteNamedShadowAccepted": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/delete/accepted",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "DeleteNamedShadowRejected": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/delete/rejected",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "GetNamedShadow": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/get",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "GetNamedShadowAccepted": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/get/accepted",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "GetNamedShadowRejected": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/get/rejected",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "UpdateNamedShadow": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/update",
      "source": "LocalMqtt",
      "target": "Pubsub"
    },
    "UpdateNamedShadowAccepted": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/update/accepted",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "UpdateNamedShadowRejected": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/update/rejected",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "UpdateNamedShadowResponseDelta": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/update/delta",
      "source": "Pubsub",
      "target": "LocalMqtt"
    },
    "UpdateNamedShadowResponseDocuments": {
      "topic": "$aws/things/thingName/shadow/name/shadowName/update/documents",
      "source": "Pubsub",
      "target": "LocalMqtt"
    }
  }
}
```