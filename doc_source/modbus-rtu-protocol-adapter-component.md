# Modbus\-RTU protocol adapter<a name="modbus-rtu-protocol-adapter-component"></a>

The Modbus\-RTU protocol adapter component \(`aws.greengrass.Modbus`\) polls information from local Modbus RTU devices\.

To request information from a local Modbus RTU device with this component, publish a message to the topic where this component subscribes\. In the message, specify the Modbus RTU request to send to a device\. Then, this component publishes a response that contains the result of the Modbus RTU request\.

**Note**  
This component provides similar functionality to the Modbus RTU protocol adapter connector in AWS IoT Greengrass V1\. For more information, see [Modbus RTU protocol adapter connector](https://docs.aws.amazon.com/greengrass/latest/developerguide/modbus-protocol-adapter-connector.html) in the *AWS IoT Greengrass V1 Developer Guide*\.

**Topics**
+ [Versions](#modbus-rtu-protocol-adapter-component-versions)
+ [Type](#modbus-rtu-protocol-adapter-component-type)
+ [Requirements](#modbus-rtu-protocol-adapter-component-requirements)
+ [Dependencies](#modbus-rtu-protocol-adapter-component-dependencies)
+ [Configuration](#modbus-rtu-protocol-adapter-component-configuration)
+ [Input data](#modbus-rtu-protocol-adapter-component-input-data)
+ [Output data](#modbus-rtu-protocol-adapter-component-output-data)
+ [Modbus RTU requests and responses](#modbus-rtu-protocol-adapter-component-requests-responses)
+ [Local log file](#modbus-rtu-protocol-adapter-component-log-file)
+ [Licenses](#modbus-rtu-protocol-adapter-component-licenses)
+ [Changelog](#modbus-rtu-protocol-adapter-component-changelog)

## Versions<a name="modbus-rtu-protocol-adapter-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="modbus-rtu-protocol-adapter-component-type"></a>

<a name="public-component-type-lambda"></a>This component is a Lambda component \(`aws.greengrass.lambda`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component's Lambda function using the [Lambda launcher component](lambda-launcher-component.md)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](manage-components.md#component-types)\.

## Requirements<a name="modbus-rtu-protocol-adapter-component-requirements"></a>

This component has the following requirements:
+ <a name="core-device-lambda-function-requirements"></a>Your core device must meet the requirements to run Lambda functions\. If you want the core device to run containerized Lambda functions, the device must meet the requirements to do so\. For more information, see [Requirements to run Lambda functions](setting-up.md#greengrass-v2-lambda-requirements)\.
+ <a name="public-component-python3-requirement"></a>[Python](https://www.python.org/) version 3\.7 installed on the core device and added to the PATH environment variable\.
+ A physical connection between the AWS IoT Greengrass core device and the Modbus devices\. The core device must be physically connected to the Modbus RTU network through a serial port, such as a USB port\.
+ <a name="connector-component-legacy-subscription-router-dependency"></a>To receive output data from this component, you must merge the following configuration update for the [legacy subscription router component](legacy-subscription-router-component.md) when you deploy this component\. The legacy subscription router component \(`aws.greengrass.LegacySubscriptionRouter`\) is a dependency of this component\. This configuration specifies the topic where this component publishes responses\.

------
#### [ Legacy subscription router v2\.1\.x ]

  ```
  {
    "subscriptions": {
      "aws-greengrass-modbus": {
        "id": "aws-greengrass-modbus",
        "source": "component:aws.greengrass.Modbus",
        "subject": "modbus/adapter/response",
        "target": "cloud"
      }
    }
  }
  ```

------
#### [ Legacy subscription router v2\.0\.x ]

  ```
  {
    "subscriptions": {
      "aws-greengrass-modbus": {
        "id": "aws-greengrass-modbus",
        "source": "arn:aws:lambda:region:aws:function:aws-greengrass-modbus:version",
        "subject": "modbus/adapter/response",
        "target": "cloud"
      }
    }
  }
  ```<a name="connector-component-legacy-subscription-router-dependency-replace"></a>
  + Replace *region* with the AWS Region that you use\.
  + Replace *version* with the version of the Lambda function that this component runs\. To find the Lambda function version, you must view the recipe for the version of this component that you want to deploy\. Open this component's details page in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass), and look for the **Lambda function** key\-value pair\. This key\-value pair contains the name and version of the Lambda function\.

**Important**  <a name="connector-component-legacy-subscription-router-dependency-note"></a>
You must update the Lambda function version on the legacy subscription router every time you deploy this component\. This ensures that you use the correct Lambda function version for the component version that you deploy\.

------

  <a name="connector-component-create-deployments"></a>For more information, see [Create deployments](create-deployments.md)\.

## Dependencies<a name="modbus-rtu-protocol-adapter-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#modbus-rtu-protocol-adapter-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.0\.7 ]

The following table lists the dependencies for version 2\.0\.7 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.5\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

------
#### [ 2\.0\.6 ]

The following table lists the dependencies for version 2\.0\.6 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.4\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

------
#### [ 2\.0\.5 ]

The following table lists the dependencies for version 2\.0\.5 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.3\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

------
#### [ 2\.0\.4 ]

The following table lists the dependencies for version 2\.0\.4 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.2\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

------
#### [ 2\.0\.3 ]

The following table lists the dependencies for version 2\.0\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.3 <2\.1\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | >=1\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | >=1\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | >=1\.0\.0  | Hard | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="modbus-rtu-protocol-adapter-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

**Note**  <a name="connector-component-lambda-parameters"></a>
This component's default configuration includes Lambda function parameters\. We recommend that you edit only the following parameters to configure this component on your devices\.

`lambdaParams`  
An object that contains the parameters for this component's Lambda function\. This object contains the following information:    
`EnvironmentVariables`  
An object that contains the Lambda function's parameters\. This object contains the following information:    
`ModbusLocalPort`  
The absolute path to the physical Modbus serial port on the device, such as `/dev/ttyS2`\.  
To run this component in a container, you must define this path as a [system device](#modbus-rtu-protocol-adapter-configuration-devices) that the component can access\. This component runs in a container by default\.  
This component has read/write access to the device\.

`containerMode`  <a name="connector-component-container-mode-parameter"></a>
\(Optional\) The containerization mode for this component\. Choose from the following options:  
+ `GreengrassContainer` – The component runs in an isolated runtime environment inside the AWS IoT Greengrass container\.

  If you specify this option, you specify a [system device](#modbus-rtu-protocol-adapter-configuration-devices) to give the container access to the Modbus device\.
+ `NoContainer` – The component doesn't run in an isolated runtime environment\.
Default: `GreengrassContainer`

`containerParams`  
<a name="connector-component-container-params-description"></a>\(Optional\) An object that contains the container parameters for this component\. The component uses these parameters if you specify `GreengrassContainer` for `containerMode`\.  
This object contains the following information:    
`memorySize`  
<a name="connector-component-container-params-memory-size-description"></a>\(Optional\) The amount of memory \(in kilobytes\) to allocate to the component\.  
Defaults to 512 MB \(525,312 KB\)\.  
  `devices`   
\(Optional\) An object that specifies the system devices that the component can access in a container\.  
To run this component in a container, you must specify the system device that you configure in the `ModbusLocalPort` environment variable\.
This object contains the following information:    
`0` – This is an array index as a string\.  
An object that contains the following information:    
`path`  
The path to the system device on the core device\. This must have the same value as the value that you configure for `ModbusLocalPort`\.  
`permission`  
\(Optional\) The permission to access the system device from the container\. This value must be `rw`, which specifies that the component has read/write access to the system device\.  
Default: `rw`  
`addGroupOwner`  
\(Optional\) Whether or not to add the system group that runs the component as an owner of the system device\.  
Default: `true`

`pubsubTopics`  <a name="connector-component-pubsub-topics-parameter"></a>
\(Optional\) An object that contains the topics where the component subscribes to receive messages\. You can specify each topic and whether the component subscribes to MQTT topics from AWS IoT Core or local publish/subscribe topics\.  
This object contains the following information:    
`0` – This is an array index as a string\.  
An object that contains the following information:    
`type`  
\(Optional\) The type of publish/subscribe messaging that this component uses to subscribe to messages\. Choose from the following options:  
+ `Pubsub` – Subscribe to local publish/subscribe messages\. If you choose this option, the topic can't contain MQTT wildcards\. For more information about how to send messages from custom component when you specify this option, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.
+ `IotCore` – Subscribe to AWS IoT Core MQTT messages\. If you choose this option, the topic can contain MQTT wildcards\. For more information about how to send messages from custom components when you specify this option, see [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.
Default: `Pubsub`  
`topic`  
\(Optional\) The topic to which the component subscribes to receive messages\. If you specify `IotCore` for `type`, you can use MQTT wildcards \(`+` and `#`\) in this topic\.

**Example: Configuration merge update \(container mode\)**  

```
{
  "lambdaExecutionParameters": {
    "EnvironmentVariables": {
      "ModbusLocalPort": "/dev/ttyS2"
    }
  },
  "containerMode": "GreengrassContainer",
  "containerParams": {
    "devices": {
      "0": {
        "path": "/dev/ttyS2",
        "permission": "rw",
        "addGroupOwner": true
      }
    }
  }
}
```

**Example: Configuration merge update \(no container mode\)**  

```
{
  "lambdaExecutionParameters": {
    "EnvironmentVariables": {
      "ModbusLocalPort": "/dev/ttyS2"
    }
  },
  "containerMode": "NoContainer"
}
```

## Input data<a name="modbus-rtu-protocol-adapter-component-input-data"></a>

This component accepts Modbus RTU request parameters on the following topic and sends the Modbus RTU request to the device\. <a name="connector-component-local-publish"></a>By default, this component subscribes to local publish/subscribe messages\. For more information about how to publish messages to this component from your custom components, see [Publish/subscribe local messages](ipc-publish-subscribe.md)\.

**Default topic \(local publish/subscribe\):** `modbus/adapter/request`

<a name="connector-component-input-properties"></a>The message accepts the following properties\. Input messages must be in JSON format\.

`request`  
The parameters for the Modbus RTU request to send\.  
The shape of the request message depends on the type of Modbus RTU request that it represents\. The following properties are required for all requests\.  
Type: `object` that contains the following information:    
`operation`  
The name of the operation to run\. For example, specify `ReadCoilsRequest` to read coils on a Modbus RTU device\. For more information about supported operations, see [Modbus RTU requests and responses](#modbus-rtu-protocol-adapter-component-requests-responses)\.  
Type: `string`  
`device`  
The target device of the request\.  
This value must be an integer between `0` and `247`\.  
Type: `integer`
The other parameters to include in the request depend on the operation\. This component handles the [cyclic redundancy check \(CRC\)](https://en.wikipedia.org/wiki/Cyclic_redundancy_check) to verify data requests for you\.  
If you request includes an `address` property, you must specify its value as an integer\. For example, `"address": 1`\.

`id`  <a name="connector-component-input-property-id"></a>
An arbitrary ID for the request\. Use this property to map an input request to an output response\. When you specify this property, the component sets the `id` property in the response object to this value\.  
Type: `string`

**Example input: Read coils request**  

```
{
  "request": {
    "operation": "ReadCoilsRequest",
    "device": 1,
    "address": 1,
    "count": 1
  },
  "id": "MyRequest"
}
```

## Output data<a name="modbus-rtu-protocol-adapter-component-output-data"></a>

<a name="connector-component-output-data"></a>This component publishes responses as output data on the following MQTT topic by default\. You must specify this topic as the `subject` in the configuration for the [legacy subscription router component](legacy-subscription-router-component.md)\. For more information about how to subscribe to messages on this topic in your custom components, see [Publish/subscribe AWS IoT Core MQTT messages](ipc-iot-core-mqtt.md)\.

**Default topic \(AWS IoT Core MQTT\):** `modbus/adapter/response`

The shape of the response message depends on the request operation and the response status\. For examples, see [Example requests and responses](#modbus-rtu-protocol-adapter-component-examples)\.

Every response includes the following properties:

`response`  
The response from the Modbus RTU device\.  
Type: `object` that contains the following information:    
`status`  
The status of the request\. The status can be one of the following values:  
+ `Success` – The request was valid, the component sent the request to the Modbus RTU network, and the Modbus RTU network returned a response\.
+ `Exception` – The request was valid, the component sent the request to the Modbus RTU network, and the Modbus RTU network returned an exception\. For more information, see [Response status: Exception](#modbus-rtu-protocol-adapter-component-response-exception)\.
+ `No Response` – The request was invalid, and the component caught the error before it sent the request to the Modbus RTU network\. For more information, see [Response status: No response](#modbus-rtu-protocol-adapter-component-response-noresponse)\.  
`operation`  
The operation that the component requested\.  
`device`  
The device where the component sent the request\.  
`payload`  
The response from the Modbus RTU device\. If the `status` is `No Response`, this object contains only an `error` property with the description of the error \(for example, `[Input/Output] No Response received from the remote unit`\)\.

`id`  
The ID of the request, which you can use to identify which response corresponds to which request\.

**Note**  
A response for a write operation is simply an echo of the request\. Although write responses don't include meaningful information, it's a good practice to check the status of the response to see if the request succeeds or fails\.

**Example output: Success**  

```
{
  "response" : {
    "status" : "success",
    "device": 1,
    "operation": "ReadCoilsRequest",
    "payload": {
      "function_code": 1,
      "bits": [1]
    }
  },
  "id" : "MyRequest"
}
```

**Example output: Failure**  

```
{
  "response" : {
    "status" : "fail",
    "error_message": "Internal Error",
    "error": "Exception",
    "device": 1,
    "operation": "ReadCoilsRequest",
    "payload": {
      "function_code": 129,
      "exception_code": 2
    }
  },
  "id" : "MyRequest"
}
```

For more examples, see [Example requests and responses](#modbus-rtu-protocol-adapter-component-examples)\.

## Modbus RTU requests and responses<a name="modbus-rtu-protocol-adapter-component-requests-responses"></a>

This connector accepts Modbus RTU request parameters as [input data](#modbus-rtu-protocol-adapter-component-input-data) and publishes responses as [output data](#modbus-rtu-protocol-adapter-component-output-data)\.

The following common operations are supported\.


| Operation name in request | Function code in response | 
| --- | --- | 
| ReadCoilsRequest | 01 | 
| ReadDiscreteInputsRequest | 02 | 
| ReadHoldingRegistersRequest | 03 | 
| ReadInputRegistersRequest | 04 | 
| WriteSingleCoilRequest | 05 | 
| WriteSingleRegisterRequest | 06 | 
| WriteMultipleCoilsRequest | 15 | 
| WriteMultipleRegistersRequest | 16 | 
| MaskWriteRegisterRequest | 22 | 
| ReadWriteMultipleRegistersRequest | 23 | 

### Example requests and responses<a name="modbus-rtu-protocol-adapter-component-examples"></a>

The following are example requests and responses for supported operations\.

Read coils  
**Request example:**  

```
{
  "request": {
    "operation": "ReadCoilsRequest",
    "device": 1,
    "address": 1,
    "count": 1
  },
  "id": "TestRequest"
}
```
**Response example:**  

```
{
  "response": {
    "status": "success",
    "device": 1,
    "operation": "ReadCoilsRequest",
    "payload": {
      "function_code": 1,
      "bits": [1]
    }
  },
  "id" : "TestRequest"
}
```

Read discrete inputs  
**Request example:**  

```
{
  "request": {
    "operation": "ReadDiscreteInputsRequest",
    "device": 1,
    "address": 1,
    "count": 1
  },
  "id": "TestRequest"
}
```
**Response example:**  

```
{
  "response": {
    "status": "success",
    "device": 1,
    "operation": "ReadDiscreteInputsRequest",
    "payload": {
      "function_code": 2,
      "bits": [1]
    }
  },
  "id" : "TestRequest"
}
```

Read holding registers  
**Request example:**  

```
{
  "request": {
    "operation": "ReadHoldingRegistersRequest",
    "device": 1,
    "address": 1,
    "count": 1
  },
  "id": "TestRequest"
}
```
**Response example:**  

```
{
  "response": {
    "status": "success",
    "device": 1,
    "operation": "ReadHoldingRegistersRequest",
    "payload": {
      "function_code": 3,
      "registers": [20,30]
    }
  },
  "id" : "TestRequest"
}
```

Read input registers  
**Request example:**  

```
{
  "request": {
    "operation": "ReadInputRegistersRequest",
    "device": 1,
    "address": 1,
    "value": 1
  },
  "id": "TestRequest"
}
```

Write single coil  
**Request example:**  

```
{
  "request": {
    "operation": "WriteSingleCoilRequest",
    "device": 1,
    "address": 1,
    "value": 1
  },
  "id": "TestRequest"
}
```
**Response example:**  

```
{
  "response": {
    "status": "success",
    "device": 1,
    "operation": "WriteSingleCoilRequest",
    "payload": {
      "function_code": 5,
      "address": 1,
      "value": true
    }
  },
  "id" : "TestRequest"
}
```

Write single register  
**Request example:**  

```
{
  "request": {
    "operation": "WriteSingleRegisterRequest",
    "device": 1,
    "address": 1,
    "value": 1
  },
  "id": "TestRequest"
}
```

Write multiple coils  
**Request example:**  

```
{
  "request": {
    "operation": "WriteMultipleCoilsRequest",
    "device": 1,
    "address": 1,
    "values": [1,0,0,1]
  },
  "id": "TestRequest"
}
```
**Response example:**  

```
{
  "response": {
    "status": "success",
    "device": 1,
    "operation": "WriteMultipleCoilsRequest",
    "payload": {
      "function_code": 15,
      "address": 1,
      "count": 4
    }
  },
  "id" : "TestRequest"
}
```

Write multiple registers  
**Request example:**  

```
{
  "request": {
    "operation": "WriteMultipleRegistersRequest",
    "device": 1,
    "address": 1,
    "values": [20,30,10]
  },
  "id": "TestRequest"
}
```
**Response example:**  

```
{
  "response": {
    "status": "success",
    "device": 1,
    "operation": "WriteMultipleRegistersRequest",
    "payload": {
      "function_code": 23,
      "address": 1,
      "count": 3
    }
  },
  "id" : "TestRequest"
}
```

Mask write register  
**Request example:**  

```
{
  "request": {
    "operation": "MaskWriteRegisterRequest",
    "device": 1,
    "address": 1,
    "and_mask": 175,
    "or_mask": 1
  },
  "id": "TestRequest"
}
```
**Response example:**  

```
{
  "response": {
    "status": "success",
    "device": 1,
    "operation": "MaskWriteRegisterRequest",
    "payload": {
      "function_code": 22,
      "and_mask": 0,
      "or_mask": 8
    }
  },
  "id" : "TestRequest"
}
```

Read write multiple registers  
**Request example:**  

```
{
  "request": {
    "operation": "ReadWriteMultipleRegistersRequest",
    "device": 1,
    "read_address": 1,
    "read_count": 2,
    "write_address": 3,
    "write_registers": [20,30,40]
  },
  "id": "TestRequest"
}
```
**Response example:**  

```
{
  "response": {
    "status": "success",
    "device": 1,
    "operation": "ReadWriteMultipleRegistersRequest",
    "payload": {
      "function_code": 23,
      "registers": [10,20,10,20]
    }
  },
  "id" : "TestRequest"
}
```
The response includes the registers that the component reads\.

### Response status: Exception<a name="modbus-rtu-protocol-adapter-component-response-exception"></a>

Exceptions can occur when the request format is valid, but the request is not completed successfully\. In this case, the response contains the following information:
+ The `status` is set to `Exception`\.
+ The `function_code` equals the function code of the request \+ 128\.
+ The `exception_code` contains the exception code\. For more information, see Modbus exception codes\.

**Example:**

```
{
  "response": {
    "status": "fail",
    "error_message": "Internal Error",
    "error": "Exception",
    "device": 1,
    "operation": "ReadCoilsRequest",
    "payload": {
      "function_code": 129,
      "exception_code": 2
    }
  },
  "id": "TestRequest"
}
```

### Response status: No response<a name="modbus-rtu-protocol-adapter-component-response-noresponse"></a>

This connector performs validation checks on the Modbus request\. For example, it checks for invalid formats and missing fields\. If the validation fails, the connector doesn't send the request\. Instead, it returns a response that contains the following information:
+ The `status` is set to `No Response`\.
+ The `error` contains the error reason\.
+ The `error_message` contains the error message\.

**Examples:**

```
{
  "response": {
    "status": "fail",
    "error_message": "Invalid address field. Expected <type 'int'>, got <type 'str'>",
    "error": "No Response",
    "device": 1,
    "operation": "ReadCoilsRequest",
    "payload": {
      "error": "Invalid address field. Expected Expected <type 'int'>, got <type 'str'>"
    }
  },
  "id": "TestRequest"
}
```

If the request targets a nonexistent device or if the Modbus RTU network is not working, you might get a `ModbusIOException`, which uses the No Response format\.

```
{
  "response": {
    "status": "fail",
    "error_message": "[Input/Output] No Response received from the remote unit",
    "error": "No Response",
    "device": 1,
    "operation": "ReadCoilsRequest",
    "payload": {
      "error": "[Input/Output] No Response received from the remote unit"
    }
  },
  "id": "TestRequest"
}
```

## Local log file<a name="modbus-rtu-protocol-adapter-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/aws.greengrass.Modbus.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.Modbus.log
  ```

## Licenses<a name="modbus-rtu-protocol-adapter-component-licenses"></a>

This component includes the following third\-party software/licensing:
+ [pymodbus](https://github.com/riptideio/pymodbus/blob/master/README.rst)/BSD License
+ [pyserial](https://github.com/pyserial/pyserial)/BSD License

<a name="component-core-software-license"></a>This component is released under the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

## Changelog<a name="modbus-rtu-protocol-adapter-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.7  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.6  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.5  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.0\.4  |  Version updated for Greengrass nucleus version 2\.1\.0 release\.  | 
|  2\.0\.3  |  Initial version\.  | 