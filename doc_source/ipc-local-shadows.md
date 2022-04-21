# Interact with local shadows<a name="ipc-local-shadows"></a>

Use the shadow IPC service to interact with local shadows on a device\. The device you choose to interact with can be your core device or a connected client device\. 

Include the [shadow manager component](shadow-manager-component.md) as a dependency in your custom component\. You can then use IPC operations in your custom components to interact with local shadows on your device through the shadow manager\. To enable custom components to react to changes in local shadow states, you can also use the publish/subscribe IPC service to subscribe to shadow events\. For more information about using the publish/subscribe service, see the [Publish/subscribe local messages](ipc-publish-subscribe.md)\.

**Topics**
+ [Minimum SDK versions](#ipc-local-shadows-sdk-versions)
+ [Authorization](#ipc-local-shadow-authorization)
+ [GetThingShadow](#ipc-operation-getthingshadow)
+ [UpdateThingShadow](#ipc-operation-updatethingshadow)
+ [DeleteThingShadow](#ipc-operation-deletethingshadow)
+ [ListNamedShadowsForThing](#ipc-operation-listnamedshadowsforthing)

## Minimum SDK versions<a name="ipc-local-shadows-sdk-versions"></a>

The following table lists the minimum versions of the AWS IoT Device SDK that you must use to interact with local shadows\.


| SDK | Minimum version | 
| --- | --- | 
|  [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2)  |  v1\.4\.0  | 
|  [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2)  |  v1\.6\.0  | 
|  [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2)  |  Linux: v1\.13\.0; Windows: v1\.14\.6  | 

## Authorization<a name="ipc-local-shadow-authorization"></a>

To use the shadow IPC service in a custom component, you must define authorization policies that allow your component to interact with shadows\. For information about defining authorization policies, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies)\.

Authorization policies for shadow interaction have the following properties\.

**IPC service identifier:** `aws.greengrass.ShadowManager`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#GetThingShadow`  |  Allows a component to retrieve the shadow of a thing\.  |  One of the following strings:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/ipc-local-shadows.html)  | 
|  `aws.greengrass#UpdateThingShadow`  |  Allows a component to update the shadow of a thing\.  |  One of the following strings:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/ipc-local-shadows.html)  | 
|  `aws.greengrass#DeleteThingShadow`  |  Allows a component to delete the shadow of a thing\.  |  One of the following strings:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/ipc-local-shadows.html)  | 
|  `aws.greengrass#ListNamedShadowsForThing`  |  Allows a component to retrieve the list of named shadows for a thing\.  |  A thing name string that allows access to the thing to list its shadows\. Use `*` to allow access to things\.  | 

**IPC service identifier:** `aws.greengrass.ipc.pubsub`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#SubscribeToTopic`  |  Allows a component to subscribe to messages for the topics that you specify\.  |  One of the following topic strings: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/ipc-local-shadows.html) The value of the topic prefix `shadowTopicPrefix` depends on the type of shadow:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/ipc-local-shadows.html) Use `*` to allow access to all topics\. This topic string doesn't support MQTT topic wildcards \(`#` and `+`\)\.  | 

**Example authorization policy**  
The following example authorization policy allows the component `com.example.MyShadowInteractionComponent` to interact with the classic device shadow and the named shadow `myNamedShadow` for the device `MyThingName`\. This policy also allows this component to receive messages on local topics for these shadows\.   

```
{
  "accessControl": {
    "aws.greengrass.ShadowManager": {
      "com.example.MyShadowInteractionComponent:shadow:1": {
        "policyDescription": "Allows access to shadows",
        "operations": [
          "aws.greengrass#GetThingShadow",
          "aws.greengrass#UpdateThingShadow",
          "aws.greengrass#DeleteThingShadow"
        ],
        "resources": [
          "$aws/things/MyThingName/shadow",
          "$aws/things/MyThingName/shadow/name/myNamedShadow"
        ]
      },
      "com.example.MyShadowInteractionComponent:shadow:2": {
        "policyDescription": "Allows access to things with shadows",
        "operations": [
          "aws.greengrass#ListNamedShadowsForThing"
        ],
        "resources": [
          "MyThingName"
        ]
      }    
    },
    "aws.greengrass.ipc.pubsub": {
      "com.example.MyShadowInteractionComponent:pubsub:1": {
        "policyDescription": "Allows access to shadow pubsub topics",
        "operations": [
          "aws.greengrass#SubscribeToTopic"
        ],
        "resources": [
          "$aws/things/MyThingName/shadow/get/accepted",
          "$aws/things/MyThingName/shadow/name/myNamedShadow/get/accepted"
        ]
      }
    }
  }
}
```

```
accessControl:
  aws.greengrass.ShadowManager:
    'com.example.MyShadowInteractionComponent:shadow:1':
      policyDescription: 'Allows access to shadows'
      operations:
        - 'aws.greengrass#GetThingShadow'
        - 'aws.greengrass#UpdateThingShadow'
        - 'aws.greengrass#DeleteThingShadow'
      resources:
        - $aws/things/MyThingName/shadow
        - $aws/things/MyThingName/shadow/name/myNamedShadow
    'com.example.MyShadowInteractionComponent:shadow:2':
      policyDescription: 'Allows access to things with shadows'
      operations:
        - 'aws.greengrass#ListNamedShadowsForThing'
      resources:
        - MyThingName
  aws.greengrass.ipc.pubsub:
    'com.example.MyShadowInteractionComponent:pubsub:1':
      policyDescription: 'Allows access to shadow pubsub topics'
      operations:
        - 'aws.greengrass#SubscribeToTopic'
      resources:
        - $aws/things/MyThingName/shadow/get/accepted
        - $aws/things/MyThingName/shadow/name/myNamedShadow/get/accepted
```

## GetThingShadow<a name="ipc-operation-getthingshadow"></a>

Get the shadow for a specified thing\.

### Request<a name="ipc-operation-getthingshadow-request"></a>

This operation's request has the following parameters:

`thingName` \(Python: `thing_name`\)  <a name="ipc-local-shadows-thing-name"></a>
The name of the thing\.  
Type: `string`

`shadowName` \(Python: `shadow_name`\)  <a name="ipc-local-shadows-shadow-name"></a>
The name of the shadow\. To specify the thing's classic shadow, set this parameter to an empty string \(`""`\)\.  
The AWS IoT Greengrass service uses the `AWSManagedGreengrassV2Deployment` named shadow to manage deployments that target individual core devices\. This named shadow is reserved for use by the AWS IoT Greengrass service\. Do not update or delete this named shadow\.
Type: `string`

### Response<a name="ipc-operation-getthingshadow-response"></a>

This operation's response has the following information:

`payload`  
The response state document as a blob\.  
Type: `object` that contains the following information:    
`state`  
The state information\.  
This object contains the following information\.    
`desired`  
The state properties and values requested to be updated in the device\.  
Type: `map` of key\-value pairs  
`reported`  
The state properties and values reported by the device\.  
Type: `map` of key\-value pairs  
`delta`  
The difference between the desired and reported state properties and values\. This property is present only if the `desired` and `reported` states are different\.  
Type: `map` of key\-value pairs  
`metadata`  
The timestamps for each attribute in the `desired` and `reported` sections so that you can determine when the state was updated\.   
Type: `string`  
`timestamp`  
The epoch date and time that the response was generated\.  
Type: `integer`  
`clientToken` \(Python: `clientToken`\)  
The token that is used to match the request and corresponding response  
Type: `string`  
`version`  
The version of the local shadow document\.  
Type: `integer`

### Errors<a name="ipc-operation-getthingshadow-errors"></a>

This operation can return the following errors\.

`InvalidArgumentsError`  <a name="ipc-invalidargumentserror"></a>
<a name="ipc-invalidargumentserror-para"></a>The local shadow service is unable to validate the request parameters\. This can occur if the request contains malformed JSON or unsupported characters\. 

`ResourceNotFoundError`  <a name="ipc-resourcenotfounderror"></a>
The requested local shadow document can't be found\.

`ServiceError`  <a name="ipc-serviceerror"></a>
An internal service error occurred, or the number of requests to the IPC service exceeded the limits specified in the `maxLocalRequestsPerSecondPerThing` and `maxTotalLocalRequestsRate` configuration parameters in the shadow manager component\.

`UnauthorizedError`  <a name="ipc-unauthorizederror"></a>
The component's authorization policy doesn't include required permissions for this operation\.

### Examples<a name="ipc-operation-getthingshadow-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Get a thing shadow**  
This example uses an `IPCUtils` class to create a connection to the AWS IoT Greengrass Core IPC service\. For more information, see [Connect to the AWS IoT Greengrass Core IPC service](interprocess-communication.md#ipc-service-connect)\.

```
package com.aws.greengrass.docs.samples.ipc;

import com.aws.greengrass.docs.samples.ipc.util.IPCUtils;
import software.amazon.awssdk.aws.greengrass.GetThingShadowResponseHandler;
import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.model.GetThingShadowRequest;
import software.amazon.awssdk.aws.greengrass.model.GetThingShadowResponse;
import software.amazon.awssdk.aws.greengrass.model.ResourceNotFoundError;
import software.amazon.awssdk.aws.greengrass.model.UnauthorizedError;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;

import java.nio.charset.StandardCharsets;
import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

public class GetThingShadow {

    public static final int TIMEOUT_SECONDS = 10;

    public static void main(String[] args) {
        // Use the current core device's name if thing name isn't set.
        String thingName = args[0].isEmpty() ? System.getenv("AWS_IOT_THING_NAME") : args[0];
        String shadowName = args[1];
        try (EventStreamRPCConnection eventStreamRPCConnection =
                     IPCUtils.getEventStreamRpcConnection()) {
            GreengrassCoreIPCClient ipcClient =
                    new GreengrassCoreIPCClient(eventStreamRPCConnection);
            GetThingShadowResponseHandler responseHandler =
                    GetThingShadow.getThingShadow(ipcClient, thingName, shadowName);
            CompletableFuture<GetThingShadowResponse> futureResponse =
                    responseHandler.getResponse();
            try {
                GetThingShadowResponse response = futureResponse.get(TIMEOUT_SECONDS,
                        TimeUnit.SECONDS);
                String shadowPayload = new String(response.getPayload(), StandardCharsets.UTF_8);
                System.out.printf("Successfully got shadow %s/%s: %s%n", thingName, shadowName,
                        shadowPayload);
            } catch (TimeoutException e) {
                System.err.printf("Timeout occurred while getting shadow: %s/%s%n", thingName,
                        shadowName);
            } catch (ExecutionException e) {
                if (e.getCause() instanceof UnauthorizedError) {
                    System.err.printf("Unauthorized error while getting shadow: %s/%s%n",
                            thingName, shadowName);
                } else if (e.getCause() instanceof ResourceNotFoundError) {
                    System.err.printf("Unable to find shadow to get: %s/%s%n", thingName,
                            shadowName);
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

    public static GetThingShadowResponseHandler getThingShadow(GreengrassCoreIPCClient greengrassCoreIPCClient, String thingName, String shadowName) {
        GetThingShadowRequest getThingShadowRequest = new GetThingShadowRequest();
        getThingShadowRequest.setThingName(thingName);
        getThingShadowRequest.setShadowName(shadowName);
        return greengrassCoreIPCClient.getThingShadow(getThingShadowRequest, Optional.empty());
    }
}
```

------
#### [ Python ]

**Example: Get a thing shadow**  

```
import awsiot.greengrasscoreipc
import awsiot.greengrasscoreipc.client as client
from awsiot.greengrasscoreipc.model import GetThingShadowRequest

TIMEOUT = 10

def sample_get_thing_shadow_request(thingName, shadowName):
    try:
        # set up IPC client to connect to the IPC server
        ipc_client = awsiot.greengrasscoreipc.connect()
                
        # create the GetThingShadow request
        get_thing_shadow_request = GetThingShadowRequest()
        get_thing_shadow_request.thing_name = thingName
        get_thing_shadow_request.shadow_name = shadowName
        
        # retrieve the GetThingShadow response after sending the request to the IPC server
        op = ipc_client.new_get_thing_shadow()
        op.activate(get_thing_shadow_request)
        fut = op.get_response()
        
        result = fut.result(TIMEOUT)
        return result.payload
        
    except InvalidArgumentsError as e:
        # add error handling
        ...
    # except ResourceNotFoundError | UnauthorizedError | ServiceError
```

------

## UpdateThingShadow<a name="ipc-operation-updatethingshadow"></a>

Update the shadow for the specified thing\.

### Request<a name="ipc-operation-updatethingshadow-request"></a>

This operation's request has the following parameters:

`thingName` \(Python: `thing_name`\)  <a name="ipc-local-shadows-thing-name"></a>
The name of the thing\.  
Type: `string`

`shadowName` \(Python: `shadow_name`\)  <a name="ipc-local-shadows-shadow-name"></a>
The name of the shadow\. To specify the thing's classic shadow, set this parameter to an empty string \(`""`\)\.  
The AWS IoT Greengrass service uses the `AWSManagedGreengrassV2Deployment` named shadow to manage deployments that target individual core devices\. This named shadow is reserved for use by the AWS IoT Greengrass service\. Do not update or delete this named shadow\.
Type: `string`

`payload`  
The request state document as a blob\.  
Type: `object` that contains the following information:    
`state`  
The state information to update\. This IPC operation affects only the specified fields\.  
This object contains the following information\. Typically, you'll use either the `desired` property or the `reported` property, but not both in the same request\.    
`desired`  
The state properties and values requested to be updated in the device\.  
Type: `map` of key\-value pairs  
`reported`  
The state properties and values reported by the device\.  
Type: `map` of key\-value pairs  
`clientToken` \(Python: `client_token`\)  
\(Optional\) The token that is used to match the request and corresponding response by the client token\.  
Type: `string`  
`version`  
\(Optional\) The version of the local shadow document to update\. The shadow service processes the update only if the specified version matches the latest version that it has\.  
Type: `integer`

### Response<a name="ipc-operation-updatethingshadow-response"></a>

This operation's response has the following information:

`payload`  
The response state document as a blob\.  
Type: `object` that contains the following information:    
`state`  
The state information\.  
This object contains the following information\.    
`desired`  
The state properties and values requested to be updated in the device\.  
Type: `map` of key\-value pairs  
`reported`  
The state properties and values reported by the device\.  
Type: `map` of key\-value pairs  
`delta`  
The state properties and values reported by the device\.  
Type: `map` of key\-value pairs  
`metadata`  
The timestamps for each attribute in the `desired` and `reported` sections so that you can determine when the state was updated\.   
Type: `string`  
`timestamp`  
The epoch date and time that the response was generated\.  
Type: `integer`  
`clientToken` \(Python: `client_token`\)  
The token that is used to match the request and corresponding response\.  
Type: `string`  
`version`  
The version of local shadow document after the update is complete\.  
Type: `integer`

### Errors<a name="ipc-operation-updatethingshadow-errors"></a>

This operation can return the following errors\.

`ConflictError`  
The local shadow service encountered a version conflict during the update operation\. This occurs when the version in the request payload doesn't match the version in the latest available local shadow document\.

`InvalidArgumentsError`  
<a name="ipc-invalidargumentserror-para"></a>The local shadow service is unable to validate the request parameters\. This can occur if the request contains malformed JSON or unsupported characters\.   
A valid `payload` has the following properties:  
+ The `state` node exists, and is an object that contains the `desired` or `reported` state information\. 
+ The `desired` and `reported` nodes are either objects or null\. At least one of these objects must contain valid state information\. 
+ The depth of the `desired` and `reported` objects can't exceed eight nodes\. 
+ The length of the `clientToken` value can't exceed 64 characters\. 
+  The `version` value must be `1` or higher\. 

`ServiceError`  <a name="ipc-serviceerror"></a>
An internal service error occurred, or the number of requests to the IPC service exceeded the limits specified in the `maxLocalRequestsPerSecondPerThing` and `maxTotalLocalRequestsRate` configuration parameters in the shadow manager component\.

`UnauthorizedError`  <a name="ipc-unauthorizederror"></a>
The component's authorization policy doesn't include required permissions for this operation\.

### Examples<a name="ipc-operation-updatethingshadow-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Update a thing shadow**  
This example uses an `IPCUtils` class to create a connection to the AWS IoT Greengrass Core IPC service\. For more information, see [Connect to the AWS IoT Greengrass Core IPC service](interprocess-communication.md#ipc-service-connect)\.

```
package com.aws.greengrass.docs.samples.ipc;

import com.aws.greengrass.docs.samples.ipc.util.IPCUtils;
import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.UpdateThingShadowResponseHandler;
import software.amazon.awssdk.aws.greengrass.model.UnauthorizedError;
import software.amazon.awssdk.aws.greengrass.model.UpdateThingShadowRequest;
import software.amazon.awssdk.aws.greengrass.model.UpdateThingShadowResponse;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;

import java.nio.charset.StandardCharsets;
import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

public class UpdateThingShadow {

    public static final int TIMEOUT_SECONDS = 10;

    public static void main(String[] args) {
        // Use the current core device's name if thing name isn't set.
        String thingName = args[0].isEmpty() ? System.getenv("AWS_IOT_THING_NAME") : args[0];
        String shadowName = args[1];
        byte[] shadowPayload = args[2].getBytes(StandardCharsets.UTF_8);
        try (EventStreamRPCConnection eventStreamRPCConnection =
                     IPCUtils.getEventStreamRpcConnection()) {
            GreengrassCoreIPCClient ipcClient =
                    new GreengrassCoreIPCClient(eventStreamRPCConnection);
            UpdateThingShadowResponseHandler responseHandler =
                    UpdateThingShadow.updateThingShadow(ipcClient, thingName, shadowName,
                            shadowPayload);
            CompletableFuture<UpdateThingShadowResponse> futureResponse =
                    responseHandler.getResponse();
            try {
                futureResponse.get(TIMEOUT_SECONDS, TimeUnit.SECONDS);
                System.out.printf("Successfully updated shadow: %s/%s%n", thingName, shadowName);
            } catch (TimeoutException e) {
                System.err.printf("Timeout occurred while updating shadow: %s/%s%n", thingName,
                        shadowName);
            } catch (ExecutionException e) {
                if (e.getCause() instanceof UnauthorizedError) {
                    System.err.printf("Unauthorized error while updating shadow: %s/%s%n",
                            thingName, shadowName);
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

    public static UpdateThingShadowResponseHandler updateThingShadow(GreengrassCoreIPCClient greengrassCoreIPCClient, String thingName, String shadowName, byte[] shadowPayload) {
        UpdateThingShadowRequest updateThingShadowRequest = new UpdateThingShadowRequest();
        updateThingShadowRequest.setThingName(thingName);
        updateThingShadowRequest.setShadowName(shadowName);
        updateThingShadowRequest.setPayload(shadowPayload);
        return greengrassCoreIPCClient.updateThingShadow(updateThingShadowRequest,
                Optional.empty());
    }
}
```

------
#### [ Python ]

**Example: Update a thing shadow**  

```
import awsiot.greengrasscoreipc
import awsiot.greengrasscoreipc.client as client
from awsiot.greengrasscoreipc.model import UpdateThingShadowRequest

TIMEOUT = 10

def sample_update_thing_shadow_request(thingName, shadowName, payload):
    try:
        # set up IPC client to connect to the IPC server
        ipc_client = awsiot.greengrasscoreipc.connect()
                
        # create the UpdateThingShadow request
        update_thing_shadow_request = UpdateThingShadowRequest()
        update_thing_shadow_request.thing_name = thingName
        update_thing_shadow_request.shadow_name = shadowName
        update_thing_shadow_request.payload = payload
                        
        # retrieve the UpdateThingShadow response after sending the request to the IPC server
        op = ipc_client.new_update_thing_shadow()
        op.activate(update_thing_shadow_request)
        fut = op.get_response()
        
        result = fut.result(TIMEOUT)
        return result.payload
        
    except InvalidArgumentsError as e:
        # add error handling
    ...
    # except ConflictError | UnauthorizedError | ServiceError
```

------

## DeleteThingShadow<a name="ipc-operation-deletethingshadow"></a>

Deletes the shadow for the specified thing\. 

Beginning in shadow manager v2\.0\.4, deleting a shadow increments the version number\. For example, when you delete the shadow `MyThingShadow` at version 1, the version of the deleted shadow is 2\. If you then recreate a shadow with the name `MyThingShadow`, the version for that shadow is 3\. 

### Request<a name="ipc-operation-deletethingshadow-request"></a>

This operation's request has the following parameters:

`thingName` \(Python: `thing_name`\)  <a name="ipc-local-shadows-thing-name"></a>
The name of the thing\.  
Type: `string`

`shadowName` \(Python: `shadow_name`\)  <a name="ipc-local-shadows-shadow-name"></a>
The name of the shadow\. To specify the thing's classic shadow, set this parameter to an empty string \(`""`\)\.  
The AWS IoT Greengrass service uses the `AWSManagedGreengrassV2Deployment` named shadow to manage deployments that target individual core devices\. This named shadow is reserved for use by the AWS IoT Greengrass service\. Do not update or delete this named shadow\.
Type: `string`

### Response<a name="ipc-operation-deletethingshadow-response"></a>

This operation's response has the following information:

`payload`  
An empty response state document\.

### Errors<a name="ipc-operation-deletethingshadow-errors"></a>

This operation can return the following errors\.

`InvalidArgumentsError`  <a name="ipc-invalidargumentserror"></a>
<a name="ipc-invalidargumentserror-para"></a>The local shadow service is unable to validate the request parameters\. This can occur if the request contains malformed JSON or unsupported characters\. 

`ResourceNotFoundError`  <a name="ipc-resourcenotfounderror"></a>
The requested local shadow document can't be found\.

`ServiceError`  <a name="ipc-serviceerror"></a>
An internal service error occurred, or the number of requests to the IPC service exceeded the limits specified in the `maxLocalRequestsPerSecondPerThing` and `maxTotalLocalRequestsRate` configuration parameters in the shadow manager component\.

`UnauthorizedError`  <a name="ipc-unauthorizederror"></a>
The component's authorization policy doesn't include required permissions for this operation\.

### Examples<a name="ipc-operation-deletethingshadow-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Delete a thing shadow**  
This example uses an `IPCUtils` class to create a connection to the AWS IoT Greengrass Core IPC service\. For more information, see [Connect to the AWS IoT Greengrass Core IPC service](interprocess-communication.md#ipc-service-connect)\.

```
package com.aws.greengrass.docs.samples.ipc;

import com.aws.greengrass.docs.samples.ipc.util.IPCUtils;
import software.amazon.awssdk.aws.greengrass.DeleteThingShadowResponseHandler;
import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.model.DeleteThingShadowRequest;
import software.amazon.awssdk.aws.greengrass.model.DeleteThingShadowResponse;
import software.amazon.awssdk.aws.greengrass.model.ResourceNotFoundError;
import software.amazon.awssdk.aws.greengrass.model.UnauthorizedError;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;

import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

public class DeleteThingShadow {

    public static final int TIMEOUT_SECONDS = 10;

    public static void main(String[] args) {
        // Use the current core device's name if thing name isn't set.
        String thingName = args[0].isEmpty() ? System.getenv("AWS_IOT_THING_NAME") : args[0];
        String shadowName = args[1];
        try (EventStreamRPCConnection eventStreamRPCConnection =
                     IPCUtils.getEventStreamRpcConnection()) {
            GreengrassCoreIPCClient ipcClient =
                    new GreengrassCoreIPCClient(eventStreamRPCConnection);
            DeleteThingShadowResponseHandler responseHandler =
                    DeleteThingShadow.deleteThingShadow(ipcClient, thingName, shadowName);
            CompletableFuture<DeleteThingShadowResponse> futureResponse =
                    responseHandler.getResponse();
            try {
                futureResponse.get(TIMEOUT_SECONDS, TimeUnit.SECONDS);
                System.out.printf("Successfully deleted shadow: %s/%s%n", thingName, shadowName);
            } catch (TimeoutException e) {
                System.err.printf("Timeout occurred while deleting shadow: %s/%s%n", thingName,
                        shadowName);
            } catch (ExecutionException e) {
                if (e.getCause() instanceof UnauthorizedError) {
                    System.err.printf("Unauthorized error while deleting shadow: %s/%s%n",
                            thingName, shadowName);
                } else if (e.getCause() instanceof ResourceNotFoundError) {
                    System.err.printf("Unable to find shadow to delete: %s/%s%n", thingName,
                            shadowName);
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

    public static DeleteThingShadowResponseHandler deleteThingShadow(GreengrassCoreIPCClient greengrassCoreIPCClient, String thingName, String shadowName) {
        DeleteThingShadowRequest deleteThingShadowRequest = new DeleteThingShadowRequest();
        deleteThingShadowRequest.setThingName(thingName);
        deleteThingShadowRequest.setShadowName(shadowName);
        return greengrassCoreIPCClient.deleteThingShadow(deleteThingShadowRequest,
                Optional.empty());
    }
}
```

------
#### [ Python ]

**Example: Delete a thing shadow**  

```
import awsiot.greengrasscoreipc
import awsiot.greengrasscoreipc.client as client
from awsiot.greengrasscoreipc.model import DeleteThingShadowRequest

TIMEOUT = 10

def sample_delete_thing_shadow_request(thingName, shadowName):
    try:
        # set up IPC client to connect to the IPC server
        ipc_client = awsiot.greengrasscoreipc.connect()
                
        # create the DeleteThingShadow request
        delete_thing_shadow_request = DeleteThingShadowRequest()
        delete_thing_shadow_request.thing_name = thingName
        delete_thing_shadow_request.shadow_name = shadowName
                        
        # retrieve the DeleteThingShadow response after sending the request to the IPC server
        op = ipc_client.new_delete_thing_shadow()
        op.activate(delete_thing_shadow_request)
        fut = op.get_response()
        
        result = fut.result(TIMEOUT)
        return result.payload
        
    except InvalidArgumentsError as e:
        # add error handling
    ...
    # except ResourceNotFoundError | UnauthorizedError | ServiceError
```

------

## ListNamedShadowsForThing<a name="ipc-operation-listnamedshadowsforthing"></a>

List the named shadows for the specified thing\.

### Request<a name="ipc-operation-listnamedshadowsforthing-request"></a>

This operation's request has the following parameters:

`thingName` \(Python: `thing_name`\)  <a name="ipc-local-shadows-thing-name"></a>
The name of the thing\.  
Type: `string`

`pageSize` \(Python: `page_size`\)  
\(Optional\) The number of shadow names to return in each call\.   
Type: `integer`  
Default: 25  
Maximum: 100

`nextToken` \(Python: `next_token`\)  
\(Optional\) The token to retrieve the next set of results\. This value is returned on paged results and is used in the call that returns the next page\.  
Type: `string`

### Response<a name="ipc-operation-listnamedshadowsforthing-response"></a>

This operation's response has the following information:

`results`  
The list of shadow names\.   
Type: `array`

`timestamp`  
\(Optional\) The date and time that the response was generated\.   
Type: `integer`

`nextToken` \(Python: `next_token`\)  
\(Optional\) The token value to use in paged requests to retrieve the next page in the sequence\. This token isn't present when there are no more shadow names to return\.  
Type: `string`  
If the requested page size exactly matches the number of shadow names in the response, then this token is present; however, when used, it returns an empty list\.

### Errors<a name="ipc-operation-listnamedshadowsforthing-errors"></a>

This operation can return the following errors\.

`InvalidArgumentsError`  <a name="ipc-invalidargumentserror"></a>
<a name="ipc-invalidargumentserror-para"></a>The local shadow service is unable to validate the request parameters\. This can occur if the request contains malformed JSON or unsupported characters\. 

`ResourceNotFoundError`  <a name="ipc-resourcenotfounderror"></a>
The requested local shadow document can't be found\.

`ServiceError`  <a name="ipc-serviceerror"></a>
An internal service error occurred, or the number of requests to the IPC service exceeded the limits specified in the `maxLocalRequestsPerSecondPerThing` and `maxTotalLocalRequestsRate` configuration parameters in the shadow manager component\.

`UnauthorizedError`  <a name="ipc-unauthorizederror"></a>
The component's authorization policy doesn't include required permissions for this operation\.

### Examples<a name="ipc-operation-listnamedshadowsforthing-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: List a thing's named shadows**  
This example uses an `IPCUtils` class to create a connection to the AWS IoT Greengrass Core IPC service\. For more information, see [Connect to the AWS IoT Greengrass Core IPC service](interprocess-communication.md#ipc-service-connect)\.

```
package com.aws.greengrass.docs.samples.ipc;

import com.aws.greengrass.docs.samples.ipc.util.IPCUtils;
import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.ListNamedShadowsForThingResponseHandler;
import software.amazon.awssdk.aws.greengrass.model.ListNamedShadowsForThingRequest;
import software.amazon.awssdk.aws.greengrass.model.ListNamedShadowsForThingResponse;
import software.amazon.awssdk.aws.greengrass.model.ResourceNotFoundError;
import software.amazon.awssdk.aws.greengrass.model.UnauthorizedError;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

public class ListNamedShadowsForThing {

    public static final int TIMEOUT_SECONDS = 10;

    public static void main(String[] args) {
        // Use the current core device's name if thing name isn't set.
        String thingName = args[0].isEmpty() ? System.getenv("AWS_IOT_THING_NAME") : args[0];
        try (EventStreamRPCConnection eventStreamRPCConnection =
                     IPCUtils.getEventStreamRpcConnection()) {
            GreengrassCoreIPCClient ipcClient =
                    new GreengrassCoreIPCClient(eventStreamRPCConnection);
            List<String> namedShadows = new ArrayList<>();
            String nextToken = null;
            try {
                // Send additional requests until there's no pagination token in the response.
                do {
                    ListNamedShadowsForThingResponseHandler responseHandler =
                            ListNamedShadowsForThing.listNamedShadowsForThing(ipcClient, thingName,
                                    nextToken, 25);
                    CompletableFuture<ListNamedShadowsForThingResponse> futureResponse =
                            responseHandler.getResponse();
                    ListNamedShadowsForThingResponse response =
                            futureResponse.get(TIMEOUT_SECONDS, TimeUnit.SECONDS);
                    List<String> responseNamedShadows = response.getResults();
                    namedShadows.addAll(responseNamedShadows);
                    nextToken = response.getNextToken();
                } while (nextToken != null);
                System.out.printf("Successfully got named shadows for thing %s: %s%n", thingName,
                        String.join(",", namedShadows));
            } catch (TimeoutException e) {
                System.err.println("Timeout occurred while listing named shadows for thing: " + thingName);
            } catch (ExecutionException e) {
                if (e.getCause() instanceof UnauthorizedError) {
                    System.err.println("Unauthorized error while listing named shadows for " +
                            "thing: " + thingName);
                } else if (e.getCause() instanceof ResourceNotFoundError) {
                    System.err.println("Unable to find thing to list named shadows: " + thingName);
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

    public static ListNamedShadowsForThingResponseHandler listNamedShadowsForThing(GreengrassCoreIPCClient greengrassCoreIPCClient, String thingName, String nextToken, int pageSize) {
        ListNamedShadowsForThingRequest listNamedShadowsForThingRequest =
                new ListNamedShadowsForThingRequest();
        listNamedShadowsForThingRequest.setThingName(thingName);
        listNamedShadowsForThingRequest.setNextToken(nextToken);
        listNamedShadowsForThingRequest.setPageSize(pageSize);
        return greengrassCoreIPCClient.listNamedShadowsForThing(listNamedShadowsForThingRequest,
                Optional.empty());
    }
}
```

------
#### [ Python ]

**Example: List a thing's named shadows**  

```
import awsiot.greengrasscoreipc
import awsiot.greengrasscoreipc.client as client
from awsiot.greengrasscoreipc.model import ListNamedShadowsForThingRequest

TIMEOUT = 10

def sample_list_named_shadows_for_thing_request(thingName, nextToken, pageSize):
    try:
        # set up IPC client to connect to the IPC server
        ipc_client = awsiot.greengrasscoreipc.connect()
                
        # create the ListNamedShadowsForThingRequest request
        list_named_shadows_for_thing_request = ListNamedShadowsForThingRequest()
        list_named_shadows_for_thing_request.thing_name = thingName
        list_named_shadows_for_thing_request.next_token = nextToken
        list_named_shadows_for_thing_request.page_size = pageSize
        
        # retrieve the ListNamedShadowsForThingRequest response after sending the request to the IPC server
        op = ipc_client.new_list_named_shadows_for_thing()
        op.activate(list_named_shadows_for_thing_request)
        fut = op.get_response()
        
        list_result = fut.result(TIMEOUT)
        
        # additional returned fields
        timestamp = list_result.timestamp
        next_token = result.next_token
        named_shadow_list = list_result.results
        
        return named_shadow_list, next_token, timestamp
                
    except InvalidArgumentsError as e:
        # add error handling
    ...
    # except ResourceNotFoundError | UnauthorizedError | ServiceError
```

------