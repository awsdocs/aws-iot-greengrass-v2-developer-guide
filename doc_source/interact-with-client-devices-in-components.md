# Interact with client devices in components<a name="interact-with-client-devices-in-components"></a>

You can develop custom Greengrass components that interact with client devices connected to a core device\. For example, you can develop components that do the following:
+ Act on MQTT messages from client devices and send data to AWS Cloud destinations\.
+ Send MQTT messages to client devices to initiate actions\.

Client devices connect to and communicate with a core device through the MQTT broker component that runs on the core device\. By default, client devices can communicate only with each other over MQTT, and Greengrass components can't receive these MQTT messages or send messages to client devices\.

Greengrass components use the [local publish/subscribe interface](ipc-publish-subscribe.md) to communicate on a core device\. To communicate with client devices in Greengrass components, configure the [MQTT bridge component](mqtt-bridge-component.md) to do the following:
+ Relay MQTT messages from client devices to local publish/subscribe\.
+ Relay MQTT messages from local publish/subscribe to client devices\.

You can also interact with client device shadows in Greengrass components\. For more information, see [Sync and interact with client device shadows](work-with-client-device-shadows.md)\.

**Topics**
+ [Configure and deploy the MQTT bridge component](#deploy-mqtt-bridge-pubsub)
+ [Receive MQTT messages from client devices](#receive-client-device-messages)
+ [Send MQTT messages to client devices](#send-client-device-messages)

## Configure and deploy the MQTT bridge component<a name="deploy-mqtt-bridge-pubsub"></a>

The MQTT bridge component consumes a list of topic mappings that each specify a message source and a message destination\. To communicate with client devices, deploy the MQTT bridge component, and specify each source and destination topic in the component configuration\.

<a name="create-mqtt-bridge-deployment-info"></a>To deploy the MQTT bridge component to a core device or group of core devices, [create a deployment](create-deployments.md) that includes the `aws.greengrass.clientdevices.mqtt.Bridge` component\. Specify the topic mappings, `mqttTopicMapping` in the MQTT bridge component configuration in the deployment\.

The following example defines a deployment that configures the MQTT bridge component to relay the `clients/MyClientDevice1/hello/world` topic from client devices to local publish/subscribe broker\. The `merge` configuration update requires a serialized JSON object\. For more information, see [Update component configurations](update-component-configurations.md)\.

------
#### [ Console ]

```
{
  "mqttTopicMapping": {
    "HelloWorldPubsub": {
      "topic": "clients/MyClientDevice1/hello/world",
      "source": "LocalMqtt",
      "target": "Pubsub"
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
        "merge": "\"mqttTopicMapping\":{\"HelloWorldPubsub\":{\"topic\":\"clients/MyClientDevice1/hello/world\",\"source\":\"LocalMqtt\",\"target\":\"Pubsub\"}}}"
      }
    }
    ...
  }
}
```

------

You can use MQTT topic wildcards to relay messages on topics that match a topic filter\. For more information, see [MQTT bridge component configuration](mqtt-bridge-component.md#mqtt-bridge-component-configuration)\.

## Receive MQTT messages from client devices<a name="receive-client-device-messages"></a>

You can subscribe to the local publish/subscribe topics that you configure for the MQTT bridge component to receive messages from client devices\.

**To receive MQTT messages from client devices in custom components**

1. [Configure and deploy the MQTT bridge component](#deploy-mqtt-bridge-pubsub) to relay messages from an MQTT topic where client devices publish to a local publish/subscribe topic\.

1. Use the local publish/subscribe IPC interface to subscribe to the topic where the MQTT bridge relays messages\. For more information, see [Publish/subscribe local messages](ipc-publish-subscribe.md) and [SubscribeToTopic](ipc-publish-subscribe.md#ipc-operation-subscribetotopic)\.

The [Connect and test client devices tutorial](client-devices-tutorial.md) includes a section where you develop a component that subscribes to messages from a client device\. For more information, see [Develop a component that interacts with client devices](client-devices-tutorial.md#develop-client-device-subscriber-component)\.

## Send MQTT messages to client devices<a name="send-client-device-messages"></a>

You can publish to the local publish/subscribe topics that you configure for the MQTT bridge component to send messages to client devices\.

**To publish MQTT messages to client devices in custom components**

1. [Configure and deploy the MQTT bridge component](#deploy-mqtt-bridge-pubsub) to relay messages from a local publish/subscribe topic to an MQTT topic where client devices subscribe\.

1. Use the local publish/subscribe IPC interface to publish to the topic where the MQTT bridge relays messages\. For more information, see [Publish/subscribe local messages](ipc-publish-subscribe.md) and [PublishToTopic](ipc-publish-subscribe.md#ipc-operation-publishtotopic)\.