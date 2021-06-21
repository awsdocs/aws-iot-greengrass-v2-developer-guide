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
+ [Requirements](#ip-detector-component-requirements)
+ [Dependencies](#ip-detector-component-dependencies)
+ [Configuration](#ip-detector-component-configuration)
+ [Changelog](#ip-detector-component-changelog)

## Versions<a name="ip-detector-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Requirements<a name="ip-detector-component-requirements"></a>

This component has the following requirements:
+ The core device's AWS IoT policy must allow the `greengrass:UpdateConnectivityInfo` permission\. For more information, see [AWS IoT policies for data plane operations](device-auth.md#iot-policies) and [Minimal AWS IoT policy to support client devices](device-auth.md#client-device-support-minimal-iot-policy)\.
+ The core device's MQTT broker component, such as the [Moquette MQTT broker](mqtt-broker-moquette-component.md), must run on port 8883\.
+ If you have a complex network setup, the IP detector component might not be able to identify the endpoints where client devices can connect to the core device\. If the IP detector component can't manage the endpoints, you must manually manage the core device endpoints instead\. For example, if the core device is behind a router that forwards the MQTT broker port to it, you must specify the router's IP address as an endpoint for the core device\. For more information, see [Manage core device endpoints](manage-core-device-endpoints.md)\.

## Dependencies<a name="ip-detector-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#ip-detector-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for version 2\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | \~2\.2\.0 | Soft | 

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="ip-detector-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`includeIPv4LoopbackAddrs`  
\(Optional\) You can enable this option to detect and report IPv4 loopback addresses, which are IP addresses where a device can communicate with itself, such as `localhost`\. Use this option in test environments where the core device and client device run on the same system\.  
Default: `false`

`includeIPv4LinkLocalAddrs`  
\(Optional\) You can enable this option to detect and report IPv4 [link\-local addresses](https://en.wikipedia.org/wiki/Link-local_address)\. Use this option if the core device's network doesn't have Dynamic Host Configuration Protocol \(DHCP\) or statically assigned IP addresses\.  
Default: `false`

## Changelog<a name="ip-detector-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.0  |  Initial version\.  | 