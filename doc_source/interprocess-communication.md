# Use the AWS IoT Device SDK for interprocess communication \(IPC\)<a name="interprocess-communication"></a>

Components running on your core device can use the AWS IoT Greengrass Core interprocess communication \(IPC\) library in the AWS IoT Device SDK to communicate with other AWS IoT Greengrass components and processes\. To develop and run custom components that use IPC, you must use the AWS IoT Device SDK to connect to the AWS IoT Greengrass Core IPC service and perform IPC operations\.

The IPC interface supports two types of operations:
+ **Request/response**

  Components send a request to the IPC service and receive a response that contains the result of the request\.
+ **Subscription**

  Components send a subscription request to the IPC service and expect a stream of event messages in response\. Components provide a subscription handler that handles event messages, errors, and stream closure\. The AWS IoT Device SDK includes a handler interface with the correct response and event types for each IPC operation\. For more information, see [Subscribe to IPC event streams](#ipc-subscribe-operations)\.

**Topics**
+ [Supported SDKs for interprocess communication](#ipc-requirements)
+ [Connect to the AWS IoT Greengrass Core IPC service](#ipc-service-connect)
+ [Authorize components to perform IPC operations](#ipc-authorization-policies)
+ [Subscribe to IPC event streams](#ipc-subscribe-operations)
+ [Publish/subscribe local messages](ipc-publish-subscribe.md)
+ [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)
+ [Interact with component lifecycle](ipc-component-lifecycle.md)
+ [Interact with component configuration](ipc-component-configuration.md)
+ [Retrieve secret values](ipc-secret-manager.md)

## Supported SDKs for interprocess communication<a name="ipc-requirements"></a>

The AWS IoT Greengrass Core IPC libraries are included in the following AWS IoT Device SDK versions\.
+ [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2) \(v1\.2\.10 or later\)

  For more information about using the AWS IoT Device SDK for Java v2 to connect to the AWS IoT Greengrass Core IPC service, see [Use AWS IoT Device SDK for Java v2](#ipc-java)\.
+ [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2) \(v1\.5\.3 or later\)

  For more information about using the AWS IoT Device SDK for Python v2 to connect to the AWS IoT Greengrass Core IPC service, see [Use AWS IoT Device SDK for Python v2](#ipc-python)\.

## Connect to the AWS IoT Greengrass Core IPC service<a name="ipc-service-connect"></a>

To use interprocess communication in your custom component, you must create a connection to an IPC server socket that the AWS IoT Greengrass Core software runs\. Complete the following tasks to download and use the AWS IoT Device SDK in the language of your choice\. 

### Use AWS IoT Device SDK for Java v2<a name="ipc-java"></a>

**To use the AWS IoT Device SDK for Java v2**

1. Download the [AWS IoT Device SDK for Java](https://github.com/aws/aws-iot-device-sdk-java-v2) \(v1\.2\.10 or later\)\.

1. Do one of the following to run your custom code in your component:
   + Build your component as a JAR file that includes the AWS IoT Device SDK, and run this JAR file in your component recipe\.
   + Define the AWS IoT Device SDK JAR as a component artifact, and add that artifact to the classpath when you run your application in your component recipe\.

1. Create a connection to the AWS IoT Greengrass Core IPC service\. The IPC client, `GreengrassCoreIPCClient`, requires an `EventStreamRPCConnection`\. Download the following `IPCUtils` class that provides this connection for you\.

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

1. Use the following code to create the IPC client\.

   ```
   try (EventStreamRPCConnection eventStreamRPCConnection = IPCUtils.getEventStreamRpcConnection()) {
       GreengrassCoreIPCClient ipcClient = new GreengrassCoreIPCClient(eventStreamRPCConnection);
   } catch (Exception e) {
       LOGGER.log(Level.SEVERE, "Exception occurred when using IPC.", e);
       System.exit(1);
   }
   ```

### Use AWS IoT Device SDK for Python v2<a name="ipc-python"></a>

**To use the AWS IoT Device SDK for Python v2**

1. Download the [AWS IoT Device SDK for Python](https://github.com/aws/aws-iot-device-sdk-python-v2) \(v1\.5\.3 or later\)\.

1. Add the SDK's [installation steps](https://github.com/aws/aws-iot-device-sdk-python-v2#installation) to the install lifecycle in your component's recipe\.

1. Create a connection to the AWS IoT Greengrass Core IPC service\. Complete the following steps to create the IPC client and establish a connection\.

------
#### [ SDK v1\.5\.4 or later ]

   Use the following code to create the IPC client\.

   ```
   import awsiot.greengrasscoreipc
   
   ipc_client = awsiot.greengrasscoreipc.connect()
   ```

------
#### [ SDK v1\.5\.3 ]

   1. Download the following `IPCUtils` class that provides the IPC server connection for you\.

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

   1. Use the following code to create the IPC client\.

      ```
      import awsiot.greengrasscoreipc.client as client
      
      ipc_utils = IPCUtils()
      connection = ipc_utils.connect()
      ipc_client = client.GreengrassCoreIPCClient(connection)
      ```

------

## Authorize components to perform IPC operations<a name="ipc-authorization-policies"></a>

To allow your custom components to use some IPC operations, you must define *authorization policies* that allow the component to perform the operation on certain resources\. Each authorization policy defines a list of operations and a list of resources that the policy allows\. For example, the publish/subscribe messaging IPC service defines publish and subscribe operations for topic resources\. You can use the `*` wildcard to allow access to all operations or all resources\.

You define authorization policies in the component recipe with the `accessControl` configuration parameter\. The `accessControl` object maps IPC service identifiers to lists of authorization policies\. You can define multiple authorization policies for each IPC service to control access\. Each authorization policy has a policy ID, which must be unique among all components\.

**Tip**  
To create unique policy IDs, you can combine the component name, IPC service name, and a counter\. For example, a component named `com.example.HelloWorld` might define two publish/subscribe authorization policies with the following IDs:  
`com.example.HelloWorld:pubsub:1`
`com.example.HelloWorld:pubsub:2`

Authorization policies use the following format\. This object is the `accessControl` configuration parameter\.

------
#### [ JSON ]

```
{
  "IPC service identifier": {
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
  }
}
```

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

**Example component recipe with an authorization policy**  
The following example component recipe includes an `accessControl` object defines an authorization policy\. This policy authorizes the `com.example.HelloWorld` component to publish to the `test/topic` topic\.  

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.HelloWorld",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "A component that publishes messages.",
  "ComponentPublisher": "Amazon",
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "accessControl": {
        "aws.greengrass.ipc.pubsub": {
          "com.example.HelloWorld:pubsub:1": {
            "policyDescription": "Allows access to publish to test/topic.",
            "operations": [
              "aws.greengrass#PublishToTopic"
            ],
            "resources": [
              "test/topic"
            ]
          }
        }
      }
    }
  },
  "Manifests": [
    {
      "Lifecycle": {
        "Run": "java -Dlog.level=INFO -jar {artifacts:path}/HelloWorld.jar"
      }
    }
  ]
}
```

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

## Subscribe to IPC event streams<a name="ipc-subscribe-operations"></a>

You can use IPC operations to subscribe to streams of events on a Greengrass core device\. To use a subscribe operation, define a *subscription handler* and create a request to the IPC service\. Then, the IPC client runs the subscription handler's functions each time that the core device streams an event message to your component\.

You can close a subscription to stop processing event messages\. To do so, call `closeStream()` \(Java\) or `close()` \(Python\) on the subscription operation object that you used to open the subscription\.

The AWS IoT Greengrass Core IPC service supports the following subscribe operations:
+ [SubscribeToTopic](ipc-publish-subscribe.md#ipc-operation-subscribetotopic)
+ [SubscribeToIoTCore](ipc-iot-core-mqtt.md#ipc-operation-subscribetoiotcore)
+ [SubscribeToComponentUpdates](ipc-component-lifecycle.md#ipc-operation-subscribetocomponentupdates)
+ [SubscribeToConfigurationUpdate](ipc-component-configuration.md#ipc-operation-subscribetoconfigurationupdate)
+ [SubscribeToValidateConfigurationUpdates](ipc-component-configuration.md#ipc-operation-subscribetovalidateconfigurationupdates)

**Topics**
+ [Define subscription handlers](#ipc-define-subscription-handlers)
+ [Best practices for subscription handlers](#ipc-subscription-handler-best-practices)
+ [Example subscription handlers](#ipc-subscription-handler-examples)

### Define subscription handlers<a name="ipc-define-subscription-handlers"></a>

To define a subscription handler, create a class with callback functions that handle event messages, errors, and stream closure\.

------
#### [ Java ]

Implement the generic `software.amazon.awssdk.eventstreamrpc.StreamResponseHandler<StreamEventType>` interface\. *StreamEventType* is the type of event message for the subscription operation\. Define the following functions to handle event messages, errors, and stream closure\.

`void onStreamEvent(StreamEventType event)`  
The callback that the IPC client calls when it receives an event message, such as an MQTT message or a component update notification\.

`boolean onStreamError(Throwable error)`  
The callback that the IPC client calls when a stream error occurs\.  
Return `true` to close the subscription stream as a result of the error, or return `false` to keep the stream open\.

`void onStreamClosed()`  
The callback that the IPC client calls when the stream closes\.

------
#### [ Python ]

Extend the stream response handler class that corresponds to the subscription operation\. The AWS IoT Device SDK includes a subscription handler class for each subscription operation\. *StreamEventType* is the type of event message for the subscription operation\. Define the following functions to handle event messages, errors, and stream closure\.

`def on_stream_event(self, event: StreamEventType) -> None`  
The callback that the IPC client calls when it receives an event message, such as an MQTT message or a component update notification\.

`def on_stream_error(self, error: Exception) -> bool`  
The callback that the IPC client calls when a stream error occurs\.  
Return `True` to close the subscription stream as a result of the error, or return `False` to keep the stream open\.

`def on_stream_closed(self) -> None`  
The callback that the IPC client calls when the stream closes\.

------

### Best practices for subscription handlers<a name="ipc-subscription-handler-best-practices"></a>

The IPC client uses a single thread that communicates with the IPC server and calls your subscription handler\. You must consider this synchronous behavior when you write subscription handler functions\. Follow these guidelines when you write subscription handler functions\.
+ **Run blocking code asynchronously**

  The IPC client can't send new requests or process new event messages while the thread is blocked\. You can run blocking code in a separate thread that you run from the handler function\. Blocking code includes `sleep` calls, loops that continuously run, and synchronous I/O requests that take time to complete\.
+ **Send new IPC requests asynchronously**

  The IPC client can't send a new request from within subscription handler functions, because the request blocks the handler function if you wait for a response\. You can send IPC requests in a separate thread that you run from the handler function\.
+ **Handle exceptions**

  The IPC client doesn't handle uncaught exceptions in subscription handler functions\. If your handler function throws an exception, the subscription closes, and the exception doesn't appear in your component logs\. You can catch exceptions in your handler functions to keep the subscription open and log errors that occur in your code\.

### Example subscription handlers<a name="ipc-subscription-handler-examples"></a>

The following example demonstrates how to use the [SubscribeToTopic](ipc-publish-subscribe.md#ipc-operation-subscribetotopic) operation and a subscription handler to subscribe to local publish/subscribe messages\.

------
#### [ Java ]

**Example: Subscribe to local publish/subscribe messages**  <a name="ipc-operation-subscribetotopic-example-java"></a>

```
String topic = "my/topic";

SubscribeToTopicRequest subscribeToTopicRequest = new SubscribeToTopicRequest();
subscribeToTopicRequest.setTopic(topic);

StreamResponseHandler<SubscriptionResponseMessage> streamResponseHandler =
        new StreamResponseHandler<SubscriptionResponseMessage>() {
            @Override
            public void onStreamEvent(SubscriptionResponseMessage subscriptionResponseMessage) {
                try {
                    String message = new String(subscriptionResponseMessage.getBinaryMessage()
                            .getMessage(), StandardCharsets.UTF_8);
                    // Handle message.
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

            @Override
            public boolean onStreamError(Throwable error) {
                // Handle error.
                return false; // Return true to close stream, false to keep stream open.
            }

            @Override
            public void onStreamClosed() {
                // Handle close.
            }
        };

SubscribeToTopicResponseHandler operationResponseHandler = greengrassCoreIPCClient
        .subscribeToTopic(subscribeToTopicRequest, Optional.of(streamResponseHandler));
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

**Example: Subscribe to local publish/subscribe messages**  <a name="ipc-operation-subscribetotopic-example-python"></a>
This example assumes that you are using version 1\.5\.4 or later of the AWS IoT Device SDK for Python v2\. If you are using version 1\.5\.3 of the SDK, see [Use AWS IoT Device SDK for Python v2](#ipc-python) for information about connecting to the AWS IoT Greengrass Core IPC service\. 

```
import time
import traceback

import awsiot.greengrasscoreipc
import awsiot.greengrasscoreipc.client as client
from awsiot.greengrasscoreipc.model import (
    SubscribeToTopicRequest,
    SubscriptionResponseMessage
)

TIMEOUT = 10

ipc_client = awsiot.greengrasscoreipc.connect()
                    
class StreamHandler(client.SubscribeToTopicStreamHandler):
    def __init__(self):
        super().__init__()

    def on_stream_event(self, event: SubscriptionResponseMessage) -> None:
        try:
            message_string = str(event.binary_message.message, "utf-8")
            # Handle message.
        except:
            traceback.print_exc()

    def on_stream_error(self, error: Exception) -> bool:
        # Handle error.
        return True  # Return True to close stream, False to keep stream open.

    def on_stream_closed(self) -> None:
        # Handle close.
        pass


topic = "my/topic"

request = SubscribeToTopicRequest()
request.topic = topic
handler = StreamHandler()
operation = ipc_client.new_subscribe_to_topic(handler) 
future = operation.activate(request)
future.result(TIMEOUT)

# Keep the main thread alive, or the process will exit.
while True:
    time.sleep(10)
    
# To stop subscribing, close the operation stream.
operation.close()
```

------