# Authenticating clients while offline<a name="offline-authentication"></a>

With *offline authentication* you can configure your AWS IoT Greengrass Core device so that client devices can connect to a core device, even when the core device isn't connected to the cloud\. When you use offline authentication, your Greengrass devices can continue to work in a partially offline environment\.

To use offline authentication for a client device with a connection to the cloud, you need the following:
+ An AWS IoT Greengrass Core device with the [Client device auth](client-device-auth-component.md) component deployed\. You must use version 2\.3\.0 or greater for offline authentication\.
+ A cloud connection for the core device during the initial connection of client devices\.

## Storing client credentials<a name="offline-auth-store-credentials"></a>

When a client device connects to a core device for the first time, the core device calls the AWS IoT Greengrass service\. When called, Greengrass validates the client device's registration as an AWS IoT thing\. It also validates that the device has a valid certificate\. The core device then stores this information locally\.

The next time that the device connects, the Greengrass core device attempts to validate the client device with the AWS IoT Greengrass service\. If it can't connect to AWS IoT Greengrass, the core device uses its locally stored device information to validate the client device\.

You can configure the length of time that the Greengrass core device stores credentials\. You can set the timeout from one minute to 2,147,483,647 minutes\. The default is one minute, which effectively turns off offline authentication\. When you set this timeout, we recommend that you consider your security needs\. You should also consider how long you expect core devices to run while disconnected from the cloud\.

The core device updates its credential storage at three times:

1. When a device connects to the core device for the first time\.

1. If the core device is connected to the cloud, when a client device reconnects to the core device\.

1. If the core device is connected to the cloud, once a day to refresh the entire credential store\.

When the Greengrass core device refreshes its credential store, it uses the [ ListClientDevicesAssociatedWithCoreDevice](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ListClientDevicesAssociatedWithCoreDevice.html) operation\. Greengrass only refreshes the devices returned by this operation\. To associate a client device with a core device, see [Associate client devices](associate-client-devices.md)\.

To use the `ListClientDevicesAssociatedWithCoreDevice` operation, you must add permission for the operation to the AWS Identity and Access Management \(IAM\) role associated with the AWS account that runs AWS IoT Greengrass\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.