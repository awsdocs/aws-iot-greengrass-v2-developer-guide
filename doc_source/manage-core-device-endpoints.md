# Manage core device endpoints<a name="manage-core-device-endpoints"></a>

When you use cloud discovery, you store MQTT broker endpoints for core devices in the AWS IoT Greengrass cloud service\. Client devices connect to AWS IoT Greengrass to retrieve these endpoints and other information for their associated core devices\.

For each core device, you can automatically or manually manage endpoints\.
+ **Automatically manage endpoints with IP detector**

  You can deploy the [IP detector component](ip-detector-component.md) to automatically manage core device endpoints for you if you have a non\-complex network setup, such as where the client devices are on the same network as the core device\. You can't use the IP detector component if the core device is behind a router that forwards the MQTT broker port to the core device, for example\.

  The IP detector component is also useful if you deploy to thing groups, because it manages the endpoints for all core devices in the thing group\. For more information, see [Use IP detector to automatically manage endpoints](#use-ip-detector)\.
+ **Manually manage endpoints**

  If you can't use the IP detector component, you must manually manage core device endpoints\. You can update these endpoints with the console or the API\. For more information, see [Manually manage endpoints](#manually-manage-endpoints)\.

**Topics**
+ [Use IP detector to automatically manage endpoints](#use-ip-detector)
+ [Manually manage endpoints](#manually-manage-endpoints)

## Use IP detector to automatically manage endpoints<a name="use-ip-detector"></a>

If you have a simple network setup, such as the client devices on the same network as the core device, you can deploy the [IP detector component](ip-detector-component.md) to do the following:
+ Monitor the Greengrass core device's local network connectivity information\. This information includes the core device's network endpoints and the port where the MQTT broker operates\.
+ Report the core device's connectivity information to the AWS IoT Greengrass cloud service\.

The IP detector component overwrites endpoints that you set manually\.

**Important**  
The core device's AWS IoT policy must allow the `greengrass:UpdateConnectivityInfo` permission to use the IP detector component\. For more information, see [AWS IoT policies for data plane operations](device-auth.md#iot-policies) and [Configure the AWS IoT thing policy](connect-client-devices.md#configure-iot-policy-requirement)\.

You can do either of the following to deploy the IP detector component:
+ Use the **Configure discovery** page in the console\. For more information, see [Configure cloud discovery \(console\)](connect-client-devices.md#configure-cloud-discovery-console)\.
+ Create and revise deployments to include the IP detector\. You can use the console, AWS CLI, or AWS API to manage deployments\. For more information, see [Create deployments](create-deployments.md)\.

### Deploy the IP detector component \(console\)<a name="deploy-ip-detector-console"></a>

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

1. On the **Components** page, choose the **Public components** tab, and then choose **aws\.greengrass\.clientdevices\.IPDetector**\.

1. On the **aws\.greengrass\.clientdevices\.IPDetector** page, choose **Deploy**\.

1. <a name="deploy-component-choose-deployment-step"></a>From **Add to deployment**, choose an existing deployment to revise, or choose to create a new deployment, and then choose **Next**\.

1. <a name="deploy-component-choose-target-step"></a>If you chose to create a new deployment, choose the target core device or thing group for the deployment\. On the **Specify target** page, under **Deployment target**, choose a core device or thing group, and then choose **Next**\.

1. On the **Select components** page, verify that the **aws\.greengrass\.clientdevices\.IPDetector** component is selected, choose **Next**\.

1. On the **Configure components** page, select **aws\.greengrass\.clientdevices\.IPDetector**, and then do the following:

   1. Choose **Configure component**\.

   1. In the **Configure aws\.greengrass\.clientdevices\.IPDetector** modal, under **Configuration update**, in **Configuration to merge**, you can enter a configuration update to configure the IP detector component\. You can specify any of the following configuration options:
      + `defaultPort` – <a name="ip-detector-component-configuration-default-port-definition"></a>\(Optional\) The MQTT broker port to report when this component detects IP addresses\. You must specify this parameter if you configure the MQTT broker to use a different port than the default port 8883\.
      + `includeIPv4LoopbackAddrs` – <a name="ip-detector-component-configuration-include-ipv4-loopback-addrs-definition"></a>\(Optional\) You can enable this option to detect and report IPv4 loopback addresses\. These are IP addresses, such as `localhost`, where a device can communicate with itself\. Use this option in test environments where the core device and client device run on the same system\.
      + `includeIPv4LinkLocalAddrs` – <a name="ip-detector-component-configuration-include-ipv4-link-local-addrs-definition"></a>\(Optional\) You can enable this option to detect and report IPv4 [link\-local addresses](https://en.wikipedia.org/wiki/Link-local_address)\. Use this option if the core device's network doesn't have Dynamic Host Configuration Protocol \(DHCP\) or statically assigned IP addresses\.

      The configuration update might look similar to the following example\.

      ```
      {
        "defaultPort": "8883",
        "includeIPv4LoopbackAddrs": false,
        "includeIPv4LinkLocalAddrs": false
      }
      ```

   1. Choose **Confirm** to close the modal, and then choose **Next**\.

1. <a name="deploy-component-configure-advanced-settings-step"></a>On the **Configure advanced settings** page, keep the default configuration settings, and choose **Next**\.

1. <a name="deploy-component-review-and-deploy-step"></a>On the **Review** page, choose **Deploy**

### Deploy the IP detector component \(AWS CLI\)<a name="deploy-ip-detector-cli"></a>

To deploy the IP detector component, create a deployment document that includes `aws.greengrass.clientdevices.IPDetector` in the `components` object, and specify the configuration update for the component\. Follow instructions in [Create deployments](create-deployments.md) to create a new deployment or revise an existing deployment\.

You can specify any of the following options to configure the IP detector component when you create the deployment document:
+ `defaultPort` – <a name="ip-detector-component-configuration-default-port-definition"></a>\(Optional\) The MQTT broker port to report when this component detects IP addresses\. You must specify this parameter if you configure the MQTT broker to use a different port than the default port 8883\.
+ `includeIPv4LoopbackAddrs` – <a name="ip-detector-component-configuration-include-ipv4-loopback-addrs-definition"></a>\(Optional\) You can enable this option to detect and report IPv4 loopback addresses\. These are IP addresses, such as `localhost`, where a device can communicate with itself\. Use this option in test environments where the core device and client device run on the same system\.
+ `includeIPv4LinkLocalAddrs` – <a name="ip-detector-component-configuration-include-ipv4-link-local-addrs-definition"></a>\(Optional\) You can enable this option to detect and report IPv4 [link\-local addresses](https://en.wikipedia.org/wiki/Link-local_address)\. Use this option if the core device's network doesn't have Dynamic Host Configuration Protocol \(DHCP\) or statically assigned IP addresses\.

The following example partial deployment document specifies to report port 8883 as the MQTT broker port\.

```
{
  ...,
  "components": {
    ...,
    "aws.greengrass.clientdevices.IPDetector": {
      "componentVersion": "2.1.1",
      "configurationUpdate": {
        "merge": "{\"defaultPort\":\"8883\",}"
      }
    }
  }
}
```

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
Because client device support in AWS IoT Greengrass V2 is backward compatible with AWS IoT Greengrass V1, you can use AWS IoT Greengrass V2 or AWS IoT Greengrass V1 API operations to manage core device endpoints\.

**To get endpoints for a core device \(AWS CLI\)**
+ Use either of the following commands:
  + [greengrassv2: get\-connectivity\-info](https://docs.aws.amazon.com/cli/latest/reference/greengrassv2/get-connectivity-info.html)
  + [greengrass: get\-connectivity\-info](https://docs.aws.amazon.com/cli/latest/reference/greengrass/get-connectivity-info.html)

**To update endpoints for a core device \(AWS CLI\)**
+ Use either of the following commands:
  + [greengrassv2: update\-connectivity\-info](https://docs.aws.amazon.com/cli/latest/reference/greengrassv2/update-connectivity-info.html)
  + [greengrass: update\-connectivity\-info](https://docs.aws.amazon.com/cli/latest/reference/greengrass/update-connectivity-info.html)

### Manage endpoints \(API\)<a name="manually-manage-endpoints-api"></a>

You can use the AWS API to manage endpoints for a core device\.

**Note**  
Because client device support in AWS IoT Greengrass V2 is backward compatible with AWS IoT Greengrass V1, you can use AWS IoT Greengrass V2 or AWS IoT Greengrass V1 API operations to manage core device endpoints\.

**To get endpoints for a core device \(AWS API\)**
+ Use either of the following operations:
  + [V2: GetConnectivityInfo](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_GetConnectivityInfo.html)
  + [V1: GetConnectivityInfo](https://docs.aws.amazon.com/greengrass/v1/apireference/getconnectivityinfo-get.html)

**To update endpoints for a core device \(AWS API\)**
+ Use either of the following operations:
  + [V2: UpdateConnectivityInfo](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_UpdateConnectivityInfo.html)
  + [V1: UpdateConnectivityInfo](https://docs.aws.amazon.com/greengrass/v1/apireference/updateconnectivityinfo-put.html)