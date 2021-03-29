# Publish/subscribe AWS IoT Core MQTT messages<a name="ipc-iot-core-mqtt"></a>

The AWS IoT Core MQTT messaging IPC service lets you send and receive MQTT messages to and from AWS IoT Core\. Components can publish messages to AWS IoT Core and subscribe to topics to act on MQTT messages from other sources\. For more information about the AWS IoT Core implementation of MQTT, see [MQTT](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html) in the *AWS IoT Core Developer Guide*\.

**Note**  
This MQTT messaging IPC service lets you exchange messages with AWS IoT Core\. For more information about how to exchange messages between components, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.

**Topics**
+ [Authorization](#ipc-iot-core-mqtt-authorization)
+ [Operations](#ipc-iot-core-mqtt-operations)

## Authorization<a name="ipc-iot-core-mqtt-authorization"></a>

To use AWS IoT Core MQTT messaging in a custom component, you must define authorization policies that allows your component to send and receive messages on topics\. For information about defining authorization policies, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies)\.

Authorization policies for AWS IoT Core MQTT messaging have the following properties\.

**IPC service identifier:** `aws.greengrass.ipc.mqttproxy`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#PublishToIoTCore`  |  Allows a component to publish messages to AWS IoT Core on the MQTT topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string supports MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `aws.greengrass#SubscribeToIoTCore`  |  Allows a component to subscribe to messages from AWS IoT Core on the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string supports MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `*`  |  Allows a component to publish and subscribe to AWS IoT Core MQTT messages for the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string supports MQTT topic wildcards \(`#` and `+`\)\.  | 

## Operations<a name="ipc-iot-core-mqtt-operations"></a>

Use the following operations for AWS IoT Core MQTT messaging\.

**Topics**
+ [PublishToIoTCore](#ipc-operation-publishtoiotcore)
+ [SubscribeToIoTCore](#ipc-operation-subscribetoiotcore)

### PublishToIoTCore<a name="ipc-operation-publishtoiotcore"></a>

Publishes an MQTT message to AWS IoT Core on a topic\.

#### Request<a name="ipc-operation-publishtoiotcore-request"></a>

This operation's request has the following parameters:

`topicName`  
The topic to which to publish the message\.

`qos`  <a name="ipc-iot-core-mqtt-qos"></a>
The MQTT QoS to use\. This enum, `QOS`, has the following values:  
+ `AT_MOST_ONCE` – QoS 0\. The MQTT message is delivered at most once\.
+ `AT_LEAST_ONCE` – QoS 1\. The MQTT message is delivered at least once\.

`payload`  
\(Optional\) The message payload as a blob\.

#### Response<a name="ipc-operation-publishtoiotcore-response"></a>

This operation doesn't provide any information in its response\.

#### Examples<a name="ipc-operation-publishtoiotcore-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Publish a message**  

```
String topic = "my/topic";
String message = "Hello, World!";
QOS qos = QOS.AT_LEAST_ONCE;

PublishToIoTCoreRequest publishToIoTCoreRequest = new PublishToIoTCoreRequest();
publishToIoTCoreRequest.setTopicName(topic);
publishToIoTCoreRequest.setPayload(message.getBytes(StandardCharsets.UTF_8));
publishToIoTCoreRequest.setQos(qos);
greengrassCoreIPCClient.publishToIoTCore(publishToIoTCoreRequest, Optional.empty()).getResponse().get();
```

------
#### [ Python ]

**Example: Publish a message**  
This example assumes that you are using version 1\.5\.4 or later of the AWS IoT Device SDK for Python v2\. If you are using version 1\.5\.3 of the SDK, see [Use AWS IoT Device SDK for Python v2](interprocess-communication.md#ipc-python) for information about connecting to the AWS IoT Greengrass Core IPC service\. 

```
import awsiot.greengrasscoreipc
from awsiot.greengrasscoreipc.model import (
    QOS,
    PublishToIoTCoreRequest
)

TIMEOUT = 10

ipc_client = awsiot.greengrasscoreipc.connect()
                    
topic = "my/topic"
message = "Hello, World"
qos = QOS.AT_LEAST_ONCE

request = PublishToIoTCoreRequest()
request.topic_name = topic
request.payload = bytes(message, "utf-8")
request.qos = qos
operation = ipc_client.new_publish_to_iot_core()
operation.activate(request)
future = operation.get_response()
future.result(TIMEOUT)
```

------

### SubscribeToIoTCore<a name="ipc-operation-subscribetoiotcore"></a>

Subscribe to MQTT messages from AWS IoT Core on a topic or topic filter\. The AWS IoT Greengrass Core software removes subscriptions when the component reaches the end of its lifecycle\.

#### Request<a name="ipc-operation-subscribetoiotcore-request"></a>

This operation's request has the following parameters:

`topicName`  
The topic to which to subscribe\. You can use MQTT topic wildcards \(`#` and `+`\) to subscribe to multiple topics\.

`qos`  <a name="ipc-iot-core-mqtt-qos"></a>
The MQTT QoS to use\. This enum, `QOS`, has the following values:  
+ `AT_MOST_ONCE` – QoS 0\. The MQTT message is delivered at most once\.
+ `AT_LEAST_ONCE` – QoS 1\. The MQTT message is delivered at least once\.

#### Response<a name="ipc-operation-subscribetoiotcore-response"></a>

This operation's response has the following information:

`messages`  
The stream of MQTT messages\. This object, `IoTCoreMessage`, contains the following information:    
message  
The MQTT message\. This object, `MQTTMessage`, contains the following information:    
`topicName`  
The topic to which the message was published\.  
`payload`  
\(Optional\) The message payload as a blob\.

#### Examples<a name="ipc-operation-subscribetoiotcore-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Subscribe to messages**  

```
String topic = "my/topic";
QOS qos = QOS.AT_MOST_ONCE;

SubscribeToIoTCoreRequest subscribeToIoTCoreRequest = new SubscribeToIoTCoreRequest();
subscribeToIoTCoreRequest.setTopicName(topic);
subscribeToIoTCoreRequest.setQos(qos);

StreamResponseHandler<IoTCoreMessage> streamResponseHandler = new StreamResponseHandler<IoTCoreMessage>() {
    @Override
    public void onStreamEvent(IoTCoreMessage ioTCoreMessage) {
        String message = new String(ioTCoreMessage.getMessage().getPayload(), StandardCharsets.UTF_8);
        // Handle message.
    }

    @Override
    public boolean onStreamError(Throwable throwable) {
        // Handle error.
        return false;
    }

    @Override
    public void onStreamClosed() {

    }
};

SubscribeToIoTCoreResponseHandler operationResponseHandler = greengrassCoreIPCClient
        .subscribeToIoTCore(subscribeToIoTCoreRequest, Optional.of(streamResponseHandler));
operationResponseHandler.getResponse().get();

// Keep the main thread alive, or the process will exit.
try {
    while (true) {
        Thread.sleep(10000);
    }
} catch (InterruptedException e) {
    System.out.println("Subscribe interrupted.");
}

// To stop subscribing, close the stream.
operationResponseHandler.closeStream();
```

------
#### [ Python ]

**Example: Subscribe to messages**  
This example assumes that you are using version 1\.5\.4 or later of the AWS IoT Device SDK for Python v2\. If you are using version 1\.5\.3 of the SDK, see [Use AWS IoT Device SDK for Python v2](interprocess-communication.md#ipc-python) for information about connecting to the AWS IoT Greengrass Core IPC service\. 

```
import time

import awsiot.greengrasscoreipc
import awsiot.greengrasscoreipc.client as client
from awsiot.greengrasscoreipc.model import (
    IoTCoreMessage,
    QOS,
    SubscribeToIoTCoreRequest
)

TIMEOUT = 10

ipc_client = awsiot.greengrasscoreipc.connect()

class StreamHandler(client.SubscribeToIoTCoreStreamHandler):
    def __init__(self):
        super().__init__()

    def on_stream_event(self, event: IoTCoreMessage) -> None:
        message = str(event.message.payload, "utf-8")
        # Handle message.

    def on_stream_error(self, error: Exception) -> bool:
        # Handle error.
        return True

    def on_stream_closed(self) -> None:
        pass


topic = "my/topic"
qos = QOS.AT_MOST_ONCE

request = SubscribeToIoTCoreRequest()
request.topic_name = topic
request.qos = qos
handler = StreamHandler()
operation = ipc_client.new_subscribe_to_iot_core(handler)
future = operation.activate(request)
future.result(TIMEOUT)

# Keep the main thread alive, or the process will exit.
while True:
    time.sleep(10)
                  
# To stop subscribing, close the operation stream.
operation.close()
```

------