# MQTT broker \(Moquette\)<a name="mqtt-broker-moquette-component"></a>

The Moquette MQTT broker component \(`aws.greengrass.clientdevices.mqtt.Moquette`\) handles MQTT messages between client devices and a Greengrass core device\. This component provides a modified version of the [Moquette MQTT broker](https://github.com/moquette-io/moquette)\.

This broker implements the MQTT 3\.1\.1 protocol\. It includes support for QoS 0, QoS 1, QoS 2 retained messages, last will messages, and persistent subscriptions\.

**Note**  <a name="client-device-component-context"></a>
Client devices are local IoT devices that connect to a Greengrass core device to send MQTT messages and data to process\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

**Topics**
+ [Versions](#mqtt-broker-moquette-component-versions)
+ [Type](#mqtt-broker-moquette-component-type)
+ [Requirements](#mqtt-broker-moquette-component-requirements)
+ [Dependencies](#mqtt-broker-moquette-component-dependencies)
+ [Configuration](#mqtt-broker-moquette-component-configuration)
+ [Local log file](#mqtt-broker-moquette-component-log-file)
+ [Changelog](#mqtt-broker-moquette-component-changelog)

## Versions<a name="mqtt-broker-moquette-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="mqtt-broker-moquette-component-type"></a>

<a name="public-component-type-plugin-para1"></a>This component is a plugin component \(`aws.greengrass.plugin`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component in the same Java Virtual Machine \(JVM\) as the nucleus\. The nucleus restarts when you change this component's version on the core device\.

<a name="public-component-type-plugin-para2"></a>This component uses the same log file as the Greengrass nucleus\. For more information, see [View AWS IoT Greengrass Core software logs](troubleshooting.md#view-greengrass-core-logs)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](manage-components.md#component-types)\.

## Requirements<a name="mqtt-broker-moquette-component-requirements"></a>

This component has the following requirements:
+ The core device must be able to accept connections on the port where the MQTT broker operates\. This component runs the MQTT broker on port 8883 by default\. You can specify a different port when you configure this component\.
+ <a name="ip-detector-mqtt-broker-port-requirement"></a>If you use the [IP detector component](ip-detector-component.md) to manage MQTT broker endpoints, you must use the default port 8883 for this component\.

## Dependencies<a name="mqtt-broker-moquette-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#mqtt-broker-moquette-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.0\.x ]

The following table lists the dependencies for version 2\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Client device auth](client-device-auth-component.md) | >=2\.0\.0 <2\.1\.0 | Hard | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="mqtt-broker-moquette-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`moquette`  
\(Optional\) The [Moquette MQTT broker](https://github.com/moquette-io/moquette) configuration to use\. You can configure a subset of Moqeutte configuration options in this component\. For more information, see the inline comments in the [Moquette configuration file](https://github.com/moquette-io/moquette/blob/master/distribution/src/main/resources/moquette.conf)\.  
This object contains the following information:    
`ssl_port`  
\(Optional\) The port where the MQTT broker operates\.  
<a name="ip-detector-mqtt-broker-port-requirement"></a>If you use the [IP detector component](ip-detector-component.md) to manage MQTT broker endpoints, you must use the default port 8883 for this component\.
Default: `8883`  
`host`  
\(Optional\) The interface where the MQTT broker binds\. For example, you might change this parameter so that the MQTT broker binds only to a specific local network\.  
Default: `0.0.0.0` \(binds to all network interfaces\)

**Example: Configuration merge update**  
The following example configuration specifies to operate the MQTT broker on port 443\.  

```
{
  "moquette": {
    "ssl_port": "443"
  }
}
```

## Local log file<a name="mqtt-broker-moquette-component-log-file"></a>

This component uses the same log file as the [Greengrass nucleus](greengrass-nucleus-component.md) component\.

```
/greengrass/v2/logs/greengrass.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/greengrass.log
  ```

## Changelog<a name="mqtt-broker-moquette-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.1  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.0  |  Initial version\.  | 