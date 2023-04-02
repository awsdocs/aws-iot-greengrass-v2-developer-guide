# Use the AWS IoT Device SDK to communicate with the Greengrass nucleus, other components, and AWS IoT Core<a name="interprocess-communication"></a>

Components running on your core device can use the AWS IoT Greengrass Core interprocess communication \(IPC\) library in the AWS IoT Device SDK to communicate with the AWS IoT Greengrass nucleus and other Greengrass components\. To develop and run custom components that use IPC, you must use the AWS IoT Device SDK to connect to the AWS IoT Greengrass Core IPC service and perform IPC operations\.

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
+ [Interact with local shadows](ipc-local-shadows.md)
+ [Manage local deployments and components](ipc-local-deployments-components.md)
+ [Authenticate and authorize client devices](ipc-client-device-auth.md)

## Supported SDKs for interprocess communication<a name="ipc-requirements"></a>

The AWS IoT Greengrass Core IPC libraries are included in the following AWS IoT Device SDK versions\.
+ [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2) \(v1\.2\.10 or later\)

  For more information about using the AWS IoT Device SDK for Java v2 to connect to the AWS IoT Greengrass Core IPC service, see [Use AWS IoT Device SDK for Java v2](#ipc-java)\.
+ [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2) \(v1\.5\.3 or later\)

  For more information about using the AWS IoT Device SDK for Python v2 to connect to the AWS IoT Greengrass Core IPC service, see [Use AWS IoT Device SDK for Python v2](#ipc-python)\.
+ [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2) \(Linux: v1\.13\.0 or later; Windows: v1\.14\.6 or later\)

  For more information about using the AWS IoT Device SDK for C\+\+ v2 to connect to the AWS IoT Greengrass Core IPC service, see [Use AWS IoT Device SDK for C\+\+ v2](#ipc-cpp)\.

## Connect to the AWS IoT Greengrass Core IPC service<a name="ipc-service-connect"></a>

To use interprocess communication in your custom component, you must create a connection to an IPC server socket that the AWS IoT Greengrass Core software runs\. Complete the following tasks to download and use the AWS IoT Device SDK in the language of your choice\. 

### Use AWS IoT Device SDK for Java v2<a name="ipc-java"></a>

**To use the AWS IoT Device SDK for Java v2**

1. Download the [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2) \(v1\.2\.10 or later\)\.

1. Do one of the following to run your custom code in your component:
   + Build your component as a JAR file that includes the AWS IoT Device SDK, and run this JAR file in your component recipe\.
   + Define the AWS IoT Device SDK JAR as a component artifact, and add that artifact to the classpath when you run your application in your component recipe\.

1. Create a connection to the AWS IoT Greengrass Core IPC service\. The IPC client, `GreengrassCoreIPCClient`, requires an `EventStreamRPCConnection`\. Download the following `IPCUtils` class that provides this connection for you\.

   ```
   /* Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
    * SPDX-License-Identifier: Apache-2.0 */
   
   package com.example.greengrass.util;
   
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
       // It is ignored but the field needs to be set when creating socket connection.
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
               final boolean[] disconnected = {false};
               final int[] disconnectedCode = {-1};
               connection.connect(new EventStreamRPCConnection.LifecycleHandler() {
                   // Only called on successful connection.
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
   
                   // This on error is for any error that is connection level, including problems during connect()
                   @Override
                   public boolean onError(Throwable t) {
                       connected.completeExceptionally(t);
                       clientConnection = null;
                       return true; // True instructs handler to disconnect due to this error.
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

### Use AWS IoT Device SDK for C\+\+ v2<a name="ipc-cpp"></a>

**Note**  
AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 

<a name="iot-device-sdk-cpp-v2-build-requirements-intro"></a>To build the AWS IoT Device SDK v2 for C\+\+, a device must have the following tools:<a name="iot-device-sdk-cpp-v2-build-requirements"></a>
+ C\+\+ 11 or later
+ CMake 3\.1 or later
+ One of the following compilers:
  + GCC 4\.8 or later
  + Clang 3\.9 or later
  + MSVC 2015 or later

**To use the AWS IoT Device SDK for C\+\+ v2**

1. Download the [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2) \(Linux: v1\.13\.0 or later; Windows: v1\.14\.6 or later\)\.

1. Follow the [installation instructions in the README](https://github.com/aws/aws-iot-device-sdk-cpp-v2#Installation) to build the AWS IoT Device SDK for C\+\+ v2 from source\.

1. In your C\+\+ build tool, link the Greengrass IPC library, `AWS::GreengrassIpc-cpp`, that you built in the previous step\. The following `CMakeLists.txt` example links the Greengrass IPC library to a project that you build with CMake\.

   ```
   cmake_minimum_required(VERSION 3.1)
   project (greengrassv2_pubsub_subscriber)
   
   file(GLOB MAIN_SRC
           "*.h"
           "*.cpp"
           )
   add_executable(${PROJECT_NAME} ${MAIN_SRC})
   
   set_target_properties(${PROJECT_NAME} PROPERTIES
           LINKER_LANGUAGE CXX
           CXX_STANDARD 11)
   find_package(aws-crt-cpp PATHS ~/sdk-cpp-workspace/build)
   find_package(EventstreamRpc-cpp PATHS ~/sdk-cpp-workspace/build)
   find_package(GreengrassIpc-cpp PATHS ~/sdk-cpp-workspace/build)
   target_link_libraries(${PROJECT_NAME} AWS::GreengrassIpc-cpp)
   ```

1. In your component code, create a connection to the AWS IoT Greengrass Core IPC service to create an IPC client \(`Aws::Greengrass::GreengrassCoreIpcClient`\)\. You must define an IPC connection lifecycle handler that handles IPC connection, disconnection, and error events\. The following example creates an IPC client and an IPC connection lifecycle handler that prints when the IPC client connects, disconnects, and encounters errors\.

   ```
   #include <iostream>
   
   #include <aws/crt/Api.h>
   #include <aws/greengrass/GreengrassCoreIpcClient.h>
   
   using namespace Aws::Crt;
   using namespace Aws::Greengrass;
   
   class IpcClientLifecycleHandler : public ConnectionLifecycleHandler {
       void OnConnectCallback() override {
           std::cout << "OnConnectCallback" << std::endl;
       }
   
       void OnDisconnectCallback(RpcError error) override {
           std::cout << "OnDisconnectCallback: " << error.StatusToString() << std::endl;
           exit(-1);
       }
   
       bool OnErrorCallback(RpcError error) override {
           std::cout << "OnErrorCallback: " << error.StatusToString() << std::endl;
           return true;
       }
   };
   
   int main() {
       // Create the IPC client.
       ApiHandle apiHandle(g_allocator);
       Io::EventLoopGroup eventLoopGroup(1);
       Io::DefaultHostResolver socketResolver(eventLoopGroup, 64, 30);
       Io::ClientBootstrap bootstrap(eventLoopGroup, socketResolver);
       IpcClientLifecycleHandler ipcLifecycleHandler;
       GreengrassCoreIpcClient ipcClient(bootstrap);
       auto connectionStatus = ipcClient.Connect(ipcLifecycleHandler).get();
       if (!connectionStatus) {
           std::cerr << "Failed to establish IPC connection: " << connectionStatus.StatusToString() << std::endl;
           exit(-1);
       }
       
       String message("Hello, World!");
       String topic("my/topic");
       int timeout = 10;
       
       // Use the IPC client to create an operation request.
       PublishToTopicRequest request;
       Vector<uint8_t> messageData({message.begin(), message.end()});
       BinaryMessage binaryMessage;
       binaryMessage.SetMessage(messageData);
       PublishMessage publishMessage;
       publishMessage.SetBinaryMessage(binaryMessage);
       request.SetTopic(topic);
       request.SetPublishMessage(publishMessage);
       
       // Activate the operation request.
       auto activate = operation->Activate(request, nullptr);
       activate.wait();
   
       // Wait for Greengrass Core to respond to the request.
       auto responseFuture = operation->GetResult();
       if (responseFuture.wait_for(std::chrono::seconds(timeout)) == std::future_status::timeout) {
           std::cerr << "Operation timed out while waiting for response from Greengrass Core." << std::endl;
           exit(-1);
       }
   
       // Check the result of the request.
       auto response = responseFuture.get();
       if (response) {
           std::cout << "Successfully published to topic: " << topic << std::endl;
       } else {
           // An error occurred.
           std::cout << "Failed to publish to topic: " << topic << std::endl;
           auto errorType = response.GetResultType();
           if (errorType == OPERATION_ERROR) {
               auto *error = response.GetOperationError();
               std::cout << "Operation error: " << error->GetMessage().value() << std::endl;
           } else {
               std::cout << "RPC error: " << response.GetRpcError() << std::endl;
           }
           exit(-1);
       }
       
       return 0;
   }
   ```

1. To run your custom code in your component, build your code as a binary artifact, and run the binary artifact in your component recipe\. Set the artifact's `Execute` permission to `OWNER` to enable the AWS IoT Greengrass Core software to run the binary artifact\.

   Your component recipe's `Manifests` section might look similar to the following example\.

------
#### [ JSON ]

   ```
   {
     ...
     "Manifests": [
       {
         "Lifecycle": {
           "Run": "{artifacts:path}/greengrassv2_pubsub_subscriber"
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.PubSubSubscriberCpp/1.0.0/greengrassv2_pubsub_subscriber",
             "Permission": {
               "Execute": "OWNER"
             }
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   ...
   Manifests:
     - Lifecycle:
         Run: {artifacts:path}/greengrassv2_pubsub_subscriber
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.PubSubSubscriberCpp/1.0.0/greengrassv2_pubsub_subscriber
           Permission:
             Execute: OWNER
   ```

------

## Authorize components to perform IPC operations<a name="ipc-authorization-policies"></a>

To allow your custom components to use some IPC operations, you must define *authorization policies* that allow the component to perform the operation on certain resources\. Each authorization policy defines a list of operations and a list of resources that the policy allows\. For example, the publish/subscribe messaging IPC service defines publish and subscribe operations for topic resources\. You can use the `*` wildcard to allow access to all operations or all resources\.

You define authorization policies with the `accessControl` configuration parameter, which you can set in the component recipe or when you deploy the component\. The `accessControl` object maps IPC service identifiers to lists of authorization policies\. You can define multiple authorization policies for each IPC service to control access\. Each authorization policy has a policy ID, which must be unique among all components\.

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

### Wildcards in authorization policies<a name="ipc-authorization-policy-wildcards"></a>

You can use the `*` wildcard in the `resources` element of IPC authorization policies to allow access to multiple resources in a single authorization policy\.
+ In all versions of the [Greengrass nucleus](greengrass-nucleus-component.md), you can specify a single `*` character as a resource to allow access to all resources\.
+ In [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 and later, you can specify the `*` character in a resource to match any combination of characters\. For example, you can specify `factory/1/devices/Thermostat*/status` to allow access to a status topic for all thermostat devices in a factory, where each device's name begins with `Thermostat`\.

When you define authorization policies for the AWS IoT Core MQTT IPC service, you can also use MQTT wildcards \(`+` and `#`\) to match multiple resources\. For more information, see [MQTT wildcards in AWS IoT Core MQTT IPC authorization policies](ipc-iot-core-mqtt.md#ipc-iot-core-mqtt-authorization-mqtt-wildcards)\.

### Recipe variables in authorization policies<a name="ipc-authorization-policy-recipe-variables"></a>

If you use [Greengrass nucleus](greengrass-nucleus-component.md) v2\.6\.0 or later, you can use the `{iot:thingName}` [recipe variable](component-recipe-reference.md#recipe-variables) in authorization policies\. When you need an authorization policy that includes the core device's name, such as for MQTT topics or device shadows, you can use this recipe variable to configure a single authorization policy for a group of core devices\. For example, you can allow a component access to the following resource for shadow IPC operations\.

```
$aws/things/{iot:thingName}/shadow/
```

### Special characters in authorization policies<a name="ipc-authorization-policy-special-characters"></a>

To specify a literal `*` or `?` character in an authorization policy, you must use an escape sequence\. The following escape sequences instruct the AWS IoT Greengrass Core software to use the literal value instead of the character's special meaning\. For example, the `*` character is a [wildcard](#ipc-authorization-policy-wildcards) that matches any combination of characters\.


| Literal character | Escape sequence | Notes | 
| --- | --- | --- | 
|  `*`  |  `${*}`  |  | 
|  `?`  |  `${?}`  |  AWS IoT Greengrass doesn't currently support the `?` wildcard, which matches any single character\.  | 
|  `$`  |  `${$}`  |  Use this escape sequence to match a resource that contains `${`\. For example, to match a resource named `${resourceName}`, you must specify `${$}{resourceName}`\. Otherwise, to match a resource that contains `$`, you can use a literal `$`, such as to allow access to a topic that begins with `$aws`\.  | 

### Authorization policy examples<a name="ipc-authorization-policy-examples"></a>

You can reference the following authorization policy examples to help you configure authorization policies for your components\.

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
        "Run": "java -jar {artifacts:path}/HelloWorld.jar"
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
        java -jar {artifacts:path}/HelloWorld.jar
```

**Example component configuration update with an authorization policy**  
The following example configuration update in a deployment specifies to configure a component with an `accessControl` object that defines an authorization policy\. This policy authorizes the `com.example.HelloWorld` component to publish to the `test/topic` topic\.    
**Configuration to merge**  

```
{
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
```
The following command creates a deployment to a core device\.  

```
aws greengrassv2 create-deployment --cli-input-json file://hello-world-deployment.json
```
The `hello-world-deployment.json` file contains the following JSON document\.  

```
{
  "targetArn": "arn:aws:iot:us-west-2:123456789012:thing/MyGreengrassCore",
  "deploymentName": "Deployment for MyGreengrassCore",
  "components": {
    "com.example.HelloWorld": {
      "componentVersion": "1.0.0",
      "configurationUpdate": {
        "merge": "{\"accessControl\":{\"aws.greengrass.ipc.pubsub\":{\"com.example.HelloWorld:pubsub:1\":{\"policyDescription\":\"Allows access to publish to test/topic.\",\"operations\":[\"aws.greengrass#PublishToTopic\"],\"resources\":[\"test/topic\"]}}}}"
      }
    }
  }
}
```
The following [Greengrass CLI](greengrass-cli-component.md) command creates a local deployment on a core device\.  

```
sudo greengrass-cli deployment create \
  --recipeDir recipes \
  --artifactDir artifacts \
  --merge "com.example.HelloWorld=1.0.0" \
  --update-config hello-world-configuration.json
```
The `hello-world-configuration.json` file contains the following JSON document\.  

```
{
  "com.example.HelloWorld": {
    "MERGE": {
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
  }
}
```

## Subscribe to IPC event streams<a name="ipc-subscribe-operations"></a>

You can use IPC operations to subscribe to streams of events on a Greengrass core device\. To use a subscribe operation, define a *subscription handler* and create a request to the IPC service\. Then, the IPC client runs the subscription handler's functions each time that the core device streams an event message to your component\.

You can close a subscription to stop processing event messages\. To do so, call `closeStream()` \(Java\), `close()` \(Python\), or `Close()` \(C\+\+\) on the subscription operation object that you used to open the subscription\.

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
<a name="ipc-subscription-handler-on-stream-error-return-value"></a>Return true to close the subscription stream as a result of the error, or return false to keep the stream open\.

`void onStreamClosed()`  
The callback that the IPC client calls when the stream closes\.

------
#### [ Python ]

Extend the stream response handler class that corresponds to the subscription operation\. The AWS IoT Device SDK includes a subscription handler class for each subscription operation\. *StreamEventType* is the type of event message for the subscription operation\. Define the following functions to handle event messages, errors, and stream closure\.

`def on_stream_event(self, event: StreamEventType) -> None`  
The callback that the IPC client calls when it receives an event message, such as an MQTT message or a component update notification\.

`def on_stream_error(self, error: Exception) -> bool`  
The callback that the IPC client calls when a stream error occurs\.  
<a name="ipc-subscription-handler-on-stream-error-return-value"></a>Return true to close the subscription stream as a result of the error, or return false to keep the stream open\.

`def on_stream_closed(self) -> None`  
The callback that the IPC client calls when the stream closes\.

------
#### [ C\+\+ ]

Implement a class that derives from the stream response handler class that corresponds to the subscription operation\. The AWS IoT Device SDK includes a subscription handler base class for each subscription operation\. *StreamEventType* is the type of event message for the subscription operation\. Define the following functions to handle event messages, errors, and stream closure\.

`void OnStreamEvent(StreamEventType *event)`  
The callback that the IPC client calls when it receives an event message, such as an MQTT message or a component update notification\.

`bool OnStreamError(OperationError *error)`  
The callback that the IPC client calls when a stream error occurs\.  
<a name="ipc-subscription-handler-on-stream-error-return-value"></a>Return true to close the subscription stream as a result of the error, or return false to keep the stream open\.

`void OnStreamClosed()`  
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
This example uses an `IPCUtils` class to create a connection to the AWS IoT Greengrass Core IPC service\. For more information, see [Connect to the AWS IoT Greengrass Core IPC service](#ipc-service-connect)\.

```
package com.aws.greengrass.docs.samples.ipc;

import com.aws.greengrass.docs.samples.ipc.util.IPCUtils;
import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.SubscribeToTopicResponseHandler;
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

public class SubscribeToTopic {
    public static final int TIMEOUT_SECONDS = 10;

    public static void main(String[] args) {
        String topic = args[0];
        try (EventStreamRPCConnection eventStreamRPCConnection =
                     IPCUtils.getEventStreamRpcConnection()) {
            GreengrassCoreIPCClient ipcClient =
                    new GreengrassCoreIPCClient(eventStreamRPCConnection);
            StreamResponseHandler<SubscriptionResponseMessage> streamResponseHandler =
                    new SubscriptionResponseHandler(topic);
            SubscribeToTopicResponseHandler responseHandler =
                    SubscribeToTopic.subscribeToTopic(ipcClient, topic, streamResponseHandler);
            CompletableFuture<SubscribeToTopicResponse> futureResponse =
                    responseHandler.getResponse();
            try {
                futureResponse.get(TIMEOUT_SECONDS, TimeUnit.SECONDS);
                System.out.println("Successfully subscribed to topic: " + topic);
            } catch (TimeoutException e) {
                System.err.println("Timeout occurred while subscribing to topic: " + topic);
            } catch (ExecutionException e) {
                if (e.getCause() instanceof UnauthorizedError) {
                    System.err.println("Unauthorized error while publishing to topic: " + topic);
                } else {
                    throw e;
                }
            }

            // Keep the main thread alive, or the process will exit.
            try {
                while (true) {
                    Thread.sleep(10000);
                }
            } catch (InterruptedException e) {
                System.out.println("Subscribe interrupted.");
            }

            // To stop subscribing, close the stream.
            responseHandler.closeStream();
        } catch (InterruptedException e) {
            System.out.println("IPC interrupted.");
        } catch (ExecutionException e) {
            System.err.println("Exception occurred when using IPC.");
            e.printStackTrace();
            System.exit(1);
        }
    }

    public static SubscribeToTopicResponseHandler subscribeToTopic(GreengrassCoreIPCClient greengrassCoreIPCClient, String topic, StreamResponseHandler<SubscriptionResponseMessage> streamResponseHandler) {
        SubscribeToTopicRequest subscribeToTopicRequest = new SubscribeToTopicRequest();
        subscribeToTopicRequest.setTopic(topic);
        return greengrassCoreIPCClient.subscribeToTopic(subscribeToTopicRequest,
                Optional.of(streamResponseHandler));
    }

    public static class SubscriptionResponseHandler implements StreamResponseHandler<SubscriptionResponseMessage> {

        private final String topic;

        public SubscriptionResponseHandler(String topic) {
            this.topic = topic;
        }

        @Override
        public void onStreamEvent(SubscriptionResponseMessage subscriptionResponseMessage) {
            try {
                String message =
                        new String(subscriptionResponseMessage.getBinaryMessage().getMessage(),
                                StandardCharsets.UTF_8);
                System.out.printf("Received new message on topic %s: %s%n", this.topic, message);
            } catch (Exception e) {
                System.err.println("Exception occurred while processing subscription response " +
                        "message.");
                e.printStackTrace();
            }
        }

        @Override
        public boolean onStreamError(Throwable error) {
            System.err.println("Received a stream error.");
            error.printStackTrace();
            return false; // Return true to close stream, false to keep stream open.
        }

        @Override
        public void onStreamClosed() {
            System.out.println("Subscribe to topic stream closed.");
        }
    }
}
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
operation.activate(request)
future_response = operation.get_response()
future_response.result(TIMEOUT)

# Keep the main thread alive, or the process will exit.
while True:
    time.sleep(10)
    
# To stop subscribing, close the operation stream.
operation.close()
```

------
#### [ C\+\+ ]

**Example: Subscribe to local publish/subscribe messages**  <a name="ipc-operation-subscribetotopic-example-cpp"></a>

```
#include <iostream>

#include <aws/crt/Api.h>
#include <aws/greengrass/GreengrassCoreIpcClient.h>

using namespace Aws::Crt;
using namespace Aws::Greengrass;

class SubscribeResponseHandler : public SubscribeToTopicStreamHandler {
    void OnStreamEvent(SubscriptionResponseMessage *response) override {
        auto jsonMessage = response->GetJsonMessage();
        if (jsonMessage.has_value() && jsonMessage.value().GetMessage().has_value()) {
            auto messageString = jsonMessage.value().GetMessage().value().View().WriteReadable();
            // Handle JSON message.
        } else {
            auto binaryMessage = response->GetBinaryMessage();
            if (binaryMessage.has_value() && binaryMessage.value().GetMessage().has_value()) {
                auto messageBytes = binaryMessage.value().GetMessage().value();
                std::string messageString(messageBytes.begin(), messageBytes.end());
                // Handle binary message.
            }
        } 
    }

    bool OnStreamError(OperationError *error) override {
        // Handle error.
        return false; // Return true to close stream, false to keep stream open.
    }
    
    void OnStreamClosed() override {
        // Handle close.
    }
};

class IpcClientLifecycleHandler : public ConnectionLifecycleHandler {
    void OnConnectCallback() override {
        // Handle connection to IPC service.
    }

    void OnDisconnectCallback(RpcError error) override {
        // Handle disconnection from IPC service.
    }

    bool OnErrorCallback(RpcError error) override {
        // Handle IPC service connection error.
        return true;
    }
};

int main() {
    ApiHandle apiHandle(g_allocator);
    Io::EventLoopGroup eventLoopGroup(1);
    Io::DefaultHostResolver socketResolver(eventLoopGroup, 64, 30);
    Io::ClientBootstrap bootstrap(eventLoopGroup, socketResolver);
    IpcClientLifecycleHandler ipcLifecycleHandler;
    GreengrassCoreIpcClient ipcClient(bootstrap);
    auto connectionStatus = ipcClient.Connect(ipcLifecycleHandler).get();
    if (!connectionStatus) {
        std::cerr << "Failed to establish IPC connection: " << connectionStatus.StatusToString() << std::endl;
        exit(-1);
    }
    
    String topic("my/topic");
    int timeout = 10;

    SubscribeToTopicRequest request;
    request.SetTopic(topic);
    
    SubscribeResponseHandler streamHandler;
    SubscribeToTopicOperation operation = ipcClient.NewSubscribeToTopic(streamHandler);
    auto activate = operation.Activate(request, nullptr);
    activate.wait();

    auto responseFuture = operation.GetResult();
    if (responseFuture.wait_for(std::chrono::seconds(timeout)) == std::future_status::timeout) {
        std::cerr << "Operation timed out while waiting for response from Greengrass Core." << std::endl;
        exit(-1);
    }
    
    auto response = responseFuture.get();
    if (!response) {
        // Handle error.
        auto errorType = response.GetResultType();
        if (errorType == OPERATION_ERROR) {
            auto *error = response.GetOperationError();
            // Handle operation error.
        } else {
            // Handle RPC error.
        }
        exit(-1);
    }

    // Keep the main thread alive, or the process will exit.
    while (true) {
        std::this_thread::sleep_for(std::chrono::seconds(10));
    }
    
    operation.Close();
    return 0;
}
```

------