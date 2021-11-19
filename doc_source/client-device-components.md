# AWS\-provided client device components<a name="client-device-components"></a>

AWS IoT Greengrass provides the following public components that you can deploy to core devices\. These components enable client devices to connect and communicate with a core device\.

**Note**  <a name="component-nucleus-dependency-update-note"></a>
Several AWS\-provided components depend on specific minor versions of the Greengrass nucleus\. Because of this dependency, you need to update these components when you update the Greengrass nucleus to a new minor version\. For information about the specific versions of the nucleus that each component depends on, see the corresponding component topic\. For more information about updating the nucleus, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| Component | Description | Depends on nucleus | [Component type](develop-greengrass-components.md#component-types) | Supported OS | [Open source](open-source.md) | 
| --- | --- | --- | --- | --- | --- | 
| <a name="client-device-auth-component-table-row"></a>[Client device auth](client-device-auth-component.md) | Enables local IoT devices, called client devices, to connect to the core device\. | Yes | Plugin | Linux, Windows | [Yes](https://github.com/aws-greengrass/aws-greengrass-client-device-auth) | 
| <a name="ip-detector-component-table-row"></a>[IP detector](ip-detector-component.md) | Reports MQTT broker connectivity information to AWS IoT Greengrass, so client devices can discover how to connect\. | Yes | Plugin | Linux, Windows | [Yes](https://github.com/aws-greengrass/aws-greengrass-ip-detector) | 
| <a name="mqtt-bridge-component-table-row"></a>[MQTT bridge](mqtt-bridge-component.md) | Relays MQTT messages between client devices, local AWS IoT Greengrass publish/subscribe, and AWS IoT Core\. | No | Plugin | Linux, Windows | [Yes](https://github.com/aws-greengrass/aws-greengrass-mqtt-bridge) | 
| <a name="mqtt-broker-moquette-component-table-row"></a>[MQTT broker \(Moquette\)](mqtt-broker-moquette-component.md) | Handles MQTT messages between client devices and the core device\. | No | Plugin | Linux, Windows | [Yes](https://github.com/aws-greengrass/aws-greengrass-moquette-mqtt) | 