# Publish/subscribe AWS IoT Core MQTT messages<a name="ipc-iot-core-mqtt"></a>

The AWS IoT Core MQTT messaging IPC service lets you send and receive MQTT messages to and from AWS IoT Core\. Components can publish messages to AWS IoT Core and subscribe to topics to act on MQTT messages from other sources\. For more information about the AWS IoT Core implementation of MQTT, see [MQTT](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html) in the *AWS IoT Core Developer Guide*\.

**Note**  
This MQTT messaging IPC service lets you exchange messages with AWS IoT Core\. For more information about how to exchange messages between components, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.

**Topics**
+ [Minimum SDK versions](#ipc-iot-core-mqtt-sdk-versions)
+ [Authorization](#ipc-iot-core-mqtt-authorization)
+ [PublishToIoTCore](#ipc-operation-publishtoiotcore)
+ [SubscribeToIoTCore](#ipc-operation-subscribetoiotcore)
+ [Examples](#ipc-iot-core-mqtt-examples)

## Minimum SDK versions<a name="ipc-iot-core-mqtt-sdk-versions"></a>

The following table lists the minimum versions of the AWS IoT Device SDK that you must use to publish and subscribe to MQTT messages to and from AWS IoT Core\.


| SDK | Minimum version | 
| --- | --- | 
|  [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2)  |  v1\.2\.10  | 
|  [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2)  |  v1\.5\.3  | 
|  [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2)  |  Linux: v1\.13\.0; Windows: v1\.14\.6  | 

## Authorization<a name="ipc-iot-core-mqtt-authorization"></a>

To use AWS IoT Core MQTT messaging in a custom component, you must define authorization policies that allows your component to send and receive messages on topics\. For information about defining authorization policies, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies)\.

Authorization policies for AWS IoT Core MQTT messaging have the following properties\.

**IPC service identifier:** `aws.greengrass.ipc.mqttproxy`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#PublishToIoTCore`  |  Allows a component to publish messages to AWS IoT Core on the MQTT topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string supports MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `aws.greengrass#SubscribeToIoTCore`  |  Allows a component to subscribe to messages from AWS IoT Core on the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string supports MQTT topic wildcards \(`#` and `+`\)\.  | 
|  `*`  |  Allows a component to publish and subscribe to AWS IoT Core MQTT messages for the topics that you specify\.  |  A topic string, such as `test/topic`, or `*` to allow access to all topics\. This topic string supports MQTT topic wildcards \(`#` and `+`\)\.  | 

**Example authorization policy**  
The following example authorization policy allows a component to publish and subscribe to all topics\.  

```
{
  "accessControl": {
    "aws.greengrass.ipc.mqttproxy": {
      "com.example.MyIoTCorePubSubComponent:mqttproxy:1": {
        "policyDescription": "Allows access to publish/subscribe to all topics.",
        "operations": [
          "aws.greengrass#PublishToIoTCore",
          "aws.greengrass#SubscribeToIoTCore"
        ],
        "resources": [
          "*"
        ]
      }
    }
  }
}
```

## PublishToIoTCore<a name="ipc-operation-publishtoiotcore"></a>

Publishes an MQTT message to AWS IoT Core on a topic\.

### Request<a name="ipc-operation-publishtoiotcore-request"></a>

This operation's request has the following parameters:

`topicName` \(Python: `topic_name`\)  
The topic to which to publish the message\.

`qos`  <a name="ipc-iot-core-mqtt-qos"></a>
The MQTT QoS to use\. This enum, `QOS`, has the following values:  
+ `AT_MOST_ONCE` – QoS 0\. The MQTT message is delivered at most once\.
+ `AT_LEAST_ONCE` – QoS 1\. The MQTT message is delivered at least once\.

`payload`  
\(Optional\) The message payload as a blob\.

### Response<a name="ipc-operation-publishtoiotcore-response"></a>

This operation doesn't provide any information in its response\.

### Examples<a name="ipc-operation-publishtoiotcore-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Publish a message**  
This example uses an `IPCUtils` class to create a connection to the AWS IoT Greengrass Core IPC service\. For more information, see [Connect to the AWS IoT Greengrass Core IPC service](interprocess-communication.md#ipc-service-connect)\.

```
package com.aws.greengrass.docs.samples.ipc;

import com.aws.greengrass.docs.samples.ipc.util.IPCUtils;
import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.PublishToIoTCoreResponseHandler;
import software.amazon.awssdk.aws.greengrass.model.PublishToIoTCoreRequest;
import software.amazon.awssdk.aws.greengrass.model.PublishToIoTCoreResponse;
import software.amazon.awssdk.aws.greengrass.model.QOS;
import software.amazon.awssdk.aws.greengrass.model.UnauthorizedError;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;

import java.nio.charset.StandardCharsets;
import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

public class PublishToIoTCore {

    public static final int TIMEOUT_SECONDS = 10;

    public static void main(String[] args) {
        String topic = args[0];
        String message = args[1];
        QOS qos = QOS.get(args[2]);
        try (EventStreamRPCConnection eventStreamRPCConnection =
                     IPCUtils.getEventStreamRpcConnection()) {
            GreengrassCoreIPCClient ipcClient =
                    new GreengrassCoreIPCClient(eventStreamRPCConnection);
            PublishToIoTCoreResponseHandler responseHandler =
                    PublishToIoTCore.publishBinaryMessageToTopic(ipcClient, topic, message, qos);
            CompletableFuture<PublishToIoTCoreResponse> futureResponse =
                    responseHandler.getResponse();
            try {
                futureResponse.get(TIMEOUT_SECONDS, TimeUnit.SECONDS);
                System.out.println("Successfully published to topic: " + topic);
            } catch (TimeoutException e) {
                System.err.println("Timeout occurred while publishing to topic: " + topic);
            } catch (ExecutionException e) {
                if (e.getCause() instanceof UnauthorizedError) {
                    System.err.println("Unauthorized error while publishing to topic: " + topic);
                } else {
                    throw e;
                }
            }
        } catch (InterruptedException e) {
            System.out.println("IPC interrupted.");
        } catch (ExecutionException e) {
            System.err.println("Exception occurred when using IPC.");
            e.printStackTrace();
            System.exit(1);
        }
    }

    public static PublishToIoTCoreResponseHandler publishBinaryMessageToTopic(GreengrassCoreIPCClient greengrassCoreIPCClient, String topic, String message, QOS qos) {
        PublishToIoTCoreRequest publishToIoTCoreRequest = new PublishToIoTCoreRequest();
        publishToIoTCoreRequest.setTopicName(topic);
        publishToIoTCoreRequest.setPayload(message.getBytes(StandardCharsets.UTF_8));
        publishToIoTCoreRequest.setQos(qos);
        return greengrassCoreIPCClient.publishToIoTCore(publishToIoTCoreRequest, Optional.empty());
    }
}
```

------
#### [ Python ]

**Example: Publish a message**  
This example assumes that you are using version 1\.5\.4 or later of the AWS IoT Device SDK for Python v2\. If you are using version 1\.5\.3 of the SDK, see [Use AWS IoT Device SDK for Python v2](interprocess-communication.md#ipc-python) for information about connecting to the AWS IoT Greengrass Core IPC service\. 

```
import awsiot.greengrasscoreipc
import awsiot.greengrasscoreipc.client as client
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
#### [ C\+\+ ]

**Example: Publish a message**  

```
#include <iostream>

#include <aws/crt/Api.h>
#include <aws/greengrass/GreengrassCoreIpcClient.h>

using namespace Aws::Crt;
using namespace Aws::Greengrass;

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

    String message("Hello, World!");
    String topic("my/topic");
    QOS qos = QOS_AT_MOST_ONCE;
    int timeout = 10;
    
    PublishToIoTCoreRequest request;
    Vector<uint8_t> messageData({message.begin(), message.end()});
    request.SetTopicName(topic);
    request.SetPayload(messageData);
    request.SetQos(qos);

    PublishToIoTCoreOperation operation = ipcClient.NewPublishToIoTCore();
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
    }

    return 0;
}
```

------

## SubscribeToIoTCore<a name="ipc-operation-subscribetoiotcore"></a>

Subscribe to MQTT messages from AWS IoT Core on a topic or topic filter\. The AWS IoT Greengrass Core software removes subscriptions when the component reaches the end of its lifecycle\.

<a name="ipc-subscribe-operation-note"></a>This operation is a subscription operation where you subscribe to a stream of event messages\. To use this operation, define a stream response handler with functions that handle event messages, errors, and stream closure\. For more information, see [Subscribe to IPC event streams](interprocess-communication.md#ipc-subscribe-operations)\.

**Event message type:** `IoTCoreMessage`

### Request<a name="ipc-operation-subscribetoiotcore-request"></a>

This operation's request has the following parameters:

`topicName` \(Python: `topic_name`\)  
The topic to which to subscribe\. You can use MQTT topic wildcards \(`#` and `+`\) to subscribe to multiple topics\.

`qos`  <a name="ipc-iot-core-mqtt-qos"></a>
The MQTT QoS to use\. This enum, `QOS`, has the following values:  
+ `AT_MOST_ONCE` – QoS 0\. The MQTT message is delivered at most once\.
+ `AT_LEAST_ONCE` – QoS 1\. The MQTT message is delivered at least once\.

### Response<a name="ipc-operation-subscribetoiotcore-response"></a>

This operation's response has the following information:

`messages`  
The stream of MQTT messages\. This object, `IoTCoreMessage`, contains the following information:    
`message`  
The MQTT message\. This object, `MQTTMessage`, contains the following information:    
`topicName` \(Python: `topic_name`\)  
The topic to which the message was published\.  
`payload`  
\(Optional\) The message payload as a blob\.

### Examples<a name="ipc-operation-subscribetoiotcore-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Subscribe to messages**  
This example uses an `IPCUtils` class to create a connection to the AWS IoT Greengrass Core IPC service\. For more information, see [Connect to the AWS IoT Greengrass Core IPC service](interprocess-communication.md#ipc-service-connect)\.

```
package com.aws.greengrass.docs.samples.ipc;

import com.aws.greengrass.docs.samples.ipc.util.IPCUtils;
import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.SubscribeToIoTCoreResponseHandler;
import software.amazon.awssdk.aws.greengrass.model.*;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;
import software.amazon.awssdk.eventstreamrpc.StreamResponseHandler;

import java.nio.charset.StandardCharsets;
import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

public class SubscribeToIoTCore {

    public static final int TIMEOUT_SECONDS = 10;

    public static void main(String[] args) {
        String topic = args[0];
        QOS qos = QOS.get(args[1]);
        try (EventStreamRPCConnection eventStreamRPCConnection =
                     IPCUtils.getEventStreamRpcConnection()) {
            GreengrassCoreIPCClient ipcClient =
                    new GreengrassCoreIPCClient(eventStreamRPCConnection);
            StreamResponseHandler<IoTCoreMessage> streamResponseHandler =
                    new SubscriptionResponseHandler();
            SubscribeToIoTCoreResponseHandler responseHandler =
                    SubscribeToIoTCore.subscribeToIoTCore(ipcClient, topic, qos,
                            streamResponseHandler);
            CompletableFuture<SubscribeToIoTCoreResponse> futureResponse =
                    responseHandler.getResponse();
            try {
                futureResponse.get(TIMEOUT_SECONDS, TimeUnit.SECONDS);
                System.out.println("Successfully subscribed to topic: " + topic);
            } catch (TimeoutException e) {
                System.err.println("Timeout occurred while subscribing to topic: " + topic);
            } catch (ExecutionException e) {
                if (e.getCause() instanceof UnauthorizedError) {
                    System.err.println("Unauthorized error while subscribing to topic: " + topic);
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

    public static SubscribeToIoTCoreResponseHandler subscribeToIoTCore(GreengrassCoreIPCClient greengrassCoreIPCClient, String topic, QOS qos, StreamResponseHandler<IoTCoreMessage> streamResponseHandler) {
        SubscribeToIoTCoreRequest subscribeToIoTCoreRequest = new SubscribeToIoTCoreRequest();
        subscribeToIoTCoreRequest.setTopicName(topic);
        subscribeToIoTCoreRequest.setQos(qos);
        return greengrassCoreIPCClient.subscribeToIoTCore(subscribeToIoTCoreRequest,
                Optional.of(streamResponseHandler));
    }

    public static class SubscriptionResponseHandler implements StreamResponseHandler<IoTCoreMessage> {

        @Override
        public void onStreamEvent(IoTCoreMessage ioTCoreMessage) {
            try {
                String topic = ioTCoreMessage.getMessage().getTopicName();
                String message = new String(ioTCoreMessage.getMessage().getPayload(),
                        StandardCharsets.UTF_8);
                System.out.printf("Received new message on topic %s: %s%n", topic, message);
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
            return false;
        }

        @Override
        public void onStreamClosed() {
            System.out.println("Subscribe to IoT Core stream closed.");
        }
    }
}
```

------
#### [ Python ]

**Example: Subscribe to messages**  
This example assumes that you are using version 1\.5\.4 or later of the AWS IoT Device SDK for Python v2\. If you are using version 1\.5\.3 of the SDK, see [Use AWS IoT Device SDK for Python v2](interprocess-communication.md#ipc-python) for information about connecting to the AWS IoT Greengrass Core IPC service\. 

```
import time
import traceback

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
        try:
            message = str(event.message.payload, "utf-8")
            topic_name = event.message.topic_name
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
#### [ C\+\+ ]

**Example: Subscribe to messages**  

```
#include <iostream>

#include <aws/crt/Api.h>
#include <aws/greengrass/GreengrassCoreIpcClient.h>

using namespace Aws::Crt;
using namespace Aws::Greengrass;

class IoTCoreResponseHandler : public SubscribeToIoTCoreStreamHandler {
    void OnStreamEvent(IoTCoreMessage *response) override {
        auto message = response->GetMessage();
        if (message.has_value() && message.value().GetPayload().has_value()) {
            auto messageBytes = message.value().GetPayload().value();
            std::string messageString(messageBytes.begin(), messageBytes.end());
            std::string topicName = message.value().GetTopicName().value().c_str();
            // Handle message.
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
    QOS qos = QOS_AT_MOST_ONCE;
    int timeout = 10;

    SubscribeToIoTCoreRequest request;
    request.SetTopicName(topic);
    request.SetQos(qos);
    IoTCoreResponseHandler streamHandler;
    SubscribeToIoTCoreOperation operation = ipcClient.NewSubscribeToIoTCore(streamHandler);
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

## Examples<a name="ipc-iot-core-mqtt-examples"></a>

Use the following examples to learn how to use the AWS IoT Core MQTT IPC service in your components\.

### Example AWS IoT Core MQTT publisher \(C\+\+\)<a name="ipc-iot-core-mqtt-example-publisher-cpp"></a>

The following example recipe allows the component to publish to all topics\.

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.IoTCorePublisherCpp",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "A component that publishes MQTT messages to IoT Core.",
  "ComponentPublisher": "Amazon",
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "accessControl": {
        "aws.greengrass.ipc.mqttproxy": {
          "com.example.IoTCorePublisherCpp:mqttproxy:1": {
            "policyDescription": "Allows access to publish to all topics.",
            "operations": [
              "aws.greengrass#PublishToIoTCore"
            ],
            "resources": [
              "*"
            ]
          }
        }
      }
    }
  },
  "Manifests": [
    {
      "Lifecycle": {
        "Run": "{artifacts:path}/greengrassv2_iotcore_publisher"
      },
      "Artifacts": [
        {
          "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.IoTCorePublisherCpp/1.0.0/greengrassv2_iotcore_publisher",
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
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.IoTCorePublisherCpp
ComponentVersion: 1.0.0
ComponentDescription: A component that publishes MQTT messages to IoT Core.
ComponentPublisher: Amazon
ComponentConfiguration:
  DefaultConfiguration:
    accessControl:
      aws.greengrass.ipc.mqttproxy:
        com.example.IoTCorePublisherCpp:mqttproxy:1:
          policyDescription: Allows access to publish to all topics.
          operations:
            - aws.greengrass#PublishToIoTCore
          resources:
            - "*"
Manifests:
  - Lifecycle:
      Run: "{artifacts:path}/greengrassv2_iotcore_publisher"
    Artifacts:
      - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.IoTCorePublisherCpp/1.0.0/greengrassv2_iotcore_publisher
        Permission:
          Execute: OWNER
```

------

The following example C\+\+ application demonstrates how to use the AWS IoT Core MQTT IPC service to publish messages to AWS IoT Core\.

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
    String message("Hello from the Greengrass IPC MQTT publisher (C++).");
    String topic("test/topic/cpp");
    QOS qos = QOS_AT_LEAST_ONCE;
    int timeout = 10;
    
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

    while (true) {
        PublishToIoTCoreRequest request;
        Vector<uint8_t> messageData({message.begin(), message.end()});
        request.SetTopicName(topic);
        request.SetPayload(messageData);
        request.SetQos(qos);

        PublishToIoTCoreOperation operation = ipcClient.NewPublishToIoTCore();
        auto activate = operation.Activate(request, nullptr);
        activate.wait();

        auto responseFuture = operation.GetResult();
        if (responseFuture.wait_for(std::chrono::seconds(timeout)) == std::future_status::timeout) {
            std::cerr << "Operation timed out while waiting for response from Greengrass Core." << std::endl;
            exit(-1);
        }

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

        std::this_thread::sleep_for(std::chrono::seconds(5));
    }

    return 0;
}
```

### Example AWS IoT Core MQTT subscriber \(C\+\+\)<a name="ipc-iot-core-mqtt-example-subscriber-cpp"></a>

The following example recipe allows the component to subscribe to all topics\.

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.IoTCoreSubscriberCpp",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "A component that subscribes to MQTT messages from IoT Core.",
  "ComponentPublisher": "Amazon",
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "accessControl": {
        "aws.greengrass.ipc.mqttproxy": {
          "com.example.IoTCoreSubscriberCpp:mqttproxy:1": {
            "policyDescription": "Allows access to subscribe to all topics.",
            "operations": [
              "aws.greengrass#SubscribeToIoTCore"
            ],
            "resources": [
              "*"
            ]
          }
        }
      }
    }
  },
  "Manifests": [
    {
      "Lifecycle": {
        "Run": "{artifacts:path}/greengrassv2_iotcore_subscriber"
      },
      "Artifacts": [
        {
          "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.IoTCoreSubscriberCpp/1.0.0/greengrassv2_iotcore_subscriber",
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
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.IoTCoreSubscriberCpp
ComponentVersion: 1.0.0
ComponentDescription: A component that subscribes to MQTT messages from IoT Core.
ComponentPublisher: Amazon
ComponentConfiguration:
  DefaultConfiguration:
    accessControl:
      aws.greengrass.ipc.mqttproxy:
        com.example.IoTCoreSubscriberCpp:mqttproxy:1:
          policyDescription: Allows access to subscribe to all topics.
          operations:
            - aws.greengrass#SubscribeToIoTCore
          resources:
            - "*"
Manifests:
  - Lifecycle:
      Run: "{artifacts:path}/greengrassv2_iotcore_subscriber"
    Artifacts:
      - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.IoTCoreSubscriberCpp/1.0.0/greengrassv2_iotcore_subscriber
        Permission:
          Execute: OWNER
```

------

The following example C\+\+ application demonstrates how to use the AWS IoT Core MQTT IPC service to subscribe to messages from AWS IoT Core\.

```
#include <iostream>

#include <aws/crt/Api.h>
#include <aws/greengrass/GreengrassCoreIpcClient.h>

using namespace Aws::Crt;
using namespace Aws::Greengrass;

class IoTCoreResponseHandler : public SubscribeToIoTCoreStreamHandler {
    void OnStreamEvent(IoTCoreMessage *response) override {
        auto message = response->GetMessage();
        if (message.has_value() && message.value().GetPayload().has_value()) {
            auto messageBytes = message.value().GetPayload().value();
            std::string messageString(messageBytes.begin(), messageBytes.end());
            std::string messageTopic = message.value().GetTopicName().value().c_str();
            std::cout << "Received new message on topic: " << messageTopic << std::endl;
            std::cout << "Message: " << messageString << std::endl;
        }
    }

    bool OnStreamError(OperationError *error) override {
        std::cout << "Received an operation error: ";
        if (error->GetMessage().has_value()) {
            std::cout << error->GetMessage().value();
        }
        std::cout << std::endl;
        return false; // Return true to close stream, false to keep stream open.
    }
    
    void OnStreamClosed() override {
        std::cout << "Subscribe to IoT Core stream closed." << std::endl;
    }
};

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
    String topic("test/topic/cpp");
    QOS qos = QOS_AT_LEAST_ONCE;
    int timeout = 10;
    
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

    SubscribeToIoTCoreRequest request;
    request.SetTopicName(topic);
    request.SetQos(qos);
    IoTCoreResponseHandler streamHandler;
    SubscribeToIoTCoreOperation operation = ipcClient.NewSubscribeToIoTCore(streamHandler);
    auto activate = operation.Activate(request, nullptr);
    activate.wait();

    auto responseFuture = operation.GetResult();
    if (responseFuture.wait_for(std::chrono::seconds(timeout)) == std::future_status::timeout) {
        std::cerr << "Operation timed out while waiting for response from Greengrass Core." << std::endl;
        exit(-1);
    }

    auto response = responseFuture.get();
    if (response) {
        std::cout << "Successfully subscribed to topic: " << topic << std::endl;
    } else {
        // An error occurred.
        std::cout << "Failed to subscribe to topic: " << topic << std::endl;
        auto errorType = response.GetResultType();
        if (errorType == OPERATION_ERROR) {
            auto *error = response.GetOperationError();
            std::cout << "Operation error: " << error->GetMessage().value() << std::endl;
        } else {
            std::cout << "RPC error: " << response.GetRpcError() << std::endl;
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