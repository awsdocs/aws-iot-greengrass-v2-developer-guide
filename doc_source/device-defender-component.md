# AWS IoT Device Defender<a name="device-defender-component"></a>

The AWS IoT Device Defender component \(`aws.greengrass.DeviceDefender`\) notifies administrators about changes in the state of Greengrass core devices\. This can help identify unusual behavior that might indicate a compromised device\. For more information, see [AWS IoT Device Defender](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender.html) in the *AWS IoT Core Developer Guide*\.

This component reads system metrics on the core device\. Then, it publishes the metrics to AWS IoT Device Defender\. For more information about how to read and interpret the metrics that this component reports, see [Device metrics document specification](https://docs.aws.amazon.com/iot/latest/developerguide/detect-device-side-metrics.html#DetectMetricsMessagesSpec) in the *AWS IoT Core Developer Guide*\.

**Note**  
This component provides similar functionality to the Device Defender connector in AWS IoT Greengrass V1\. For more information, see [Device Defender connector](https://docs.aws.amazon.com/greengrass/latest/developerguide/device-defender-connector.html) in the *AWS IoT Greengrass V1 Developer Guide*\.

**Topics**
+ [Versions](#device-defender-component-versions)
+ [Type](#device-defender-component-type)
+ [Operating system](#device-defender-component-os-support)
+ [Requirements](#device-defender-component-requirements)
+ [Dependencies](#device-defender-component-dependencies)
+ [Configuration](#device-defender-component-configuration)
+ [Input data](#device-defender-component-input-data)
+ [Output data](#device-defender-component-output-data)
+ [Local log file](#device-defender-component-log-file)
+ [Licenses](#device-defender-component-licenses)
+ [Changelog](#device-defender-component-changelog)

## Versions<a name="device-defender-component-versions"></a>

This component has the following versions:
+ 3\.0\.x
+ 2\.0\.x

For information about changes in each version of the component, see the [changelog](#device-defender-component-changelog)\.

## Type<a name="device-defender-component-type"></a>

------
#### [ 3\.0\.x ]

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

------
#### [ 2\.0\.x ]

<a name="public-component-type-lambda"></a>This <a name="public-component-type-lambda-phrase"></a>component is a Lambda component \(`aws.greengrass.lambda`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component's Lambda function using the [Lambda launcher component](lambda-launcher-component.md)\.

------

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="device-defender-component-os-support"></a>

------
#### [ 3\.0\.x ]

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

------
#### [ 2\.0\.x ]

This component can be installed on Linux core devices only\.

------

## Requirements<a name="device-defender-component-requirements"></a>

This component has the following requirements:

------
#### [ 3\.0\.x ]
+ <a name="public-component-python3-requirement"></a>[Python](https://www.python.org/) version 3\.7 installed on the core device and added to the PATH environment variable\.
+ AWS IoT Device Defender configured to use the Detect feature to monitor violations\. For more information, see [Detect](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender-detect.html) in the *AWS IoT Core Developer Guide*\.

------
#### [ 2\.0\.x ]
+ <a name="core-device-lambda-function-requirements"></a>Your core device must meet the requirements to run Lambda functions\. If you want the core device to run containerized Lambda functions, the device must meet the requirements to do so\. For more information, see [Lambda function requirements](setting-up.md#greengrass-v2-lambda-requirements)\.
+ <a name="public-component-python3-requirement"></a>[Python](https://www.python.org/) version 3\.7 installed on the core device and added to the PATH environment variable\.
+ AWS IoT Device Defender configured to use the Detect feature to monitor violations\. For more information, see [Detect](https://docs.aws.amazon.com/iot/latest/developerguide/device-defender-detect.html) in the *AWS IoT Core Developer Guide*\.
+ The [psutil](https://pypi.org/project/psutil/) library installed on the core device\. Version 5\.7\.0 is the latest version that is verified to work with the component\.
+ The [cbor](https://pypi.org/project/cbor/) library installed on the core device\. Version 1\.0\.0 is the latest version that is verified to work with the component\.
+ <a name="connector-component-legacy-subscription-router-dependency"></a>To receive output data from this component, you must merge the following configuration update for the [legacy subscription router component](legacy-subscription-router-component.md) when you deploy this component\. The legacy subscription router component \(`aws.greengrass.LegacySubscriptionRouter`\) is a dependency of this component\. This configuration specifies the topic where this component publishes responses\.

------
#### [ Legacy subscription router v2\.1\.x ]

  ```
  {
    "subscriptions": {
      "aws-greengrass-device-defender": {
        "id": "aws-greengrass-device-defender",
        "source": "component:aws.greengrass.DeviceDefender",
        "subject": "$aws/things/+/defender/metrics/json",
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
      "aws-greengrass-device-defender": {
        "id": "aws-greengrass-device-defender",
        "source": "arn:aws:lambda:region:aws:function:aws-greengrass-device-defender:version",
        "subject": "$aws/things/+/defender/metrics/json",
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

------

## Dependencies<a name="device-defender-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#device-defender-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 3\.0\.0 ]

The following table lists the dependencies for version 3\.0\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <3\.0\.0 | Soft | 
| [Token exchange service](token-exchange-service-component.md) | >=0\.0\.0 | Hard | 

------
#### [ 2\.0\.8 ]

The following table lists the dependencies for version 2\.0\.8 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.6\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

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

## Configuration<a name="device-defender-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ 3\.0\.x ]

`SampleIntervalSeconds`  
\(Optional\) The amount of time in seconds between each cycle where the component gathers and reports metrics\.  
The minimum value is 300 seconds \(5 minutes\)\.  
Default: 300 seconds

 `UseInstaller`   
\(Optional\) Boolean value that defines whether to use the installer script in this component to install this component's dependencies\.  

Set this value to `false` if you want to use a custom script to install dependencies, or if you want to include runtime dependencies in a pre\-built Linux image\. To use this component, you will need to install the following libraries, including any dependencies, and make them available to the default Greengrass system user\.
+ [AWS IoT Device SDK v2 for Python](https://github.com/aws/aws-iot-device-sdk-python-v2)
+ [cbor](https://pypi.org/project/cbor/) library\. Version 1\.0\.0 is the latest version that is verified to work with the component\.
+ [psutil](https://pypi.org/project/psutil/) library\. Version 5\.7\.0 is the latest version that is verified to work with the component\.
Default: `true`

------
#### [ 2\.0\.x ]

**Note**  <a name="connector-component-lambda-parameters"></a>
This component's default configuration includes Lambda function parameters\. We recommend that you edit only the following parameters to configure this component on your devices\.

`lambdaParams`  
An object that contains the parameters for this component's Lambda function\. This object contains the following information:    
`EnvironmentVariables`  
An object that contains the Lambda function's parameters\. This object contains the following information:    
`PROCFS_PATH`  
\(Optional\) The path to the `/proc` folder\.  
+ To run this component in a container, use the default value, `/host-proc`\. The component runs in a container by default\.
+ To run this component in no container mode, specify `/proc` for this parameter\.
Default: `/host-proc`\. This is the default path where this component mounts the `/proc` folder in the container\.  
This component has read\-only access to this folder\.  
`SAMPLE_INTERVAL_SECONDS`  
\(Optional\) The amount of time in seconds between each cycle where the component gathers and reports metrics\.  
The minimum value is 300 seconds \(5 minutes\)\.  
Default: 300 seconds

`containerMode`  
\(Optional\) The containerization mode for this component\. Choose from the following options:  
+ `GreengrassContainer` – The component runs in an isolated runtime environment inside the AWS IoT Greengrass container\.
+ `NoContainer` – The component doesn't run in an isolated runtime environment\.

  If you specify this option, you must specify `/proc` for the `PROCFS_PATH` environment variable parameter\.
Default: `GreengrassContainer`

`containerParams`  
<a name="connector-component-container-params-description"></a>\(Optional\) An object that contains the container parameters for this component\. The component uses these parameters if you specify `GreengrassContainer` for `containerMode`\.  
This object contains the following information:    
`memorySize`  
<a name="connector-component-container-params-memory-size-description"></a>\(Optional\) The amount of memory \(in kilobytes\) to allocate to the component\.  
Defaults to 50,000 KB\.

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
      "PROCFS_PATH": "/host_proc"
    }
  },
  "containerMode": "GreengrassContainer"
}
```

**Example: Configuration merge update \(no container mode\)**  

```
{
  "lambdaExecutionParameters": {
    "EnvironmentVariables": {
      "PROCFS_PATH": "/proc"
    }
  },
  "containerMode": "NoContainer"
}
```

------

## Input data<a name="device-defender-component-input-data"></a>

This component doesn't accept messages as input data\.

## Output data<a name="device-defender-component-output-data"></a>

This component publishes security metrics to the following reserved topic for AWS IoT Device Defender\. This component replaces *coreDeviceName* with the name of the core device when it publishes the metrics\.

**Topic \(AWS IoT Core MQTT\):** `$aws/things/coreDeviceName/defender/metrics/json`

**Example output**  

```
{
  "header": {
    "report_id": 1529963534,
    "version": "1.0"
  },
  "metrics": {
    "listening_tcp_ports": {
      "ports": [
        {
          "interface": "eth0",
          "port": 24800
        },
        {
          "interface": "eth0",
          "port": 22
        },
        {
          "interface": "eth0",
          "port": 53
        }
      ],
      "total": 3
    },
    "listening_udp_ports": {
      "ports": [
        {
          "interface": "eth0",
          "port": 5353
        },
        {
          "interface": "eth0",
          "port": 67
        }
      ],
      "total": 2
    },
    "network_stats": {
      "bytes_in": 1157864729406,
      "bytes_out": 1170821865,
      "packets_in": 693092175031,
      "packets_out": 738917180
    },
    "tcp_connections": {
      "established_connections":{
        "connections": [
          {
            "local_interface": "eth0",
            "local_port": 80,
            "remote_addr": "192.168.0.1:8000"
          },
          {
            "local_interface": "eth0",
            "local_port": 80,
            "remote_addr": "192.168.0.1:8000"
          }
        ],
        "total": 2
      }
    }
  }
}
```

For more information about the metrics that this component reports, see [Device metrics document specification](https://docs.aws.amazon.com/iot/latest/developerguide/detect-device-side-metrics.html#DetectMetricsMessagesSpec) in the *AWS IoT Core Developer Guide*\.

## Local log file<a name="device-defender-component-log-file"></a>

This component uses the following log file\.

------
#### [ Linux ]

```
/greengrass/v2/logs/aws.greengrass.DeviceDefender.log
```

------
#### [ Windows ]

```
C:\greengrass\v2\logs\aws.greengrass.DeviceDefender.log
```

------

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux ]

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.DeviceDefender.log
  ```

------
#### [ Windows \(PowerShell\) ]

  ```
  Get-Content C:\greengrass\v2\logs\aws.greengrass.DeviceDefender.log -Tail 10 -Wait
  ```

------

## Licenses<a name="device-defender-component-licenses"></a>

<a name="component-core-software-license"></a>This component is released under the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

## Changelog<a name="device-defender-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.8  |  Version updated for Greengrass nucleus version 2\.5\.0 release\.  | 
|  3\.0\.0  |  <a name="changelog-device-defender-3.0.0-major-version-changes"></a>This version of the AWS IoT Device Defender component expects different configuration parameters than version 2\.x\. If you use a non\-default configuration for version 2\.x, and you want to upgrade from v2\.x to v3\.x, you must update the component's configuration\. For more information, see [AWS IoT Device Defender component configuration](#device-defender-component-configuration)\. <a name="changelog-device-defender-3.0.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/device-defender-component.html)  | 
|  2\.0\.7  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.6  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.5  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.0\.4  |  Version updated for Greengrass nucleus version 2\.1\.0 release\.  | 
|  2\.0\.3  |  Initial version\.  | 