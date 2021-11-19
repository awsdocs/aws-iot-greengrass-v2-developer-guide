# IP detector<a name="ip-detector-component"></a>

The IP detector component \(`aws.greengrass.clientdevices.IPDetector`\) does the following:
+ Monitors the Greengrass core device's network connectivity information\. This information includes the core device's network endpoints and the port where an MQTT broker operates\.
+ Updates the core device's connectivity information in the AWS IoT Greengrass cloud service\.

Client devices can use Greengrass cloud discovery to retrieve associated core devices' connectivity information\. Then, client devices can try to connect to each core device until they successfully connect\.

**Note**  <a name="client-device-component-context"></a>
Client devices are local IoT devices that connect to a Greengrass core device to send MQTT messages and data to process\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

The IP detector component replaces a core device's existing connectivity information with the information it detects\. Because this component removes existing information, you can either use the IP detector component, or manually manage connectivity information\.

**Note**  
The IP detector component detects only IPv4 addresses\.

**Topics**
+ [Versions](#ip-detector-component-versions)
+ [Type](#ip-detector-component-type)
+ [Operating system](#ip-detector-component-os-support)
+ [Requirements](#ip-detector-component-requirements)
+ [Dependencies](#ip-detector-component-dependencies)
+ [Configuration](#ip-detector-component-configuration)
+ [Local log file](#ip-detector-component-log-file)
+ [Changelog](#ip-detector-component-changelog)

## Versions<a name="ip-detector-component-versions"></a>

This component has the following versions:
+ 2\.1\.x
+ 2\.0\.x

## Type<a name="ip-detector-component-type"></a>

<a name="public-component-type-plugin-para1"></a>This component is a plugin component \(`aws.greengrass.plugin`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component in the same Java Virtual Machine \(JVM\) as the nucleus\. The nucleus restarts when you change this component's version on the core device\.

<a name="public-component-type-plugin-para2"></a>This component uses the same log file as the Greengrass nucleus\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="ip-detector-component-os-support"></a>

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

## Requirements<a name="ip-detector-component-requirements"></a>

This component has the following requirements:
+ The [Greengrass service role](greengrass-service-role.md) must be associated to your AWS account and allow the `iot:GetThingShadow` and `iot:UpdateThingShadow` permissions\.
+ The core device's AWS IoT policy must allow the `greengrass:UpdateConnectivityInfo` permission\. For more information, see [AWS IoT policies for data plane operations](device-auth.md#iot-policies) and [Minimal AWS IoT policy to support client devices](device-auth.md#client-device-support-minimal-iot-policy)\.
+ If you configure the core device's MQTT broker component to use a port other than the default port 8883, you must use IP detector v2\.1\.0 or later\. Configure it to report the port where the broker operates\.
+ If you have a complex network setup, the IP detector component might not be able to identify the endpoints where client devices can connect to the core device\. If the IP detector component can't manage the endpoints, you must manually manage the core device endpoints instead\. For example, if the core device is behind a router that forwards the MQTT broker port to it, you must specify the router's IP address as an endpoint for the core device\. For more information, see [Manage core device endpoints](manage-core-device-endpoints.md)\.

## Dependencies<a name="ip-detector-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#ip-detector-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.1\.1 ]

The following table lists the dependencies for version 2\.1\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.2\.0 <2\.6\.0 | Soft | 

------
#### [ 2\.1\.0 and 2\.0\.2 ]

The following table lists the dependencies for versions 2\.1\.0 and 2\.0\.2 of this component\.


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

## Configuration<a name="ip-detector-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ 2\.1\.x ]

`defaultPort`  
\(Optional\) The MQTT broker port to report when this component detects IP addresses\. You must specify this parameter if you configure the MQTT broker to use a different port than the default port 8883\.  
Default: `8883`

`includeIPv4LoopbackAddrs`  <a name="ip-detector-component-configuration-include-ipv4-loopback-addrs"></a>
\(Optional\) You can enable this option to detect and report IPv4 loopback addresses\. These are IP addresses, such as `localhost`, where a device can communicate with itself\. Use this option in test environments where the core device and client device run on the same system\.  
Default: `false`

`includeIPv4LinkLocalAddrs`  <a name="ip-detector-component-configuration-include-ipv4-link-local-addrs"></a>
\(Optional\) You can enable this option to detect and report IPv4 [link\-local addresses](https://en.wikipedia.org/wiki/Link-local_address)\. Use this option if the core device's network doesn't have Dynamic Host Configuration Protocol \(DHCP\) or statically assigned IP addresses\.  
Default: `false`

------
#### [ 2\.0\.x ]

`includeIPv4LoopbackAddrs`  <a name="ip-detector-component-configuration-include-ipv4-loopback-addrs"></a>
\(Optional\) You can enable this option to detect and report IPv4 loopback addresses\. These are IP addresses, such as `localhost`, where a device can communicate with itself\. Use this option in test environments where the core device and client device run on the same system\.  
Default: `false`

`includeIPv4LinkLocalAddrs`  <a name="ip-detector-component-configuration-include-ipv4-link-local-addrs"></a>
\(Optional\) You can enable this option to detect and report IPv4 [link\-local addresses](https://en.wikipedia.org/wiki/Link-local_address)\. Use this option if the core device's network doesn't have Dynamic Host Configuration Protocol \(DHCP\) or statically assigned IP addresses\.  
Default: `false`

------

## Local log file<a name="ip-detector-component-log-file"></a>

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

## Changelog<a name="ip-detector-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.1\.1  |  Version updated for Greengrass nucleus version 2\.5\.0 release\.  | 
|  2\.1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/ip-detector-component.html)  | 
|  2\.0\.2  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.1  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.0  |  Initial version\.  | 