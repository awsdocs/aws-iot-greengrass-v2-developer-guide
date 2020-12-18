# Interprocess communication<a name="interprocess-communication"></a>

AWS IoT Greengrass Core provides an interprocess communication \(IPC\) SDK that you can use in your custom components to communicate with other components and processes\.

**Topics**
+ [Requirements](#ipc-requirements)
+ [Authorize components to use interprocess communication](#ipc-authorization-policies)
+ [Publish/subscribe messaging](#ipc-publish-subscribe)
+ [AWS IoT Core MQTT messaging](#ipc-iot-core-mqtt)
+ [Component lifecycle](#ipc-component-lifecycle)
+ [Component configuration](#ipc-component-configuration)
+ [Secret manager](#ipc-secret-manager)

## Requirements<a name="ipc-requirements"></a>

To develop custom component that you IPC, you must use the AWS IoT Device SDK v2\. Use the following information to download and use the SDK in the language of your choice\.

------
#### [ Java ]

Download the [AWS IoT Device SDK for Java](https://github.com/aws/aws-iot-device-sdk-java-v2)\.

Then, do one of the following to run your custom code in your component:
+ Build your component as a JAR file that includes the AWS IoT Device SDK, and run this JAR file in your component recipe\.
+ Define the AWS IoT Device SDK JAR as a component artifact, and add that artifact to the classpath when you run your application in your component recipe\.

To use IPC in your custom component, you must create a connection to an IPC server socket that the AWS IoT Greengrass Core software runs\. The IPC client, `GreengrassCoreIPCClient`, requires an `EventStreamRPCConnection`\. You can use the following `IPCUtils` class that provides this connection for you\.

```
/* Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
 * SPDX-License-Identifier: Apache-2.0 */

import software.amazon.awssdk.crt.io.ClientBootstrap;
import software.amazon.awssdk.crt.io.EventLoopGroup;
import software.amazon.awssdk.crt.io.SocketOptions;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnectionConfig;
import software.amazon.awssdk.eventstreamrpc.GreengrassConnectMessageSupplier;
 
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
 
public final class IPCUtils {
    // Port number is not used in domain sockets.
    // It is ignored but the field needs to be set when creating socket connection
    public static final int DEFAULT_PORT_NUMBER = 8033;
    private static EventStreamRPCConnection clientConnection = null;
 
    private IPCUtils() {
 
    }
 
    public static EventStreamRPCConnection getEventStreamRpcConnection()
            throws ExecutionException, InterruptedException {
        String ipcServerSocketPath = System.getenv("AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT");
        String authToken = System.getenv("SVCUID");
        SocketOptions socketOptions = IPCUtils.getSocketOptionsForIPC();
 
        if (clientConnection == null) {
            clientConnection = connectToGGCOverEventStreamIPC(socketOptions, authToken, ipcServerSocketPath);
        }
        return clientConnection;
    }
 
    // removed dependency on kernel, as it is only being used to pull ipcServerSocketPath
    private static EventStreamRPCConnection connectToGGCOverEventStreamIPC(SocketOptions socketOptions,
                                                                           String authToken, String ipcServerSocketPath)
            throws ExecutionException, InterruptedException {
 
        try (EventLoopGroup elGroup = new EventLoopGroup(1);
             ClientBootstrap clientBootstrap = new ClientBootstrap(elGroup, null)) {
 
            final EventStreamRPCConnectionConfig config =
                    new EventStreamRPCConnectionConfig(clientBootstrap, elGroup, socketOptions, null,
                            ipcServerSocketPath, DEFAULT_PORT_NUMBER,
                            GreengrassConnectMessageSupplier.connectMessageSupplier(authToken));
            final CompletableFuture<Void> connected = new CompletableFuture<>();
            final EventStreamRPCConnection connection = new EventStreamRPCConnection(config);
            final boolean disconnected[] = {false};
            final int disconnectedCode[] = {-1};
            //this is a bit cumbersome but does not prevent a convenience wrapper from exposing a sync
            //connect() or a connect() that returns a CompletableFuture that errors
            //this could be wrapped by utility methods to provide a more
            connection.connect(new EventStreamRPCConnection.LifecycleHandler() {
                //only called on successful connection.
                // That is full on Connect -> ConnectAck(ConnectionAccepted=true)
                @Override
                public void onConnect() {
                    connected.complete(null);
                }
 
                @Override
                public void onDisconnect(int errorCode) {
 
                    disconnected[0] = true;
                    disconnectedCode[0] = errorCode;
                    clientConnection = null;
                }
 
                //This on error is for any errors that is connection level, including problems during connect()
                @Override
                public boolean onError(Throwable t) {
                    connected.completeExceptionally(t);
                    clientConnection = null;
                    return true;    //hints at handler to disconnect due to this error
                }
            });
            connected.get();
            return connection;
        }
    }
 
    private static SocketOptions getSocketOptionsForIPC() {
        SocketOptions socketOptions = new SocketOptions();
        socketOptions.connectTimeoutMs = 3000;
        socketOptions.domain = SocketOptions.SocketDomain.LOCAL;
        socketOptions.type = SocketOptions.SocketType.STREAM;
        return socketOptions;
    }
}
```

Then, use the following code to create the IPC client\.

```
try (EventStreamRPCConnection eventStreamRPCConnection = IPCUtils.getEventStreamRpcConnection()) {
    GreengrassCoreIPCClient ipcClient = new GreengrassCoreIPCClient(eventStreamRPCConnection);
} catch (Exception e) {
    LOGGER.log(Level.SEVERE, "Exception occurred when using IPC.", e);
    System.exit(1);
}
```

------
#### [ Python ]

Download the [AWS IoT Device SDK for Python](https://github.com/aws/aws-iot-device-sdk-python-v2)\.

Then, add the SDK's [installation steps](https://github.com/aws/aws-iot-device-sdk-python-v2#installation) to the install lifecycle in your component's recipe\.

To use IPC in your custom component, you must create a connection to an IPC server socket that the AWS IoT Greengrass Core software runs\. You can use the following `IPCUtils` class that provides this connection for you\.

```
# Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
# SPDX-License-Identifier: Apache-2.0

import os

from awscrt.io import (
    ClientBootstrap,
    DefaultHostResolver,
    EventLoopGroup,
    SocketDomain,
    SocketOptions,
)
from awsiot.eventstreamrpc import Connection, LifecycleHandler, MessageAmendment

TIMEOUT = 10


class IPCUtils:
    def connect(self):
        elg = EventLoopGroup()
        resolver = DefaultHostResolver(elg)
        bootstrap = ClientBootstrap(elg, resolver)
        socket_options = SocketOptions()
        socket_options.domain = SocketDomain.Local
        amender = MessageAmendment.create_static_authtoken_amender(os.getenv("SVCUID"))
        hostname = os.getenv("AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT")
        connection = Connection(
            host_name=hostname,
            port=8033,
            bootstrap=bootstrap,
            socket_options=socket_options,
            connect_message_amender=amender,
        )
        self.lifecycle_handler = LifecycleHandler()
        connect_future = connection.connect(self.lifecycle_handler)
        connect_future.result(TIMEOUT)
        return connection
```

Then, use the following code to create the IPC client\.

```
import awsiot.greengrasscoreipc.client as client

ipc_utils = IPCUtils()
connection = ipc_utils.connect()
ipc_client = client.GreengrassCoreIPCClient(connection)
```

------

## Authorize components to use interprocess communication<a name="ipc-authorization-policies"></a>

To allow your custom components to use some IPC operations, you must define *authorization policies* that allow the component to perform the operation on certain resources\. Each authorization policy defines a list of operations and a list of resources that the policy allows\. For example, the publish/subscribe messaging IPC service defines publish and subscribe operations for topic resources\. You can use the `*` wildcard to allow access to all operations or all resources\.

You define authorization policies in the component recipe with the `accessControl` configuration parameter\. The `accessControl` object maps IPC service identifiers to lists of authorization policies\. You can define multiple authorization policies for each IPC service to control access\. Each authorization policy has a policy ID, which must be unique among all components\.

**Tip**  
To create unique policy IDs, you can combine the component name, IPC service name, and a counter\. For example, a component named `com.example.HelloWorld` might define two publish/subscribe authorization policies with the following IDs:  
`com.example.HelloWorld:pubsub:1`
`com.example.HelloWorld:pubsub:2`

Authorization policies use the following format\. This object is the `accessControl` configuration parameter\.

------
#### [ YAML ]

```
IPC service identifier:
  policyId:
    policyDescription: description
    operations:
      - operation1
      - operation2
    resources:
      - resource1
      - resource2
```

------
#### [ JSON ]

```
{
  "IPC service identifier": [
    "policyId": {
      "policyDescription": "description",
      "operations": [
        "operation1",
        "operation2"
      ],
      "resources": [
        "resource1",
        "resource2"
      ]
    }
  ]
}
```

------

**Example recipe with an authorization policy**  
The following example component recipe includes an `accessControl` object defines an authorization policy\. This policy authorizes the `com.example.HelloWorld` component to publish to the `test/topic` topic\.  

```
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.HelloWorld
ComponentVersion: '1.0.0'
ComponentDescription: A component that publishes messages.
ComponentPublisher: Amazon
ComponentConfiguration:
  DefaultConfiguration:
    accessControl:
      aws.greengrass.ipc.pubsub:
        "com.example.HelloWorld:pubsub:1":
          policyDescription: Allows access to publish to test/topic.
          operations:
            - "aws.greengrass#PublishToTopic"
          resources:
            - "test/topic"
Manifests:
  - Lifecycle:
      Run: |-
        java -Dlog.level=INFO -jar {artifacts:path}/HelloWorld.jar
```

## Publish/subscribe messaging<a name="ipc-publish-subscribe"></a>

Publish/subscribe \(pubsub\) messaging enables you to send and receive messages to topics\. Components can publish messages to topics to send messages to other components\. Then, components that are subscribed to that topic can act on the messages that they receive\.

**Note**  
You can't use this publish/subscribe IPC service to publish or subscribe to AWS IoT Core MQTT\. For more information about how to exchange messages with AWS IoT Core MQTT, see the [AWS IoT Core MQTT messaging](#ipc-iot-core-mqtt)\.

### Authorization<a name="ipc-publish-subscribe-authorization"></a>

To use publish/subscribe messaging IPC service in a custom component, you must define authorization policies that allows your component to send and receive messages to topics\. Authorization policies for publish/subscribe messaging have the following properties\.

**IPC service identifier:** `aws.greengrass.ipc.pubsub`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#PublishToTopic`  |  Allows a component to publish messages to the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string doesn't support MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `aws.greengrass#SubscribeToTopic`  |  Allows a component to subscribe to messages for the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string doesn't support MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `*`  |  Allows a component to publish and subscribe to messages for the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string doesn't support MQTT topic wildcards \(`#` and `+`\)\.  | 

### Operations<a name="ipc-publish-subscribe-operations"></a>

Use the following operations for publish/subscribe messaging\.

#### PublishToTopic<a name="ipc-operation-publishtotopic"></a>

Publish a message to a topic\.

##### Request<a name="ipc-operation-publishtotopic-request"></a>

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

##### <a name="ipc-operation-publishtotopic-response"></a>

This operation doesn't provide any information in its response\.

#### SubscribeToTopic<a name="ipc-operation-subscribetotopic"></a>

Subscribe to messages at a topic\.

##### Request<a name="ipc-operation-subscribetotopic-request"></a>

This operation's request has the following parameters:

`topic`  
The topic to which to subscribe\.

##### Response<a name="ipc-operation-subscribetotopic-response"></a>

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

### Examples<a name="ipc-publish-subscribe-examples"></a>

Use the following examples to learn how to use the publish/subscribe IPC service in your components\.

#### Example publish/subscribe publisher<a name="ipc-publish-subscribe-example-publisher"></a>

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

#### Example publish/subscribe subscriber<a name="ipc-publish-subscribe-example-subscriber"></a>

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

## AWS IoT Core MQTT messaging<a name="ipc-iot-core-mqtt"></a>

The AWS IoT Core MQTT messaging IPC service lets you send and receive MQTT messages to and from AWS IoT Core\. Components can publish messages to AWS IoT Core and subscribe to topics to act on MQTT messages from other sources\. For more information about the AWS IoT Core implementation of MQTT, see [MQTT](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html) in the *AWS IoT Core Developer Guide*\.

**Note**  
This MQTT messaging IPC service lets you exchange messages with AWS IoT Core\. For more information about how to exchange messages between components, see [Publish/subscribe messaging](#ipc-publish-subscribe)\.

### Authorization<a name="ipc-iot-core-mqtt-authorization"></a>

To use AWS IoT Core MQTT messaging in a custom component, you must define authorization policies that allows your component to send and receive messages on topics\. Authorization policies for AWS IoT Core MQTT messaging have the following properties\.

**IPC service identifier:** `aws.greengrass.ipc.mqttproxy`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#PublishToIoTCore`  |  Allows a component to publish messages to AWS IoT Core on the MQTT topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string supports MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `aws.greengrass#SubscribeToIoTCore`  |  Allows a component to subscribe to messages from AWS IoT Core on the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string supports MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `*`  |  Allows a component to publish and subscribe to AWS IoT Core MQTT messages for the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string supports MQTT topic wildcards \(`#` and `+`\)\.  | 

### Operations<a name="ipc-iot-core-mqtt-operations"></a>

Use the following operations for AWS IoT Core MQTT messaging\.

#### PublishToIoTCore<a name="ipc-operation-publishtoiotcore"></a>

Publishes an MQTT message to AWS IoT Core on a topic\.

##### Request<a name="ipc-operation-publishtoiotcore-request"></a>

This operation's request has the following parameters:

`topicName`  
The topic to which to publish the message\.

`qos`  <a name="ipc-iot-core-mqtt-qos"></a>
The MQTT QoS to use\. This enum, `QOS`, has the following values:  
+ `AT_MOST_ONCE` – QoS 0\. The MQTT message is delivered at most once\.
+ `AT_LEAST_ONCE` – QoS 1\. The MQTT message is delivered at least once\.

`payload`  
\(Optional\) The message payload as a blob\.

##### Response<a name="ipc-operation-publishtoiotcore-response"></a>

This operation doesn't provide any information in its response\.

#### SubscribeToIoTCore<a name="ipc-operation-subscribetoiotcore"></a>

Subscribe to MQTT messages from AWS IoT Core on a topic or topic filter\. The AWS IoT Greengrass Core software removes subscriptions when the component reaches the end of its lifecycle\.

##### Request<a name="ipc-operation-subscribetoiotcore-request"></a>

This operation's request has the following parameters:

`topicName`  
The topic to which to subscribe\. You can use MQTT topic wildcards \(`#` and `+`\) to subscribe to multiple topics\.

`qos`  <a name="ipc-iot-core-mqtt-qos"></a>
The MQTT QoS to use\. This enum, `QOS`, has the following values:  
+ `AT_MOST_ONCE` – QoS 0\. The MQTT message is delivered at most once\.
+ `AT_LEAST_ONCE` – QoS 1\. The MQTT message is delivered at least once\.

##### Response<a name="ipc-operation-subscribetoiotcore-response"></a>

This operation's response has the following information:

`messages`  
The stream of MQTT messages\. This object, `IoTCoreMessage`, contains the following information:    
message  
The MQTT message\. This object, `MQTTMessage`, contains the following information:    
`topicName`  
The topic to which the message was published\.  
`payload`  
\(Optional\) The message payload as a blob\.

## Component lifecycle<a name="ipc-component-lifecycle"></a>

The component lifecycle IPC service lets you do the following:
+ Update component state on the core device
+ Subscribe to component state updates
+ Prevent the kernel from stopping the component to apply an update

### Operations<a name="ipc-component-lifecycle-operations"></a>

Use the following operations to manage component lifecycle\.

#### UpdateState<a name="ipc-operation-updatestate"></a>

Update the state of the component on the core device\.

##### Request<a name="ipc-operation-updatestate-request"></a>

This operation's request has the following parameters:

`state`  
The state to set\. This enum, `LifecycleState`, has the following values:  
+ `RUNNING`
+ `ERRORED`

##### Response<a name="ipc-operation-updatestate-response"></a>

This operation doesn't provide any information in its response\.

#### SubscribeToComponentUpdates<a name="ipc-operation-subscribetocomponentupdates"></a>

Subscribe to receive notifications before the AWS IoT Greengrass Core software updates a component\. The notification specifies whether or not the kernel will restart as part of the update\.

The kernel sends update notifications only if the deployment's component update policy specifies to notify components\. The default behavior is to notify components\. For more information, see [Create deployments](create-deployments.md) and the [ComponentUpdatePolicy](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ComponentUpdatePolicy.html) object that you can provide when you call the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation\.

**Important**  
Local deployments don't notify components before updates\.

##### Request<a name="ipc-operation-subscribetocomponentupdates-request"></a>

This operation's request doesn't have any parameters\.

##### Response<a name="ipc-operation-subscribetocomponentupdates-response"></a>

This operation's response has the following information:

`messages`  
The stream of notification messages\. This object, `ComponentUpdatePolicyEvents`, contains the following information:    
`preUpdateEvent`  
\(Optional\) An event that indicates that the kernel wants to update a component\. You can respond with the [DeferComponentUpdate](#ipc-operation-defercomponentupdate) operation to acknowledge or defer the update until your component is ready to restart\. This object, `PreComponentUpdateEvent`, contains the following information:    
`deploymentId`  
The ID of the AWS IoT Greengrass deployment that updates the component\.  
`isGgcRestarting`  
Whether or not the kernel needs to restart to apply the update\.  
`postUpdateEvent`  
\(Optional\) An event that indicates that the kernel updated a component\. This object, `PostComponentUpdateEvent`, contains the following information:    
`deploymentId`  
The ID of the AWS IoT Greengrass deployment that updated the component\.

#### DeferComponentUpdate<a name="ipc-operation-defercomponentupdate"></a>

Acknowledge or defer a component update that you discover with [SubscribeToComponentUpdates](#ipc-operation-subscribetocomponentupdates)\. You specify the amount of time to wait before the kernel checks again if your component is ready to let the component update proceed\. You can also use this operation to tell the kernel that your component is ready for the update\.

If a component doesn't respond to the component update notification, the kernel waits the amount of time that you specify in the deployment's component update policy\. After that timeout, the kernel proceeds with the deployment\. The default component update timeout is 60 seconds\. For more information, see [Create deployments](create-deployments.md) and the [ComponentUpdatePolicy](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ComponentUpdatePolicy.html) object that you can provide when you call the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation\.

##### Request<a name="ipc-operation-defercomponentupdate-request"></a>

This operation's request has the following parameters:

`deploymentId`  
The ID of the AWS IoT Greengrass deployment to defer\.

`message`  
\(Optional\) The name of the component for which to defer updates\.  
Defaults to the name of the component that makes the request\.

`recheckAfterMs`  
The amount of time in milliseconds for which to defer the update\. The kernel waits for this amount of time and then sends another `PreComponentUpdateEvent` that you can discover with [SubscribeToComponentUpdates](#ipc-operation-subscribetocomponentupdates)\.  
Specify `0` to acknowledge the update\. This tells the kernel that your component is ready for the update\.  
Defaults to zero milliseconds, which means to acknowledge the update\.

##### Response<a name="ipc-operation-defercomponentupdate-response"></a>

This operation doesn't provide any information in its response\.

## Component configuration<a name="ipc-component-configuration"></a>

The component configuration IPC service lets you do the following:
+ Get and set component configuration parameters
+ Subscribe to component configuration updates
+ Validate component configuration updates before the kernel applies them

### Operations<a name="ipc-component-configuration-operations"></a>

Use the following operations to manage component configurations\.

#### GetConfiguration<a name="ipc-operation-getconfiguration"></a>

Gets a configuration value for a component on the core device\. You specify the key path for which to get a configuration value\.

##### Request<a name="ipc-operation-getconfiguration-request"></a>

This operation's request has the following parameters:

`componentName`  <a name="ipc-configuration-request-component-name"></a>
\(Optional\) The name of the component\.  
Defaults to the name of the component that makes the request\.

`keyPath`  
The key path to the configuration value\. Specify a list where each entry is the key for a single level in the configuration object\. For example, specify `["mqtt", "port"]` to get the value of `port` in the following configuration\.  

```
{
  "mqtt": {
    "port": 443
  }
}
```

##### Response<a name="ipc-operation-getconfiguration-response"></a>

This operation's response has the following information:

`componentName`  <a name="ipc-configuration-response-component-name"></a>
The name of the component\.

`value`  
The requested configuration as an object\.

#### UpdateConfiguration<a name="ipc-operation-updateconfiguration"></a>

Updates a configuration value for a component on the core device\.

##### Request<a name="ipc-operation-updateconfiguration-request"></a>

This operation's request has the following parameters:

`keyPath`  
\(Optional\) The key path to the container node \(the object\) to update\. Specify a list where each entry is the key for a single level in the configuration object\. For example, specify the key path `["mqtt"]` and the merge value `{ "port": 443 }` to set the value of `port` in the following configuration\.  

```
{
  "mqtt": {
    "port": 443
  }
}
```
The key path must specify a container node \(an object\) in the configuration\. If the node doesn't exist in the component's configuration, this operation creates it and sets its value to the object in `valueToMerge`\.  
Defaults to the root of the configuration object\.

`timestamp`  
The current Unix epoch time in milliseconds\. This operation uses this timestamp to resolve concurrent updates to the key\. If the key in the component configuration has a greater timestamp than the timestamp in the request, then the request fails\.

`valueToMerge`  
The configuration object to merge at the location that you specify in `keyPath`\. For more information, see [Update component configurations](update-component-configurations.md)\.

##### Response<a name="ipc-operation-updateconfiguration-response"></a>

This operation doesn't provide any information in its response\.

#### SubscribeToConfigurationUpdate<a name="ipc-operation-subscribetoconfigurationupdate"></a>

Subscribe to receive notifications when a component's configuration updates\. When you subscribe to a key, you receive a notification when any child of that key updates\.

##### Request<a name="ipc-operation-subscribetoconfigurationupdate-request"></a>

This operation's request has the following parameters:

`componentName`  <a name="ipc-configuration-request-component-name"></a>
\(Optional\) The name of the component\.  
Defaults to the name of the component that makes the request\.

`keyPath`  
The key path to the configuration value for which to subscribe\. Specify a list where each entry is the key for a single level in the configuration object\. For example, specify `["mqtt", "port"]` to get the value of `port` in the following configuration\.  

```
{
  "mqtt": {
    "port": 443
  }
}
```

##### Response<a name="ipc-operation-subscribetoconfigurationupdate-response"></a>

This operation's response has the following information:

`messages`  
The stream of notification messages\. This object, `ConfigurationUpdateEvents`, contains the following information:    
`configurationUpdateEvent`  
The configuration update event\. This object, `ConfigurationUpdateEvent`, contains the following information:    
`componentName`  <a name="ipc-configuration-response-component-name"></a>
The name of the component\.  
`keyPath`  
The key path to the configuration value that updated\.

#### SubscribeToValidateConfigurationUpdates<a name="ipc-operation-subscribetovalidateconfigurationupdates"></a>

Subscribe to receive notifications before this component's configuration updates\. This lets components validate updates to their own configuration\. Use the [SendConfigurationValidityReport](#ipc-operation-sendconfigurationvalidityreport) operation to tell the kernel whether or not the configuration is valid\.

**Important**  
Local deployments don't notify components of updates\.

##### Request<a name="ipc-operation-subscribetovalidateconfigurationupdates-request"></a>

This operation's request doesn't have any parameters\.

##### Response<a name="ipc-operation-subscribetovalidateconfigurationupdates-response"></a>

This operation's response has the following information:

`messages`  
The stream of notification messages\. This object, `ValidateConfigurationUpdateEvents`, contains the following information:    
`validateConfigurationUpdateEvent`  
The configuration update event\. This object, `ValidateConfigurationUpdateEvent`, contains the following information:    
`deploymentId`  
The ID of the AWS IoT Greengrass deployment that updates the component\.  
`configuration`  
The object that contains the new configuration\.

#### SendConfigurationValidityReport<a name="ipc-operation-sendconfigurationvalidityreport"></a>

Tell the kernel whether or not a configuration update to this component is valid\. The deployment fails if you tell the kernel that the new configuration isn't valid\. Use the [SubscribeToValidateConfigurationUpdates](#ipc-operation-subscribetovalidateconfigurationupdates) operation to subscribe to validate configuration updates\.

If a component doesn't respond to a validate configuration update notification, the kernel waits the amount of time that you specify in the deployment's configuration validation policy\. After that timeout, the kernel proceeds with the deployment\. The default component validation timeout is 20 seconds\. For more information, see [Create deployments](create-deployments.md) and the [ConfigurationValidationPolicy](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ConfigurationValidationPolicy.html) object that you can provide when you call the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) operation\.

##### Request<a name="ipc-operation-sendconfigurationvalidityreport-request"></a>

This operation's request has the following parameters:

`configurationValidityReport`  
The report that tells the kernel whether or not the configuration update is valid\. This object, `ConfigurationValidityReport`, contains the following information:    
`status`  
The validity status\. This enum, `ConfigurationValidityStatus`, has the following values:  
+ `SUCCEEDED` – The configuration is valid and the kernel can apply it to this component\.
+ `FAILED` – The configuration isn't valid and the deployment fails\.  
`deploymentId`  
The ID of the AWS IoT Greengrass deployment that requested the configuration update\.  
`message`  
\(Optional\) A message that reports why the configuration isn't valid\.

##### Response<a name="ipc-operation-sendconfigurationvalidityreport-response"></a>

This operation doesn't provide any information in its response\.

## Secret manager<a name="ipc-secret-manager"></a>

Use the secret manager IPC service to retrieve secret values from secrets on the core device\. You use the [secret manager component](secret-manager-component.md) to deploy encrypted secrets to core devices\. Then, you can use an IPC operation to decrypt the secret and use its value in your custom components\.

### Authorization<a name="ipc-secret-manager-authorization"></a>

To use secret manager in a custom component, you must define authorization policies that allow your component to get the value of secrets that you store on the core device\. Authorization policies for secret manager have the following properties\.

**IPC service identifier:** `aws.greengrass.SecretManager`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#GetSecretValue` or `*`  |  Allows a component to get the value of secrets that are encrypted on the core device\.  |  A Secrets Manager secret ARN, or `*` to allow access to all secrets\.  | 

### Operations<a name="ipc-secret-manager-operations"></a>

Use the following operation to get the values of secrets\.

#### GetSecretValue<a name="ipc-operation-getsecretvalue"></a>

Gets the value of a secret that you store on the core device\.

This operation is similar to the Secrets Manager operation that you can use to get the value of a secret in the AWS Cloud\. For more information, see [GetSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html) in the *AWS Secrets Manager API Reference*\.

##### Request<a name="ipc-operation-getsecretvalue-request"></a>

This operation's request has the following parameters:

`secretId`  
The name of the secret to get\. You can specify either the Amazon Resource Name \(ARN\) or the friendly name of the secret\.

`versionId`  
\(Optional\) The ID of the version to get\.  
You can specify either `versionId` or `versionStage`\.  
If you don't specify `versionId` or `versionStage`, this operation defaults to the version with the `AWSCURRENT` label\.

`versionStage`  
\(Optional\) The staging label of the version to get\.  
You can specify either `versionId` or `versionStage`\.  
If you don't specify `versionId` or `versionStage`, this operation defaults to the version with the `AWSCURRENT` label\.

##### Response<a name="ipc-operation-getsecretvalue-response"></a>

This operation's response has the following information:

`secretId`  
The ID of the secret\.

`versionId`  
The ID of this version of the secret\.

`versionStage`  
The list of staging labels attached to this version of the secret\.

`secretValue`  
The value of this version of the secret\.

##### Examples<a name="ipc-operation-getsecretvalue-examples"></a>

The following example demonstrates how to call this operation in custom component code\.

```
GreengrassCoreIPCClient greengrassCoreIPCClient = new GreengrassCoreIPCClient(eventStreamRpcConnection);
GetSecretValueRequest request = new GetSecretValueRequest();
request.setSecretId(secretId);
request.setVersionStage(versionStage);
GetSecretValueResponse result = greengrassCoreIPCClient.getSecretValue(request, Optional.empty()).getResponse().get();
System.out.println("secretString=" + result.getSecretValue().getSecretString());
```