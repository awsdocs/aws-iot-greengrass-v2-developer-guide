# Client device auth<a name="client-device-auth-component"></a>

The client device auth component \(`aws.greengrass.clientdevices.Auth`\) authenticates client devices and authorizes client device actions\.

**Note**  <a name="client-device-component-context"></a>
Client devices are local IoT devices that connect to a Greengrass core device to send MQTT messages and data to process\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

**Topics**
+ [Versions](#client-device-auth-component-versions)
+ [Type](#client-device-auth-component-type)
+ [Operating system](#client-device-auth-component-os-support)
+ [Requirements](#client-device-auth-component-requirements)
+ [Dependencies](#client-device-auth-component-dependencies)
+ [Configuration](#client-device-auth-component-configuration)
+ [Local log file](#client-device-auth-component-log-file)
+ [Changelog](#client-device-auth-component-changelog)

## Versions<a name="client-device-auth-component-versions"></a>

This component has the following versions:

**Note**  
Client device auth version 2\.3\.0 has been discontinued\. We strongly recommend that you upgrade to client device auth version 2\.3\.1\.
+ 2\.3\.x
+ 2\.2\.x
+ 2\.1\.x
+ 2\.0\.x

## Type<a name="client-device-auth-component-type"></a>

<a name="public-component-type-plugin-para1"></a>This component is a plugin component \(`aws.greengrass.plugin`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component in the same Java Virtual Machine \(JVM\) as the nucleus\. The nucleus restarts when you change this component's version on the core device\.

<a name="public-component-type-plugin-para2"></a>This component uses the same log file as the Greengrass nucleus\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="client-device-auth-component-os-support"></a>

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

## Requirements<a name="client-device-auth-component-requirements"></a>

This component has the following requirements:
+ The [Greengrass service role](greengrass-service-role.md) must be associated to your AWS account and allow the `iot:DescribeCertificate` permission\.
+ The core device's AWS IoT policy must allow the following permissions:
  + `greengrass:GetConnectivityInfo`, where the resources include the ARN of the core device that runs this component
  + `greengrass:VerifyClientDeviceIoTCertificateAssociation`, where the resources include the Amazon Resource Name \(ARN\) of each client device that connects to the core device
  + `greengrass:VerifyClientDeviceIdentity`
  + `greengrass:PutCertificateAuthorities`
  + `iot:Publish`, where the resources include the ARN of the following MQTT topic:
    + `$aws/things/coreDeviceThingName*-gci/shadow/get`
  + `iot:Subscribe`, where the resources include the ARNs of the following MQTT topic filters:
    + `$aws/things/coreDeviceThingName*-gci/shadow/update/delta`
    + `$aws/things/coreDeviceThingName*-gci/shadow/get/accepted`
  + `iot:Receive`, where the resources include the ARNs of the following MQTT topics:
    + `$aws/things/coreDeviceThingName*-gci/shadow/update/delta`
    + `$aws/things/coreDeviceThingName*-gci/shadow/get/accepted`

  For more information, see [AWS IoT policies for data plane operations](device-auth.md#iot-policies) and [Minimal AWS IoT policy to support client devices](device-auth.md#client-device-support-minimal-iot-policy)\.
+ \(Optional\) To use offline authentication, the AWS Identity and Access Management \(IAM\) role used by the AWS IoT Greengrass service must contain the following permission:
  + `greengrass:ListClientDevicesAssociatedWithCoreDevice` to enable the core device to list clients for offline authentication\.

### Endpoints and ports<a name="client-device-auth-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `iot.region.amazonaws.com`  | 443 | Yes |  Used to get information about AWS IoT thing certificates\.  | 

## Dependencies<a name="client-device-auth-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#client-device-auth-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.3\.1 and 2\.3\.2 ]

The following table lists the dependencies for version 2\.3\.1 and 2\.3\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.6\.0 <2\.10\.0 | Soft | 

------
#### [ 2\.3\.0 ]

The following table lists the dependencies for version 2\.3\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.6\.0 <2\.10\.0 | Soft | 

------
#### [ 2\.2\.3 ]

The following table lists the dependencies for version 2\.2\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.6\.0 <=2\.9\.0 | Soft | 

------
#### [ 2\.2\.2 ]

The following table lists the dependencies for version 2\.2\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.6\.0 <=2\.8\.0 | Soft | 

------
#### [ 2\.2\.1 ]

The following table lists the dependencies for version 2\.2\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.6\.0 <2\.8\.0 | Soft | 

------
#### [ 2\.2\.0 ]

The following table lists the dependencies for version 2\.2\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.6\.0 <2\.7\.0 | Soft | 

------
#### [ 2\.1\.0 ]

The following table lists the dependencies for version 2\.1\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.7\.0 | Soft | 

------
#### [ 2\.0\.4 ]

The following table lists the dependencies for version 2\.0\.4 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.6\.0 | Soft | 

------
#### [ 2\.0\.2 and 2\.0\.3 ]

The following table lists the dependencies for versions 2\.0\.2 and 2\.0\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.5\.0 | Soft | 

------
#### [ 2\.0\.1 ]

The following table lists the dependencies for version 2\.0\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.4\.0 | Soft | 

------
#### [ 2\.0\.0 ]

The following table lists the dependencies for version 2\.0\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.3\.0 | Soft | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="client-device-auth-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ v2\.3\.x ]

`deviceGroups`  <a name="client-device-auth-component-configuration-devicegroups"></a>
Device groups are groups of client devices that have permissions to connect and communicate with a core device\. Use selection rules to identify groups of client devices, and define *client device authorization policies* that specify the permissions for each device group\.  
This object contains the following information:    
`formatVersion`  
The format version for this configuration object\.  
Choose from the following options:  
+ `2021-03-05`  
`definitions`  
The device groups for this core device\. Each definition specifies a *selection rule* to evaluate if a client device is a member of the group\. Each definition also specifies the permissions policy to apply to client devices that match the selection rule\. If a client device is a member of multiple device groups, the device's permissions are comprised of each group's permissions policy\.  
This object contains the following information:    
`groupNameKey`  
The name of this device group\. Replace *groupNameKey* with a name that helps you identify this device group\.  
This object contains the following information:    
`selectionRule`  
The query that specifies which client devices are members of this device group\. When a client device connects, the core device evaluates this selection rule to determine if the client device is a member of this device group\. If the client device is a member, the core device uses this device group's policy to authorize the client device's actions\.  
Each selection rule comprises at least one *selection rule clause*, which is a single expression query that can match client devices\. Selection rules use the same query syntax as AWS IoT fleet indexing\. For more information about selection rule syntax, see [AWS IoT fleet indexing query syntax](https://docs.aws.amazon.com/iot/latest/developerguide/query-syntax.html) in the *AWS IoT Core Developer Guide*\.  
Use the `*` wildcard to match multiple client devices with one selection rule clause\. You can use this wildcard at the end of the thing name to match client devices whose names start with a string that you specify\. You can also use this wildcard to match all client devices\.  
To select a value that contains a colon character \(`:`\), escape the colon with a backslash character \(`\`\)\. In formats such as JSON, you must escape backslash characters, so you enter two backslash characters before the colon character\. For example, specify `thingName: MyTeam\\:ClientDevice1` to select a thing whose name is `MyTeam:ClientDevice1`\.
You can specify the following selector:  
+ `thingName` – The name of a client device's AWS IoT thing\.

**Example selection rule**  
The following selection rule matches client devices whose names are `MyClientDevice1` or `MyClientDevice2`\.  

```
thingName: MyClientDevice1 OR thingName: MyClientDevice2
```

**Example selection rule \(use wildcards\)**  
The following selection rule matches client devices whose names start with `MyClientDevice`\.  

```
thingName: MyClientDevice*
```

**Example selection rule \(match all devices\)**  
The following selection rule matches all client devices\.  

```
thingName: *
```  
`policyName`  
The permissions policy that applies to client devices in this device group\. Specify the name of a policy that you define in the `policies` object\.  
`policies`  
The client device authorization policies for client devices that connect to the core device\. Each authorization policy specifies a set of actions and the resources where a client device can perform those actions\.  
This object contains the following information:    
`policyNameKey`  
The name of this authorization policy\. Replace *policyNameKey* with a name that helps you identify this authorization policy\. You use this policy name to define which policy applies to a device group\.  
This object contains the following information:    
`statementNameKey`  
The name of this policy statement\. Replace *statementNameKey* with a name that helps you identify this policy statement\.  
This object contains the following information:    
`operations`  
The list of operations to allow  for the resources in this policy\.  
You can include any of the following operations:  
+ `mqtt:connect` – Grants permission to connect to the core device\. Client devices must have this permission to connect to a core device\.

  This operation supports the following resources:
  + `mqtt:clientId:deviceClientId` – Restrict access based on the client ID that a client device uses to connect to the core device's MQTT broker\. Replace *deviceClientId* with the client ID to use\.
+ `mqtt:publish` – Grants permission to publish MQTT messages to topics\.

  This operation supports the following resources:
  + <a name="client-device-authorization-policy-resource-mqtt-topic"></a>`mqtt:topic:mqttTopic` – Restrict access based on the MQTT topic where a client device publishes a message\. Replace *mqttTopic* with the topic to use\.

    This resource doesn't support MQTT topic wildcards\.
+ `mqtt:subscribe` – Grants permission to subscribe to MQTT topic filters to receive messages\.

  This operation supports the following resources:
  + <a name="client-device-authorization-policy-resource-mqtt-topicfilter"></a>`mqtt:topicfilter:mqttTopicFilter` – Restrict access based on the MQTT topics where a client device can subscribe to messages\. Replace *mqttTopicFilter* with the topic filter to use\.

    This resource supports the `+` and `#` MQTT topic wildcards\. For more information, see [MQTT topics](https://docs.aws.amazon.com/iot/latest/developerguide/topics.html) in the *AWS IoT Core Developer Guide*\.

    The client device can subscribe to the exact topic filters that you allow\. For example, if you allow the client device to subscribe to the `mqtt:topicfilter:client/+/status` resource, the client device can subscribe to `client/+/status` but not `client/client1/status`\.
You can specify the `*` wildcard to allow access to all actions\.  
`resources`  
The list of resources to allow  for the operations in this policy\. Specify resources that correspond to the operations in this policy\. For example, you might specify a list of MQTT topic resources \(`mqtt:topic:mqttTopic`\) in a policy that specifies the `mqtt:publish` operation\.  
You can specify the `*` wildcard to allow access to all resources\. You can't use the `*` wildcard to match partial resource identifiers\. For example, you can specify **"resources": "\*"**, but you can't specify **"resources": "mqtt:clientId:\*"**\.  
`statementDescription`  
\(Optional\) A description for this policy statement\.

`certificates`  <a name="client-device-auth-component-configuration-certificates"></a>
\(Optional\) The certificate configuration options for this core device\. This object contains the following information:    
`serverCertificateValiditySeconds`  
\(Optional\) The amount of time \(in seconds\) after which the local MQTT server certificate expires\. You can configure this option to customize how often client devices disconnect and reconnect to the core device\.  
This component rotates the local MQTT server certificate 24 hours before it expires\. The MQTT broker, such as the [Moquette MQTT broker component](mqtt-broker-moquette-component.md), generates a new certificate and restarts\. When this happens, all client devices connected to this core device are disconnected\. Client devices can reconnect to the core device after a short period of time\.  
Default: `604800` \(7 days\)  
Minimum value: `172800` \(2 days\)  
Maximum value: `864000` \(10 days\)

`performance`  
\(Optional\) The performance configuration options for this core device\. This object contains the following information:    
`maxActiveAuthTokens`  
\(Optional\) The maximum number of active client device authorization tokens\. You can increase this number to enable a greater number of client devices to connect to a single core device without reauthenticating them\.  
Default: `2500`  
`cloudRequestQueueSize`  
\(Optional\) The maximum number of AWS Cloud requests to queue before this component rejects requests\.  
Default: `100`  
`maxConcurrentCloudRequests`  
\(Optional\) The maximum number of concurrent requests to send to the AWS Cloud\. You can increase this number to improve authentication performance on core devices where you connect large numbers of client devices\.  
Default: `1`

`certificateAuthority`  
\(Optional\) Certificate authority configuration options to replace the core device intermediate authority with your own intermediate certificate authority\. This object contains the following information\.    
certificateUri  
The location of the certificate\. It can be a file system URI or a URI that points to a certificate stored in a hardware security module\.  
`certificateChainUri`  
The location of the certificate chain for the core device CA\. This should be the complete certificate chain back to your root CA\. It can be a file system URI or a URI that points to a certificate chain stored in a hardware security module\.  
`privateKeyUri`  
The location of the core device's private key\. This can be a file system URI or a URI that points to a certificate private key stored in a hardware security module\.

`security`  
\(Optional\) Security configuration options for this core device\. This object contains the following information\.    
`clientDeviceTrustDurationMinutes`  
The duration in minutes that the authentication information of a client device can be trusted before it is required to reauthenticate with the core device\. The default value is 1\.

**Example: Configuration merge update \(using a restrictive policy\)**  <a name="client-device-auth-component-configuration-example-restrictive"></a>
The following example configuration specifies to allow client devices whose names start with `MyClientDevice` to connect and publish/subscribe on all topics\.  

```
{
  "deviceGroups": {
    "formatVersion": "2021-03-05",
    "definitions": {
      "MyDeviceGroup": {
        "selectionRule": "thingName: MyClientDevice*",
        "policyName": "MyRestrictivePolicy"
      }
    },
    "policies": {
      "MyRestrictivePolicy": {
        "AllowConnect": {
          "statementDescription": "Allow client devices to connect.",
          "operations": [
            "mqtt:connect"
          ],
          "resources": [
            "*"
          ]
        },
        "AllowPublish": {
          "statementDescription": "Allow client devices to publish on test/topic.",
          "operations": [
            "mqtt:publish"
          ],
          "resources": [
            "mqtt:topic:test/topic"
          ]
        },
        "AllowSubscribe": {
          "statementDescription": "Allow client devices to subscribe to test/topic/response.",
          "operations": [
            "mqtt:subscribe"
          ],
          "resources": [
            "mqtt:topicfilter:test/topic/response"
          ]
        }
      }
    }
  }
}
```

**Example: Configuration merge update \(using a permissive policy\)**  <a name="client-device-auth-component-configuration-example-permissive"></a>
The following example configuration specifies to allow all client devices to connect and publish/subscribe on all topics\.  

```
{
  "deviceGroups": {
    "formatVersion": "2021-03-05",
    "definitions": {
      "MyPermissiveDeviceGroup": {
        "selectionRule": "thingName: *",
        "policyName": "MyPermissivePolicy"
      }
    },
    "policies": {
      "MyPermissivePolicy": {
        "AllowAll": {
          "statementDescription": "Allow client devices to perform all actions.",
          "operations": [
            "*"
          ],
          "resources": [
            "*"
          ]
        }
      }
    }
  }
}
```

------
#### [ v2\.2\.x ]

`deviceGroups`  <a name="client-device-auth-component-configuration-devicegroups"></a>
Device groups are groups of client devices that have permissions to connect and communicate with a core device\. Use selection rules to identify groups of client devices, and define *client device authorization policies* that specify the permissions for each device group\.  
This object contains the following information:    
`formatVersion`  
The format version for this configuration object\.  
Choose from the following options:  
+ `2021-03-05`  
`definitions`  
The device groups for this core device\. Each definition specifies a *selection rule* to evaluate if a client device is a member of the group\. Each definition also specifies the permissions policy to apply to client devices that match the selection rule\. If a client device is a member of multiple device groups, the device's permissions are comprised of each group's permissions policy\.  
This object contains the following information:    
`groupNameKey`  
The name of this device group\. Replace *groupNameKey* with a name that helps you identify this device group\.  
This object contains the following information:    
`selectionRule`  
The query that specifies which client devices are members of this device group\. When a client device connects, the core device evaluates this selection rule to determine if the client device is a member of this device group\. If the client device is a member, the core device uses this device group's policy to authorize the client device's actions\.  
Each selection rule comprises at least one *selection rule clause*, which is a single expression query that can match client devices\. Selection rules use the same query syntax as AWS IoT fleet indexing\. For more information about selection rule syntax, see [AWS IoT fleet indexing query syntax](https://docs.aws.amazon.com/iot/latest/developerguide/query-syntax.html) in the *AWS IoT Core Developer Guide*\.  
Use the `*` wildcard to match multiple client devices with one selection rule clause\. You can use this wildcard at the end of the thing name to match client devices whose names start with a string that you specify\. You can also use this wildcard to match all client devices\.  
To select a value that contains a colon character \(`:`\), escape the colon with a backslash character \(`\`\)\. In formats such as JSON, you must escape backslash characters, so you enter two backslash characters before the colon character\. For example, specify `thingName: MyTeam\\:ClientDevice1` to select a thing whose name is `MyTeam:ClientDevice1`\.
You can specify the following selector:  
+ `thingName` – The name of a client device's AWS IoT thing\.

**Example selection rule**  
The following selection rule matches client devices whose names are `MyClientDevice1` or `MyClientDevice2`\.  

```
thingName: MyClientDevice1 OR thingName: MyClientDevice2
```

**Example selection rule \(use wildcards\)**  
The following selection rule matches client devices whose names start with `MyClientDevice`\.  

```
thingName: MyClientDevice*
```

**Example selection rule \(match all devices\)**  
The following selection rule matches all client devices\.  

```
thingName: *
```  
`policyName`  
The permissions policy that applies to client devices in this device group\. Specify the name of a policy that you define in the `policies` object\.  
`policies`  
The client device authorization policies for client devices that connect to the core device\. Each authorization policy specifies a set of actions and the resources where a client device can perform those actions\.  
This object contains the following information:    
`policyNameKey`  
The name of this authorization policy\. Replace *policyNameKey* with a name that helps you identify this authorization policy\. You use this policy name to define which policy applies to a device group\.  
This object contains the following information:    
`statementNameKey`  
The name of this policy statement\. Replace *statementNameKey* with a name that helps you identify this policy statement\.  
This object contains the following information:    
`operations`  
The list of operations to allow  for the resources in this policy\.  
You can include any of the following operations:  
+ `mqtt:connect` – Grants permission to connect to the core device\. Client devices must have this permission to connect to a core device\.

  This operation supports the following resources:
  + `mqtt:clientId:deviceClientId` – Restrict access based on the client ID that a client device uses to connect to the core device's MQTT broker\. Replace *deviceClientId* with the client ID to use\.
+ `mqtt:publish` – Grants permission to publish MQTT messages to topics\.

  This operation supports the following resources:
  + <a name="client-device-authorization-policy-resource-mqtt-topic"></a>`mqtt:topic:mqttTopic` – Restrict access based on the MQTT topic where a client device publishes a message\. Replace *mqttTopic* with the topic to use\.

    This resource doesn't support MQTT topic wildcards\.
+ `mqtt:subscribe` – Grants permission to subscribe to MQTT topic filters to receive messages\.

  This operation supports the following resources:
  + <a name="client-device-authorization-policy-resource-mqtt-topicfilter"></a>`mqtt:topicfilter:mqttTopicFilter` – Restrict access based on the MQTT topics where a client device can subscribe to messages\. Replace *mqttTopicFilter* with the topic filter to use\.

    This resource supports the `+` and `#` MQTT topic wildcards\. For more information, see [MQTT topics](https://docs.aws.amazon.com/iot/latest/developerguide/topics.html) in the *AWS IoT Core Developer Guide*\.

    The client device can subscribe to the exact topic filters that you allow\. For example, if you allow the client device to subscribe to the `mqtt:topicfilter:client/+/status` resource, the client device can subscribe to `client/+/status` but not `client/client1/status`\.
You can specify the `*` wildcard to allow access to all actions\.  
`resources`  
The list of resources to allow  for the operations in this policy\. Specify resources that correspond to the operations in this policy\. For example, you might specify a list of MQTT topic resources \(`mqtt:topic:mqttTopic`\) in a policy that specifies the `mqtt:publish` operation\.  
You can specify the `*` wildcard to allow access to all resources\. You can't use the `*` wildcard to match partial resource identifiers\. For example, you can specify **"resources": "\*"**, but you can't specify **"resources": "mqtt:clientId:\*"**\.  
`statementDescription`  
\(Optional\) A description for this policy statement\.

`certificates`  <a name="client-device-auth-component-configuration-certificates"></a>
\(Optional\) The certificate configuration options for this core device\. This object contains the following information:    
`serverCertificateValiditySeconds`  
\(Optional\) The amount of time \(in seconds\) after which the local MQTT server certificate expires\. You can configure this option to customize how often client devices disconnect and reconnect to the core device\.  
This component rotates the local MQTT server certificate 24 hours before it expires\. The MQTT broker, such as the [Moquette MQTT broker component](mqtt-broker-moquette-component.md), generates a new certificate and restarts\. When this happens, all client devices connected to this core device are disconnected\. Client devices can reconnect to the core device after a short period of time\.  
Default: `604800` \(7 days\)  
Minimum value: `172800` \(2 days\)  
Maximum value: `864000` \(10 days\)

`performance`  
\(Optional\) The performance configuration options for this core device\. This object contains the following information:    
`maxActiveAuthTokens`  
\(Optional\) The maximum number of active client device authorization tokens\. You can increase this number to enable a greater number of client devices to connect to a single core device without reauthenticating them\.  
Default: `2500`  
`cloudRequestQueueSize`  
\(Optional\) The maximum number of AWS Cloud requests to queue before this component rejects requests\.  
Default: `100`  
`maxConcurrentCloudRequests`  
\(Optional\) The maximum number of concurrent requests to send to the AWS Cloud\. You can increase this number to improve authentication performance on core devices where you connect large numbers of client devices\.  
Default: `1`

**Example: Configuration merge update \(using a restrictive policy\)**  <a name="client-device-auth-component-configuration-example-restrictive"></a>
The following example configuration specifies to allow client devices whose names start with `MyClientDevice` to connect and publish/subscribe on all topics\.  

```
{
  "deviceGroups": {
    "formatVersion": "2021-03-05",
    "definitions": {
      "MyDeviceGroup": {
        "selectionRule": "thingName: MyClientDevice*",
        "policyName": "MyRestrictivePolicy"
      }
    },
    "policies": {
      "MyRestrictivePolicy": {
        "AllowConnect": {
          "statementDescription": "Allow client devices to connect.",
          "operations": [
            "mqtt:connect"
          ],
          "resources": [
            "*"
          ]
        },
        "AllowPublish": {
          "statementDescription": "Allow client devices to publish on test/topic.",
          "operations": [
            "mqtt:publish"
          ],
          "resources": [
            "mqtt:topic:test/topic"
          ]
        },
        "AllowSubscribe": {
          "statementDescription": "Allow client devices to subscribe to test/topic/response.",
          "operations": [
            "mqtt:subscribe"
          ],
          "resources": [
            "mqtt:topicfilter:test/topic/response"
          ]
        }
      }
    }
  }
}
```

**Example: Configuration merge update \(using a permissive policy\)**  <a name="client-device-auth-component-configuration-example-permissive"></a>
The following example configuration specifies to allow all client devices to connect and publish/subscribe on all topics\.  

```
{
  "deviceGroups": {
    "formatVersion": "2021-03-05",
    "definitions": {
      "MyPermissiveDeviceGroup": {
        "selectionRule": "thingName: *",
        "policyName": "MyPermissivePolicy"
      }
    },
    "policies": {
      "MyPermissivePolicy": {
        "AllowAll": {
          "statementDescription": "Allow client devices to perform all actions.",
          "operations": [
            "*"
          ],
          "resources": [
            "*"
          ]
        }
      }
    }
  }
}
```

------
#### [ v2\.1\.x ]

`deviceGroups`  <a name="client-device-auth-component-configuration-devicegroups"></a>
Device groups are groups of client devices that have permissions to connect and communicate with a core device\. Use selection rules to identify groups of client devices, and define *client device authorization policies* that specify the permissions for each device group\.  
This object contains the following information:    
`formatVersion`  
The format version for this configuration object\.  
Choose from the following options:  
+ `2021-03-05`  
`definitions`  
The device groups for this core device\. Each definition specifies a *selection rule* to evaluate if a client device is a member of the group\. Each definition also specifies the permissions policy to apply to client devices that match the selection rule\. If a client device is a member of multiple device groups, the device's permissions are comprised of each group's permissions policy\.  
This object contains the following information:    
`groupNameKey`  
The name of this device group\. Replace *groupNameKey* with a name that helps you identify this device group\.  
This object contains the following information:    
`selectionRule`  
The query that specifies which client devices are members of this device group\. When a client device connects, the core device evaluates this selection rule to determine if the client device is a member of this device group\. If the client device is a member, the core device uses this device group's policy to authorize the client device's actions\.  
Each selection rule comprises at least one *selection rule clause*, which is a single expression query that can match client devices\. Selection rules use the same query syntax as AWS IoT fleet indexing\. For more information about selection rule syntax, see [AWS IoT fleet indexing query syntax](https://docs.aws.amazon.com/iot/latest/developerguide/query-syntax.html) in the *AWS IoT Core Developer Guide*\.  
Use the `*` wildcard to match multiple client devices with one selection rule clause\. You can use this wildcard at the end of the thing name to match client devices whose names start with a string that you specify\. You can also use this wildcard to match all client devices\.  
To select a value that contains a colon character \(`:`\), escape the colon with a backslash character \(`\`\)\. In formats such as JSON, you must escape backslash characters, so you enter two backslash characters before the colon character\. For example, specify `thingName: MyTeam\\:ClientDevice1` to select a thing whose name is `MyTeam:ClientDevice1`\.
You can specify the following selector:  
+ `thingName` – The name of a client device's AWS IoT thing\.

**Example selection rule**  
The following selection rule matches client devices whose names are `MyClientDevice1` or `MyClientDevice2`\.  

```
thingName: MyClientDevice1 OR thingName: MyClientDevice2
```

**Example selection rule \(use wildcards\)**  
The following selection rule matches client devices whose names start with `MyClientDevice`\.  

```
thingName: MyClientDevice*
```

**Example selection rule \(match all devices\)**  
The following selection rule matches all client devices\.  

```
thingName: *
```  
`policyName`  
The permissions policy that applies to client devices in this device group\. Specify the name of a policy that you define in the `policies` object\.  
`policies`  
The client device authorization policies for client devices that connect to the core device\. Each authorization policy specifies a set of actions and the resources where a client device can perform those actions\.  
This object contains the following information:    
`policyNameKey`  
The name of this authorization policy\. Replace *policyNameKey* with a name that helps you identify this authorization policy\. You use this policy name to define which policy applies to a device group\.  
This object contains the following information:    
`statementNameKey`  
The name of this policy statement\. Replace *statementNameKey* with a name that helps you identify this policy statement\.  
This object contains the following information:    
`operations`  
The list of operations to allow  for the resources in this policy\.  
You can include any of the following operations:  
+ `mqtt:connect` – Grants permission to connect to the core device\. Client devices must have this permission to connect to a core device\.

  This operation supports the following resources:
  + `mqtt:clientId:deviceClientId` – Restrict access based on the client ID that a client device uses to connect to the core device's MQTT broker\. Replace *deviceClientId* with the client ID to use\.
+ `mqtt:publish` – Grants permission to publish MQTT messages to topics\.

  This operation supports the following resources:
  + <a name="client-device-authorization-policy-resource-mqtt-topic"></a>`mqtt:topic:mqttTopic` – Restrict access based on the MQTT topic where a client device publishes a message\. Replace *mqttTopic* with the topic to use\.

    This resource doesn't support MQTT topic wildcards\.
+ `mqtt:subscribe` – Grants permission to subscribe to MQTT topic filters to receive messages\.

  This operation supports the following resources:
  + <a name="client-device-authorization-policy-resource-mqtt-topicfilter"></a>`mqtt:topicfilter:mqttTopicFilter` – Restrict access based on the MQTT topics where a client device can subscribe to messages\. Replace *mqttTopicFilter* with the topic filter to use\.

    This resource supports the `+` and `#` MQTT topic wildcards\. For more information, see [MQTT topics](https://docs.aws.amazon.com/iot/latest/developerguide/topics.html) in the *AWS IoT Core Developer Guide*\.

    The client device can subscribe to the exact topic filters that you allow\. For example, if you allow the client device to subscribe to the `mqtt:topicfilter:client/+/status` resource, the client device can subscribe to `client/+/status` but not `client/client1/status`\.
You can specify the `*` wildcard to allow access to all actions\.  
`resources`  
The list of resources to allow  for the operations in this policy\. Specify resources that correspond to the operations in this policy\. For example, you might specify a list of MQTT topic resources \(`mqtt:topic:mqttTopic`\) in a policy that specifies the `mqtt:publish` operation\.  
You can specify the `*` wildcard to allow access to all resources\. You can't use the `*` wildcard to match partial resource identifiers\. For example, you can specify **"resources": "\*"**, but you can't specify **"resources": "mqtt:clientId:\*"**\.  
`statementDescription`  
\(Optional\) A description for this policy statement\.

`certificates`  <a name="client-device-auth-component-configuration-certificates"></a>
\(Optional\) The certificate configuration options for this core device\. This object contains the following information:    
`serverCertificateValiditySeconds`  
\(Optional\) The amount of time \(in seconds\) after which the local MQTT server certificate expires\. You can configure this option to customize how often client devices disconnect and reconnect to the core device\.  
This component rotates the local MQTT server certificate 24 hours before it expires\. The MQTT broker, such as the [Moquette MQTT broker component](mqtt-broker-moquette-component.md), generates a new certificate and restarts\. When this happens, all client devices connected to this core device are disconnected\. Client devices can reconnect to the core device after a short period of time\.  
Default: `604800` \(7 days\)  
Minimum value: `172800` \(2 days\)  
Maximum value: `864000` \(10 days\)

**Example: Configuration merge update \(using a restrictive policy\)**  <a name="client-device-auth-component-configuration-example-restrictive"></a>
The following example configuration specifies to allow client devices whose names start with `MyClientDevice` to connect and publish/subscribe on all topics\.  

```
{
  "deviceGroups": {
    "formatVersion": "2021-03-05",
    "definitions": {
      "MyDeviceGroup": {
        "selectionRule": "thingName: MyClientDevice*",
        "policyName": "MyRestrictivePolicy"
      }
    },
    "policies": {
      "MyRestrictivePolicy": {
        "AllowConnect": {
          "statementDescription": "Allow client devices to connect.",
          "operations": [
            "mqtt:connect"
          ],
          "resources": [
            "*"
          ]
        },
        "AllowPublish": {
          "statementDescription": "Allow client devices to publish on test/topic.",
          "operations": [
            "mqtt:publish"
          ],
          "resources": [
            "mqtt:topic:test/topic"
          ]
        },
        "AllowSubscribe": {
          "statementDescription": "Allow client devices to subscribe to test/topic/response.",
          "operations": [
            "mqtt:subscribe"
          ],
          "resources": [
            "mqtt:topicfilter:test/topic/response"
          ]
        }
      }
    }
  }
}
```

**Example: Configuration merge update \(using a permissive policy\)**  <a name="client-device-auth-component-configuration-example-permissive"></a>
The following example configuration specifies to allow all client devices to connect and publish/subscribe on all topics\.  

```
{
  "deviceGroups": {
    "formatVersion": "2021-03-05",
    "definitions": {
      "MyPermissiveDeviceGroup": {
        "selectionRule": "thingName: *",
        "policyName": "MyPermissivePolicy"
      }
    },
    "policies": {
      "MyPermissivePolicy": {
        "AllowAll": {
          "statementDescription": "Allow client devices to perform all actions.",
          "operations": [
            "*"
          ],
          "resources": [
            "*"
          ]
        }
      }
    }
  }
}
```

------
#### [ v2\.0\.x ]

`deviceGroups`  <a name="client-device-auth-component-configuration-devicegroups"></a>
Device groups are groups of client devices that have permissions to connect and communicate with a core device\. Use selection rules to identify groups of client devices, and define *client device authorization policies* that specify the permissions for each device group\.  
This object contains the following information:    
`formatVersion`  
The format version for this configuration object\.  
Choose from the following options:  
+ `2021-03-05`  
`definitions`  
The device groups for this core device\. Each definition specifies a *selection rule* to evaluate if a client device is a member of the group\. Each definition also specifies the permissions policy to apply to client devices that match the selection rule\. If a client device is a member of multiple device groups, the device's permissions are comprised of each group's permissions policy\.  
This object contains the following information:    
`groupNameKey`  
The name of this device group\. Replace *groupNameKey* with a name that helps you identify this device group\.  
This object contains the following information:    
`selectionRule`  
The query that specifies which client devices are members of this device group\. When a client device connects, the core device evaluates this selection rule to determine if the client device is a member of this device group\. If the client device is a member, the core device uses this device group's policy to authorize the client device's actions\.  
Each selection rule comprises at least one *selection rule clause*, which is a single expression query that can match client devices\. Selection rules use the same query syntax as AWS IoT fleet indexing\. For more information about selection rule syntax, see [AWS IoT fleet indexing query syntax](https://docs.aws.amazon.com/iot/latest/developerguide/query-syntax.html) in the *AWS IoT Core Developer Guide*\.  
Use the `*` wildcard to match multiple client devices with one selection rule clause\. You can use this wildcard at the end of the thing name to match client devices whose names start with a string that you specify\. You can also use this wildcard to match all client devices\.  
To select a value that contains a colon character \(`:`\), escape the colon with a backslash character \(`\`\)\. In formats such as JSON, you must escape backslash characters, so you enter two backslash characters before the colon character\. For example, specify `thingName: MyTeam\\:ClientDevice1` to select a thing whose name is `MyTeam:ClientDevice1`\.
You can specify the following selector:  
+ `thingName` – The name of a client device's AWS IoT thing\.

**Example selection rule**  
The following selection rule matches client devices whose names are `MyClientDevice1` or `MyClientDevice2`\.  

```
thingName: MyClientDevice1 OR thingName: MyClientDevice2
```

**Example selection rule \(use wildcards\)**  
The following selection rule matches client devices whose names start with `MyClientDevice`\.  

```
thingName: MyClientDevice*
```

**Example selection rule \(match all devices\)**  
The following selection rule matches all client devices\.  

```
thingName: *
```  
`policyName`  
The permissions policy that applies to client devices in this device group\. Specify the name of a policy that you define in the `policies` object\.  
`policies`  
The client device authorization policies for client devices that connect to the core device\. Each authorization policy specifies a set of actions and the resources where a client device can perform those actions\.  
This object contains the following information:    
`policyNameKey`  
The name of this authorization policy\. Replace *policyNameKey* with a name that helps you identify this authorization policy\. You use this policy name to define which policy applies to a device group\.  
This object contains the following information:    
`statementNameKey`  
The name of this policy statement\. Replace *statementNameKey* with a name that helps you identify this policy statement\.  
This object contains the following information:    
`operations`  
The list of operations to allow  for the resources in this policy\.  
You can include any of the following operations:  
+ `mqtt:connect` – Grants permission to connect to the core device\. Client devices must have this permission to connect to a core device\.

  This operation supports the following resources:
  + `mqtt:clientId:deviceClientId` – Restrict access based on the client ID that a client device uses to connect to the core device's MQTT broker\. Replace *deviceClientId* with the client ID to use\.
+ `mqtt:publish` – Grants permission to publish MQTT messages to topics\.

  This operation supports the following resources:
  + <a name="client-device-authorization-policy-resource-mqtt-topic"></a>`mqtt:topic:mqttTopic` – Restrict access based on the MQTT topic where a client device publishes a message\. Replace *mqttTopic* with the topic to use\.

    This resource doesn't support MQTT topic wildcards\.
+ `mqtt:subscribe` – Grants permission to subscribe to MQTT topic filters to receive messages\.

  This operation supports the following resources:
  + <a name="client-device-authorization-policy-resource-mqtt-topicfilter"></a>`mqtt:topicfilter:mqttTopicFilter` – Restrict access based on the MQTT topics where a client device can subscribe to messages\. Replace *mqttTopicFilter* with the topic filter to use\.

    This resource supports the `+` and `#` MQTT topic wildcards\. For more information, see [MQTT topics](https://docs.aws.amazon.com/iot/latest/developerguide/topics.html) in the *AWS IoT Core Developer Guide*\.

    The client device can subscribe to the exact topic filters that you allow\. For example, if you allow the client device to subscribe to the `mqtt:topicfilter:client/+/status` resource, the client device can subscribe to `client/+/status` but not `client/client1/status`\.
You can specify the `*` wildcard to allow access to all actions\.  
`resources`  
The list of resources to allow  for the operations in this policy\. Specify resources that correspond to the operations in this policy\. For example, you might specify a list of MQTT topic resources \(`mqtt:topic:mqttTopic`\) in a policy that specifies the `mqtt:publish` operation\.  
You can specify the `*` wildcard to allow access to all resources\. You can't use the `*` wildcard to match partial resource identifiers\. For example, you can specify **"resources": "\*"**, but you can't specify **"resources": "mqtt:clientId:\*"**\.  
`statementDescription`  
\(Optional\) A description for this policy statement\.

**Example: Configuration merge update \(using a restrictive policy\)**  <a name="client-device-auth-component-configuration-example-restrictive"></a>
The following example configuration specifies to allow client devices whose names start with `MyClientDevice` to connect and publish/subscribe on all topics\.  

```
{
  "deviceGroups": {
    "formatVersion": "2021-03-05",
    "definitions": {
      "MyDeviceGroup": {
        "selectionRule": "thingName: MyClientDevice*",
        "policyName": "MyRestrictivePolicy"
      }
    },
    "policies": {
      "MyRestrictivePolicy": {
        "AllowConnect": {
          "statementDescription": "Allow client devices to connect.",
          "operations": [
            "mqtt:connect"
          ],
          "resources": [
            "*"
          ]
        },
        "AllowPublish": {
          "statementDescription": "Allow client devices to publish on test/topic.",
          "operations": [
            "mqtt:publish"
          ],
          "resources": [
            "mqtt:topic:test/topic"
          ]
        },
        "AllowSubscribe": {
          "statementDescription": "Allow client devices to subscribe to test/topic/response.",
          "operations": [
            "mqtt:subscribe"
          ],
          "resources": [
            "mqtt:topicfilter:test/topic/response"
          ]
        }
      }
    }
  }
}
```

**Example: Configuration merge update \(using a permissive policy\)**  <a name="client-device-auth-component-configuration-example-permissive"></a>
The following example configuration specifies to allow all client devices to connect and publish/subscribe on all topics\.  

```
{
  "deviceGroups": {
    "formatVersion": "2021-03-05",
    "definitions": {
      "MyPermissiveDeviceGroup": {
        "selectionRule": "thingName: *",
        "policyName": "MyPermissivePolicy"
      }
    },
    "policies": {
      "MyPermissivePolicy": {
        "AllowAll": {
          "statementDescription": "Allow client devices to perform all actions.",
          "operations": [
            "*"
          ],
          "resources": [
            "*"
          ]
        }
      }
    }
  }
}
```

------

## Local log file<a name="client-device-auth-component-log-file"></a>

This component uses the same log file as the [Greengrass nucleus](greengrass-nucleus-component.md) component\.

------
#### [ Linux ]

```
/greengrass/v2/logs/greengrass.log
```

------
#### [ Windows ]

```
C:\greengrass\v2\logs\greengrass.log
```

------

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux ]

  ```
  sudo tail -f /greengrass/v2/logs/greengrass.log
  ```

------
#### [ Windows \(PowerShell\) ]

  ```
  Get-Content C:\greengrass\v2\logs\greengrass.log -Tail 10 -Wait
  ```

------

## Changelog<a name="client-device-auth-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.3\.2  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/client-device-auth-component.html)  | 
|  2\.3\.1  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/client-device-auth-component.html)  | 
|  2\.3\.0  |   This version is no longer available\. The improvements in this version are available in later versions of this component\.  New features [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/client-device-auth-component.html)  | 
|  2\.2\.3  | Version updated for Greengrass nucleus version 2\.8\.0 release\. | 
|  2\.3\.0  |  New features [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/client-device-auth-component.html)  | 
|  2\.2\.3  | Version updated for Greengrass nucleus version 2\.8\.0 release\. | 
|  2\.2\.2  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/client-device-auth-component.html)  | 
|  2\.2\.1  |  Version updated for Greengrass nucleus version 2\.7\.0 release\.  | 
|  2\.2\.0  |  <a name="changelog-client-device-auth-2.2.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/client-device-auth-component.html)  | 
|  2\.1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/client-device-auth-component.html)  | 
|  2\.0\.4  |  Version updated for Greengrass nucleus version 2\.5\.0 release\.  | 
|  2\.0\.3  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/client-device-auth-component.html)  | 
|  2\.0\.2  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.1  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.0  |  Initial version\.  | 