# Manage core device endpoints<a name="manage-core-device-endpoints"></a>

When you use cloud discovery, you store MQTT broker endpoints for core devices in the AWS IoT Greengrass cloud service\. Client devices connect to AWS IoT Greengrass to retrieve these endpoints and other information for their associated core devices\.

For each core device, you can choose to automatically or manually manage endpoints\.
+ **Automatically manage endpoints with IP detector**

  You can deploy the [IP detector component](ip-detector-component.md) the automatically manage core device endpoints for you if you have a non\-complex network setup, such as where the client devices are on the same network as the core device\. You can't use the IP detector component if the core device is behind a router that forwards the MQTT broker port to the core device, for example\.

  The IP detector component is also useful if you deploy to thing groups, because it manages the endpoints for all core devices in the thing group\. For more information, see [Use IP detector to automatically manage endpoints](#use-ip-detector)\.
**Note**  
If you use the IP detector component to manage endpoints, you must use the default port 8883 for the [Moquette MQTT broker component](mqtt-broker-moquette-component.md)\.
+ **Manually manage endpoints**

  If you can't use the IP detector component, you must manually manage core device endpoints\. You can update these endpoints with the console or the API\. For more information, see [Manually manage endpoints](#manually-manage-endpoints)\.

**Topics**
+ [Use IP detector to automatically manage endpoints](#use-ip-detector)
+ [Manually manage endpoints](#manually-manage-endpoints)

## Use IP detector to automatically manage endpoints<a name="use-ip-detector"></a>

If you have a simple network setup, such as the client devices on the same network as the core device, you can deploy the [IP detector component](ip-detector-component.md) to do the following:
+ Monitor the Greengrass core device's local network connectivity information\. This information includes the core device's network endpoints and the port where the MQTT broker operates\.
+ Report the core device's connectivity information to the AWS IoT Greengrass cloud service\.

The core device's AWS IoT policy must allow the `greengrass:UpdateConnectivityInfo` permission to use the IP detector component\. For more information, see [AWS IoT policies for data plane operations](device-auth.md#iot-policies) and [Minimal AWS IoT policy to support client devices](device-auth.md#client-device-support-minimal-iot-policy)\.

The IP detector component overwrites endpoints that you set manually\.

You can do either of the following to deploy the IP detector component:
+ Use the **Configure discovery** page in the console\. For more information, see [Configure cloud discovery \(console\)](connect-client-devices.md#configure-cloud-discovery-console)\.
+ Create and revise deployments to include the IP detector\. You can use the console, AWS CLI, or AWS API to manage deployments\. For more information, see [Create deployments](create-deployments.md)\.

## Manually manage endpoints<a name="manually-manage-endpoints"></a>

You can manually manage MQTT broker endpoints for core devices\.

Each MQTT broker endpoint has the following information:

**Endpoint** \(`HostAddress`\)  
An IP address or DNS address where client devices can connect to an MQTT broker on the core device\.

**Port** \(`PortNumber`\)  
The port where the MQTT broker operates on the core device\.  
You can configure this port on the [Moquette MQTT broker component](mqtt-broker-moquette-component.md), which defaults to use port 8883\.

**Metadata** \(`Metadata`\)  
Additional metadata to provide to client devices that connect to this endpoint\.

**Topics**
+ [Manage endpoints \(console\)](#manually-manage-endpoints-console)
+ [Manage endpoints \(AWS CLI\)](#manually-manage-endpoints-cli)
+ [Manage endpoints \(API\)](#manually-manage-endpoints-api)

### Manage endpoints \(console\)<a name="manually-manage-endpoints-console"></a>

You can use the AWS IoT Greengrass console to view, update, and remove endpoints for a core device\.

**To manage endpoints for a core device \(console\)**

1. <a name="navigate-greengrass-console"></a>Navigate to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. Choose **Core devices**\.

1. Choose the core device to manage\.

1. On the core device's details page, choose the **Client devices** tab\.

1. In the **MQTT broker endpoints** section, you can see the core device's MQTT broker endpoints\. Choose **Manage endpoints**\.

1. In the **Manage endpoints** modal, add or remove MQTT broker endpoints for the core device\.

1. Choose **Update**\.

### Manage endpoints \(AWS CLI\)<a name="manually-manage-endpoints-cli"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to manage endpoints for a core device\.

**Note**  
Because client device support in AWS IoT Greengrass V2 is backward compatible with AWS IoT Greengrass V1, you use AWS IoT Greengrass V1 API operations to manage core device endpoints\.

**To get endpoints for a core device \(AWS CLI\)**
+ Use the following command: [get\-connectivity\-info](https://docs.aws.amazon.com/cli/latest/reference/greengrass/get-connectivity-info.html)\.

**To update endpoints for a core device \(AWS CLI\)**
+ Use the following command: [update\-connectivity\-info](https://docs.aws.amazon.com/cli/latest/reference/greengrass/update-connectivity-info.html)\.

### Manage endpoints \(API\)<a name="manually-manage-endpoints-api"></a>

You can use the AWS API to manage endpoints for a core device\.

**Note**  
Because client device support in AWS IoT Greengrass V2 is backward compatible with AWS IoT Greengrass V1, you use AWS IoT Greengrass V1 API operations to manage endpoints\.

**To get endpoints for a core device \(AWS API\)**
+ Use the following operation: [GetConnectivityInfo](https://docs.aws.amazon.com/greengrass/v1/apireference/getconnectivityinfo-get.html)\.

**To update endpoints for a core device \(AWS API\)**
+ Use the following command: [UpdateConnectivityInfo](https://docs.aws.amazon.com/greengrass/v1/apireference/updateconnectivityinfo-put.html)\.