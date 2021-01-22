# Publish/subscribe local messages<a name="ipc-publish-subscribe"></a>

Publish/subscribe \(pubsub\) messaging enables you to send and receive messages to topics\. Components can publish messages to topics to send messages to other components\. Then, components that are subscribed to that topic can act on the messages that they receive\.

**Note**  
You can't use this publish/subscribe IPC service to publish or subscribe to AWS IoT Core MQTT\. For more information about how to exchange messages with AWS IoT Core MQTT, see the [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.

**Topics**
+ [Authorization](#ipc-publish-subscribe-authorization)
+ [Operations](#ipc-publish-subscribe-operations)
+ [Examples](#ipc-publish-subscribe-examples)

## Authorization<a name="ipc-publish-subscribe-authorization"></a>

To use publish/subscribe messaging IPC service in a custom component, you must define authorization policies that allows your component to send and receive messages to topics\. For information about defining authorization policies, see [Authorize components to use interprocess communication](interprocess-communication.md#ipc-authorization-policies)\.

Authorization policies for publish/subscribe messaging have the following properties\.

**IPC service identifier:** `aws.greengrass.ipc.pubsub`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#PublishToTopic`  |  Allows a component to publish messages to the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string doesn't support MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `aws.greengrass#SubscribeToTopic`  |  Allows a component to subscribe to messages for the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string doesn't support MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `*`  |  Allows a component to publish and subscribe to messages for the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string doesn't support MQTT topic wildcards \(`#` and `+`\)\.  | 

## Operations<a name="ipc-publish-subscribe-operations"></a>

Use the following operations for publish/subscribe messaging\.

**Topics**
+ [PublishToTopic](#ipc-operation-publishtotopic)
+ [SubscribeToTopic](#ipc-operation-subscribetotopic)

### PublishToTopic<a name="ipc-operation-publishtotopic"></a>

Publish a message to a topic\.

#### Request<a name="ipc-operation-publishtotopic-request"></a>

This operation's request has the following parameters:

`topic`  
The topic to which to publish the message\.

`publishMessage`  
The message to publish\. This object, `PublishMessage`, contains the following information\. You must specify one of `jsonMessage` and `binaryMessage`\.  <a name="ipc-publish-subscribe-message-shape"></a>  
`jsonMessage`  
\(Optional\) A JSON message\. This object, `JsonMessage`, contains the following information:    
`message`  
The JSON message as an object\.  
`binaryMessage`  
\(Optional\) A binary message\. This object, `BinaryMessage`, contains the following information:    
`message`  
The binary message as a blob\.

#### <a name="ipc-operation-publishtotopic-response"></a>

This operation doesn't provide any information in its response\.

#### Examples<a name="ipc-operation-publishtotopic-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Publish a binary message**  

```
String topic = "my/topic";
String message = "Hello, World!";

PublishToTopicRequest publishToTopicRequest = new PublishToTopicRequest();
PublishMessage publishMessage = new PublishMessage();
BinaryMessage binaryMessage = new BinaryMessage();
binaryMessage.setMessage(message.getBytes(StandardCharsets.UTF_8));
publishMessage.setBinaryMessage(binaryMessage);
publishToTopicRequest.setPublishMessage(publishMessage);
publishToTopicRequest.setTopic(topic);
greengrassCoreIPCClient.publishToTopic(publishToTopicRequest, Optional.empty()).getResponse().get();
```

------
#### [ Python ]

**Example: Publish a binary message**  

```
import awsiot.greengrasscoreipc.client as client
from awsiot.greengrasscoreipc.model import (
    PublishToTopicRequest,
    PublishMessage,
    BinaryMessage
)

TIMEOUT = 10

ipc_utils = IPCUtils()
connection = ipc_utils.connect()
ipc_client = client.GreengrassCoreIPCClient(connection)
                    
topic = "my/topic"
message = "Hello, World!"

request = PublishToTopicRequest()
request.topic = topic
publish_message = PublishMessage()
publish_message.binary_message = BinaryMessage()
publish_message.binary_message.message = bytes(message, "utf-8")
request.publish_message = publish_message
operation = ipc_client.new_publish_to_topic()
operation.activate(request)
future = operation.get_response()
future.result(TIMEOUT)
```

------

### SubscribeToTopic<a name="ipc-operation-subscribetotopic"></a>

Subscribe to messages at a topic\.

#### Request<a name="ipc-operation-subscribetotopic-request"></a>

This operation's request has the following parameters:

`topic`  
The topic to which to subscribe\.

#### Response<a name="ipc-operation-subscribetotopic-response"></a>

This operation's response has the following information:

`topicName`  
The topic to which the message was published\.

`messages`  
The stream of messages\. This object, `SubscriptionResponseMessage`, contains the following information\. Each message contains `jsonMessage` or `binaryMessage`\.  <a name="ipc-publish-subscribe-message-shape"></a>  
`jsonMessage`  
\(Optional\) A JSON message\. This object, `JsonMessage`, contains the following information:    
`message`  
The JSON message as an object\.  
`binaryMessage`  
\(Optional\) A binary message\. This object, `BinaryMessage`, contains the following information:    
`message`  
The binary message as a blob\.

#### Examples<a name="ipc-operation-subscribetotopic-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Subscribe to messages**  

```
String topic = "my/topic";

SubscribeToTopicRequest subscribeToTopicRequest = new SubscribeToTopicRequest();
subscribeToTopicRequest.setTopic(topic);

StreamResponseHandler<SubscriptionResponseMessage> streamResponseHandler =
        new StreamResponseHandler<SubscriptionResponseMessage>() {
            @Override
            public void onStreamEvent(SubscriptionResponseMessage subscriptionResponseMessage) {
                String message = new String(subscriptionResponseMessage.getBinaryMessage()
                        .getMessage(), StandardCharsets.UTF_8);
                // Handle message.
            }

            @Override
            public boolean onStreamError(Throwable error) {
                // Handle error.
                return false;
            }

            @Override
            public void onStreamClosed() {
                // Handle close.
            }
        };

SubscribeToTopicResponseHandler operationResponseHandler = greengrassCoreIPCClient
        .subscribeToTopic(subscribeToTopicRequest, Optional.of(streamResponseHandler));
operationResponseHandler.getResponse().get();

// To stop subscribing, close the stream.
operationResponseHandler.closeStream();
```

------
#### [ Python ]

**Example: Subscribe to messages**  

```
import awsiot.greengrasscoreipc.client as client
from awsiot.greengrasscoreipc.model import (
    SubscribeToTopicRequest,
    SubscriptionResponseMessage
)

TIMEOUT = 10

ipc_utils = IPCUtils()
connection = ipc_utils.connect()
ipc_client = client.GreengrassCoreIPCClient(connection)
                    
class StreamHandler(client.SubscribeToTopicStreamHandler):
    def __init__(self):
        super().__init__()

    def on_stream_event(self, event: SubscriptionResponseMessage) -> None:
        message_string = str(event.binary_message.message, "utf-8")
        # Handle message.

    def on_stream_error(self, error: Exception) -> bool:
        # Handle error.
        return True

    def on_stream_closed(self) -> None:
        pass


topic = "my/topic"

request = SubscribeToTopicRequest()
request.topic = topic
handler = StreamHandler()
operation = ipc_client.new_subscribe_to_topic(handler)
future = operation.activate(request)
future.result(TIMEOUT)

# To stop subscribing, close the operation stream.
operation.close()
```

------

## Examples<a name="ipc-publish-subscribe-examples"></a>

Use the following examples to learn how to use the publish/subscribe IPC service in your components\.

### Example publish/subscribe publisher<a name="ipc-publish-subscribe-example-publisher"></a>

The following example recipe allows the component to publish to all topics\.

```
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.PubSubPublisher
ComponentVersion: '1.0.0'
ComponentDescription: A component that publishes messages.
ComponentPublisher: Amazon
ComponentConfiguration:
  DefaultConfiguration:
    accessControl:
      aws.greengrass.ipc.pubsub:
        'com.example.PubSubPublisher:pubsub:1':
          policyDescription: Allows access to publish to all topics.
          operations:
            - 'aws.greengrass#PublishToTopic'
          resources:
            - '*'
Manifests:
  - Lifecycle:
      Run: |-
        java -Dlog.level=INFO -jar {artifacts:path}/PubSubPublisher.jar
```

The following example Java application demonstrates how to use the publish/subscribe IPC service to publish messages to other components\.

```
/* Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
 * SPDX-License-Identifier: Apache-2.0 */

package com.example.ipc.pubsub;

import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.model.*;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;

import java.nio.charset.StandardCharsets;
import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;
import java.util.logging.Level;
import java.util.logging.Logger;

public class PubSubPublisher {
    private static final Logger LOGGER = Logger.getLogger(PubSubPublisher.class.getName());

    public static void main(String[] args) {
        String message = "Hello from the pub/sub publisher.";
        String topic = "test/topic";

        while (true) {
            try (EventStreamRPCConnection eventStreamRPCConnection = IPCUtils.getEventStreamRpcConnection()) {
                GreengrassCoreIPCClient ipcClient = new GreengrassCoreIPCClient(eventStreamRPCConnection);

                PublishToTopicRequest publishRequest = new PublishToTopicRequest();
                PublishMessage publishMessage = new PublishMessage();
                BinaryMessage binaryMessage = new BinaryMessage();
                binaryMessage.setMessage(message.getBytes(StandardCharsets.UTF_8));
                publishMessage.setBinaryMessage(binaryMessage);
                publishRequest.setPublishMessage(publishMessage);
                publishRequest.setTopic(topic);
                CompletableFuture<PublishToTopicResponse> futureResponse = ipcClient.publishToTopic(publishRequest, Optional.empty()).getResponse();

                try {
                    futureResponse.get(10, TimeUnit.SECONDS);
                    LOGGER.info("Successfully published to topic: " + topic);
                } catch (TimeoutException e) {
                    LOGGER.warning("Timeout occurred while publishing to topic " + topic);
                } catch (ExecutionException e) {
                    if (e.getCause() instanceof UnauthorizedError) {
                        LOGGER.severe("Unauthorized error while publishing to topic: " + topic);
                    } else {
                        LOGGER.severe("Execution exception while publishing to topic: " + topic);
                    }
                    throw e;
                }
                Thread.sleep(5000);
            } catch (Exception e) {
                LOGGER.log(Level.SEVERE, "Exception occurred when using IPC.", e);
                System.exit(1);
            }
        }
    }
}
```

### Example publish/subscribe subscriber<a name="ipc-publish-subscribe-example-subscriber"></a>

The following example recipe allows the component to subscribe to all topics\.

```
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.PubSubSubscriber
ComponentVersion: '1.0.0'
ComponentDescription: A component that subscribes to messages.
ComponentPublisher: Amazon
ComponentConfiguration:
  DefaultConfiguration:
    accessControl:
      aws.greengrass.ipc.pubsub:
        'com.example.PubSubSubscriber:pubsub:1':
          policyDescription: Allows access to subscribe to all topics.
          operations:
            - 'aws.greengrass#SubscribeToTopic'
          resources:
            - '*'
Manifests:
  - Lifecycle:
      Run: |-
        java -Dlog.level=INFO -jar {artifacts:path}/PubSubSubscriber.jar
```

The following example Java application demonstrates how to use the publish/subscribe IPC service to subscribe to messages to other components\.

```
/* Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
 * SPDX-License-Identifier: Apache-2.0 */

package com.example.ipc.pubsub;

import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.model.SubscribeToTopicRequest;
import software.amazon.awssdk.aws.greengrass.model.SubscribeToTopicResponse;
import software.amazon.awssdk.aws.greengrass.model.SubscriptionResponseMessage;
import software.amazon.awssdk.aws.greengrass.model.UnauthorizedError;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;
import software.amazon.awssdk.eventstreamrpc.StreamResponseHandler;

import java.nio.charset.StandardCharsets;
import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;
import java.util.logging.Level;
import java.util.logging.Logger;

public class PubSubSubscriber {
    private static final Logger LOGGER = Logger.getLogger(PubSubSubscriber.class.getName());

    public static void main(String[] args) {
        String topic = "test/topic";

        while (true) {
            try (EventStreamRPCConnection eventStreamRPCConnection = IPCUtils.getEventStreamRpcConnection()) {
                GreengrassCoreIPCClient ipcClient = new GreengrassCoreIPCClient(eventStreamRPCConnection);

                SubscribeToTopicRequest subscribeRequest = new SubscribeToTopicRequest();
                subscribeRequest.setTopic(topic);
                CompletableFuture<SubscribeToTopicResponse> futureResponse = ipcClient.subscribeToTopic(subscribeRequest, Optional.of(new SubscribeResponseHandler())).getResponse();

                try {
                    futureResponse.get(10, TimeUnit.SECONDS);
                    LOGGER.info("Successfully subscribed to topic: " + topic);
                } catch (TimeoutException e) {
                    LOGGER.warning("Timeout occurred while subscribing to topic " + topic);
                } catch (ExecutionException e) {
                    if (e.getCause() instanceof UnauthorizedError) {
                        LOGGER.severe("Unauthorized error while subscribing to topic: " + topic);
                    } else {
                        LOGGER.severe("Execution exception while subscribing to topic: " + topic);
                    }
                    throw e;
                }
                Thread.sleep(5000);
            } catch (Exception e) {
                LOGGER.log(Level.SEVERE, "Exception occurred when using IPC.", e);
                System.exit(1);
            }
        }
    }

    private static class SubscribeResponseHandler implements StreamResponseHandler<SubscriptionResponseMessage> {

        @Override
        public void onStreamEvent(SubscriptionResponseMessage subscriptionResponseMessage) {
            String message = new String(subscriptionResponseMessage.getBinaryMessage().getMessage(), StandardCharsets.UTF_8);
            LOGGER.info("Received new message: " + message);
        }

        @Override
        public boolean onStreamError(Throwable error) {
            LOGGER.log(Level.WARNING, "Received a stream error.", error);
            return false;
        }

        @Override
        public void onStreamClosed() {
            LOGGER.info("Subscribe to topic stream closed.");
        }
    }
}
```