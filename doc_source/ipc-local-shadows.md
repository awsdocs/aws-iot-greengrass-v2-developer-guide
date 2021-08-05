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
|  [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2)  |  v1\.13\.0  | 

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
      "policyId1": {
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
      "policyId2": {
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
    policyId1:
      policyDescription: 'Allows access to shadows'
      operations:
        - 'aws.greengrass#GetThingShadow'
        - 'aws.greengrass#UpdateThingShadow'
        - 'aws.greengrass#DeleteThingShadow'
      resources:
        - $aws/things/MyThingName/shadow
        - $aws/things/MyThingName/shadow/name/myNamedShadow
    policyId2:
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

`thingName`  
The name of the thing for which to get the shadow\.   
Type: `string`

`shadowName`  
\(Optional\) The name of the shadow to get\. This parameter is required only for named shadows\.  
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
`clientToken`  
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

```
byte[] sampleGetThingShadowRequest(String thingName, String shadowName) {
    try {
        // set up IPC client to connect to the IPC server
        EventStreamRPCConnection eventStreamRpcConnection = IPCUtils.getEventStreamRpcConnection();
        GreengrassCoreIPCClient greengrassCoreIPCClient = new GreengrassCoreIPCClient(eventStreamRpcConnection);
        
        // create the GetThingShadow request
        GetThingShadowRequest getThingShadowRequest = new GetThingShadowRequest();
        getThingShadowRequest.setThingName(thingName);
        getThingShadowRequest.setShadowName(shadowName);
        
        // retrieve the GetThingShadow response after sending the request to the IPC server
        GetThingShadowResponse getThingShadowResponse =
                greengrassCoreIPCClient.getThingShadow(getThingShadowRequest, Optional.empty())
                .getResponse()
                .get();
        
        byte[] payload = getThingShadowResponse.getPayload();
        return payload;
        
    catch (ExecutionException e) {
        if (e.getCause() instanceof InvalidArgumentsError) {
        // add error handling
        }
        ...
    // catch ResourceNotFoundError | UnauthorizedError | ServiceError
    }
}
```

------
#### [ Python ]

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

`thingName`  
The name of the thing for which to update the shadow\.   
Type: `string`

`shadowName`  
\(Optional\) The name of the shadow to update\. This parameter is required only for named shadows\.  
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
`clientToken`  
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
`clientToken`  
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

```
byte[] sampleUpdateThingShadowRequest(String thingName, String shadowName, byte[] updateDocument) {
    try {
        // set up IPC client to connect to the IPC server
        EventStreamRPCConnection eventStreamRpcConnection = IPCUtils.getEventStreamRpcConnection();
        GreengrassCoreIPCClient greengrassCoreIPCClient = new GreengrassCoreIPCClient(eventStreamRpcConnection);
        
        // create the UpdateThingShadow request
        UpdateThingShadowRequest updateThingShadowRequest = new UpdateThingShadowRequest();
        updateThingShadowRequest.setThingName(thingName);
        updateThingShadowRequest.setShadowName(shadowName);
        updateThingShadowRequest.setPayload(updateDocument);
        
        // retrieve the UpdateThingShadow response after sending the request to the IPC server
        UpdateThingShadowRespone updateThingShadowResponse =
                greengrassCoreIPCClient.updateThingShadow(updateThingShadowRequest, Optional.empty())
                .getResponse()
                .get();
        
        byte[] payload = updateThingShadowResponse.getPayload();
        return payload;
        
    catch (ExecutionException e) {
        if (e.getCause() instanceof InvalidArgumentsError) {
        // add error handling
        }
        ...
    // catch ConflictError | UnauthorizedError | ServiceError 
    }
}
```

------
#### [ Python ]

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

### Request<a name="ipc-operation-deletethingshadow-request"></a>

This operation's request has the following parameters:

`thingName`  
The name of the thing for which to delete the shadow\.   
Type: `string`

`shadowName`  
\(Optional\) The name of the shadow to delete\. This parameter is required only for named shadows\.  
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

```
byte[] sampleDeleteThingShadowRequest(String thingName, String shadowName) {
    try {
        // set up IPC client to connect to the IPC server
        EventStreamRPCConnection eventStreamRpcConnection = IPCUtils.getEventStreamRpcConnection();
        GreengrassCoreIPCClient greengrassCoreIPCClient = new GreengrassCoreIPCClient(eventStreamRpcConnection);
        
        // create the DeleteThingShadow request
        DeleteThingShadowRequest deleteThingShadowRequest = new DeleteThingShadowRequest();
        deleteThingShadowRequest.setThingName(thingName);
        deleteThingShadowRequest.setShadowName(shadowName);
        
        // retrieve the DeleteThingShadow response after sending the request to the IPC server
        DeleteThingShadowResponse deleteThingShadowResponse =
                greengrassCoreIPCClient.deleteThingShadow(deleteThingShadowRequest, Optional.empty())
                .getResponse()
                .get();
        
        byte[] payload = deleteThingShadowResponse.getPayload();
        return payload;
        
    catch (ExecutionException e) {
        if (e.getCause() instanceof InvalidArgumentsError) {
        // add error handling
        }
        ...
    // catch ResourceNotFoundError | UnauthorizedError | ServiceError
    }
}
```

------
#### [ Python ]

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

`thingName`  
The name of the thing for which to list the named shadows\.   
Type: `string`

`pageSize`  
\(Optional\) The number of shadow names to return in each call\.   
Type: `integer`  
Default: 25  
Maximum: 100

`nextToken`  
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

`nextToken`  
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

```
List<String> sampleListNamedShadowsForThingRequest(String thingName, String nextToken, int pageSize) {
    try {
        // set up IPC client to connect to the IPC server
        EventStreamRPCConnection eventStreamRpcConnection = IPCUtils.getEventStreamRpcConnection();
        GreengrassCoreIPCClient greengrassCoreIPCClient = new GreengrassCoreIPCClient(eventStreamRpcConnection);
        
        // create the ListNamedShadowsForThing request
        ListNamedShadowsForThingRequest request = new ListNamedShadowsForThingRequest();
        request.setThingName(thingName);
        request.setNextToken(nextToken);
        request.setPageSize(pageSize);
        
        // retrieve the ListNamedShadowsForThing response after sending the request to the IPC server
        ListThingShadowsForThingResponse response =
                greengrassCoreIPCClient.listNamedShadowsForThing(request, Optional.empty())
                .getResponse()
                .get();
        
        List<String> listOfNamedShadows = response.getResults().get()
        // pagination token used to get next set of data
        // null indicates that all named shadows were received
        String tokenForNextQuery = response.getNextToken().get()
        return listOfNamedShadows;
        
    catch (ExecutionException e) {
        if (e.getCause() instanceof InvalidArgumentsError) {
        // add error handling
        }
        ...
    // catch ResourceNotFoundError | UnauthorizedError | ServiceError
    }
}
```

------
#### [ Python ]

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