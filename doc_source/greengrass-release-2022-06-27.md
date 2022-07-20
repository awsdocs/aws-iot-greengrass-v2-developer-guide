# Release: AWS IoT Greengrass Core v2\.6\.0 software update on June 27, 2022<a name="greengrass-release-2022-06-27"></a>

This release provides version 2\.6\.0 of the Greengrass nucleus component, new AWS\-provided components, and updates to AWS\-provided components\.

**Release date:** June 27, 2022

**Release highlights**
+ **Wildcards in local publish/subscribe topics** – You can now use MQTT wildcards when you subscribe to local publish/subscribe topics\. For more information, see [Publish/subscribe local messages](ipc-publish-subscribe.md) and [SubscribeToTopic](ipc-publish-subscribe.md#ipc-operation-subscribetotopic)\.
+ **Client device shadow support** – You can now interact with client device shadows in custom components and sync client device shadows with AWS IoT Core\. For more information, see [Interact with and sync client device shadows](work-with-client-device-shadows.md)\.
+ **Local MQTT 5 support for client devices** – You can now deploy the EMQX MQTT 5 broker to use MQTT 5 features in communication between client devices and a core device\. For more information, see [MQTT 5 broker \(EMQX\)](mqtt-broker-emqx-component.md) and [Connect client devices to core devices](connect-client-devices.md)\.
+ **Recipe variables in component configurations** – You can now use specific recipe variables in component configurations\. You can use these recipe variables when you define a component's default configuration in a recipe or when you configure a component in a deployment\. For more information, see [Recipe variables](component-recipe-reference.md#recipe-variables) and [Use recipe variables in merge updates](update-component-configurations.md#merge-configuration-update-recipe-variables)\.
+ **Wildcards in IPC authorization policies** – You can now use the `*` wildcard to match any combination of characters in interprocess communication \(IPC\) authorization policies\. This wildcard enables you to allow access to multiple resources in a single authorization policy\. For more information, see [Wildcards in authorization policies](interprocess-communication.md#ipc-authorization-policy-wildcards)\.
+ **IPC operations that manage local deployments and components** – You can now develop custom components that manage local deployments and view component details\. For more information, see [IPC: Manage local deployments and components](ipc-local-deployments-components.md)\.
+ **IPC operations that authenticate and authorize client devices** – You can now use these operations to create a custom local broker component\. For more information, see [IPC: Authenticate and authorize client devices](ipc-client-device-auth.md)\.

**Topics**
+ [Public component updates](#greengrass-2022-06-27-components)

## Public component updates<a name="greengrass-2022-06-27-components"></a>

The following table lists AWS\-provided components that include new and updated features\.

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus |  Version 2\.6\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.6.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-06-27.html)  | 
| MQTT 5 broker \(EMQX\) |  Version 1\.0\.0 of the new [EMQX MQTT 5 broker component](mqtt-broker-emqx-component.md) is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-06-27.html)  | 
| Shadow manager |  Version 2\.2\.0 of the [shadow manager component](shadow-manager-component.md) is available\. <a name="changelog-shadow-manager-2.2.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-06-27.html)  | 
| Client device auth |  Version 2\.2\.0 of the [client device auth component](client-device-auth-component.md) is available\. <a name="changelog-client-device-auth-2.2.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-06-27.html)  | 
| MQTT bridge |  Version 2\.2\.0 of the [MQTT bridge component](mqtt-bridge-component.md) is available\. <a name="changelog-mqtt-bridge-2.2.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-06-27.html)  | 
| Greengrass CLI |  Version 2\.6\.0 of the [Greengrass CLI](greengrass-cli-component.md) is available\. <a name="changelog-greengrass-cli-2.6.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-06-27.html)  | 