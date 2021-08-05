# Client device auth<a name="client-device-auth-component"></a>

The client device auth component \(`aws.greengrass.clientdevices.Auth`\) authenticates client devices and authorizes client device actions\.

**Note**  <a name="client-device-component-context"></a>
Client devices are local IoT devices that connect to a Greengrass core device to send MQTT messages and data to process\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

**Topics**
+ [Versions](#client-device-auth-component-versions)
+ [Requirements](#client-device-auth-component-requirements)
+ [Dependencies](#client-device-auth-component-dependencies)
+ [Configuration](#client-device-auth-component-configuration)
+ [Changelog](#client-device-auth-component-changelog)

## Versions<a name="client-device-auth-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Requirements<a name="client-device-auth-component-requirements"></a>

This component has the following requirements:
+ The [Greengrass service role](greengrass-service-role.md) must be associated to your AWS account and allow the `iot:DescribeCertificate` permission\.
+ The core device's AWS IoT policy must allow the following permissions:
  + `greengrass:PutCertificateAuthorities`
  + `greengrass:VerifyClientDeviceIdentity`
  + `greengrass:VerifyClientDeviceIoTCertificateAssociation`
  + `greengrass:GetConnectivityInfo`

  For more information, see [AWS IoT policies for data plane operations](device-auth.md#iot-policies) and [Minimal AWS IoT policy to support client devices](device-auth.md#client-device-support-minimal-iot-policy)\.

## Dependencies<a name="client-device-auth-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#client-device-auth-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.0\.2 ]

The following table lists the dependencies for version 2\.0\.2 of this component\.


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

`deviceGroups`  
Device groups are groups of client devices that have permissions to connect and communicate with a core device\. Use selection rules to identify groups of client devices, and define authorization policies that specify the permissions for each device group\.  
This object contains the following information:    
`formatVersion`  
The format version for this configuration object\.  
Choose from the following options:  
+ `2021-03-05`  
`definitions`  
The device groups for this core device\. Each definition specifies a selection rule to evaluate if a client device is a member of the group\. Each definition also specifies the permissions policy to apply to client devices who match the selection rule\. If a client device is a member of multiple device groups, the device's permissions are the union of each group's permissions policy\.  
This object contains the following information:    
`groupNameKey`  
The name of this device group\. Replace *groupNameKey* with a name that helps you identify this device group\.  
This object contains the following information:    
`selectionRule`  
The selection rule that specifies which client devices are members of this device group\. When a client device connects, the core device evaluates the selection rule to determine if the client device is a member of this device group\. If the client device is a member, the core device uses this device group's policy to authorize the client device's actions\.  
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
The device group authorization policies for client devices that connect to the core device\. Each authorization policy specifies a set of actions and the resources on which a client device can perform those actions\.  
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

**Example: Configuration merge update \(using a restrictive policy\)**  
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

**Example: Configuration merge update \(using a permissive policy\)**  
The following example configuration specifies to allow client devices whose names start with `MyClientDevice` to connect and publish/subscribe on all topics\.  

```
{
  "deviceGroups": {
    "formatVersion": "2021-03-05",
    "definitions": {
      "MyDeviceGroup": {
        "selectionRule": "thingName: MyClientDevice*",
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

## Changelog<a name="client-device-auth-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.2  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.1  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.0  |  Initial version\.  | 