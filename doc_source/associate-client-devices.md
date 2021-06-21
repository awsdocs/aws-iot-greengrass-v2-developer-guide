# Associate client devices<a name="associate-client-devices"></a>

To use cloud discovery, associate client devices with a core device so that they can discover the core device\. Then, they can use the [Greengrass discovery API](greengrass-discover-api.md) to retrieve connectivity information and certificates for their associated core devices\.

Likewise, disassociate client devices from a core device to stop them from discovering the core device\.

**Topics**
+ [Manage client device associations \(console\)](#manage-client-device-associations-console)
+ [Manage client device associations \(AWS CLI\)](#manage-client-device-associations-cli)
+ [Manage client device associations \(API\)](#manage-client-device-associations-api)

## Manage client device associations \(console\)<a name="manage-client-device-associations-console"></a>

You can use the AWS IoT Greengrass console to view, add, and delete client device associations\.

**To view client device associations for a core device \(console\)**

1. <a name="navigate-greengrass-console"></a>Navigate to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. Choose **Core devices**\.

1. Choose the core device to manage\.

1. On the core device's details page, choose the **Client devices** tab\.

1. In the **Associated client devices** section, you can see which client devices \(AWS IoT things\) are associated with the core device\.

**To associate client devices with a core device \(console\)**

1. <a name="navigate-greengrass-console"></a>Navigate to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. Choose **Core devices**\.

1. Choose the core device to manage\.

1. On the core device's details page, choose the **Client devices** tab\.

1. In the **Associated client devices** section, choose **Associate client devices**\.

1. In the **Associate client devices with core device** modal, do the following for each client device to associate:

   1. Enter the name of the AWS IoT thing to associate as a client device\.

   1. Choose **Add**\.

1. Choose **Associate**\.

   The client devices that you associated can now use the Greengrass discovery API to discover this core device\.

**To disassociate client devices from a core device \(console\)**

1. <a name="navigate-greengrass-console"></a>Navigate to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

1. Choose **Core devices**\.

1. Choose the core device to manage\.

1. On the core device's details page, choose the **Client devices** tab\.

1. In the **Associated client devices** section, select each client device to disassociate\.

1. Choose **Disassociate**\.

1. In the confirmation modal, choose **Disassociate**\.

   The client devices that you disassociated can no longer use the Greengrass discovery API to discover this core device\.

## Manage client device associations \(AWS CLI\)<a name="manage-client-device-associations-cli"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to manage client device associations for a core device\.

**To view client device associations for a core device \(AWS CLI\)**
+ Use the following command: [list\-client\-devices\-associated\-with\-core\-device](https://docs.aws.amazon.com/cli/latest/reference/greengrassv2/list-client-devices-associated-with-core-device.html)\.

**To associate client devices with a core device \(AWS CLI\)**
+ Use the following command: [batch\-associate\-client\-device\-with\-core\-device](https://docs.aws.amazon.com/cli/latest/reference/greengrassv2/batch-associate-client-device-with-core-device.html)\.

**To disassociate client devices from a core device \(AWS CLI\)**
+ Use the following command: [batch\-disassociate\-client\-device\-from\-core\-device](https://docs.aws.amazon.com/cli/latest/reference/greengrassv2/batch-disassociate-client-device-from-core-device.html)\.

## Manage client device associations \(API\)<a name="manage-client-device-associations-api"></a>

You can use the AWS API to manage client device associations for a core device\.

**To view client device associations for a core device \(AWS API\)**
+ Use the following operation: [ListClientDevicesAssociatedWithCoreDevice](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ListClientDevicesAssociatedWithCoreDevice.html)\.

**To associate client devices with a core device \(AWS API\)**
+ Use the following operation: [BatchAssociateClientDeviceWithCoreDevice](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_BatchAssociateClientDeviceWithCoreDevice.html)\.

**To disassociate client devices from a core device \(AWS API\)**
+ Use the following operation: [BatchDisassociateClientDeviceFromCoreDevice](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_BatchDisassociateClientDeviceFromCoreDevice.html)\.