# Choose an MQTT broker<a name="choose-local-mqtt-broker"></a>

AWS IoT Greengrass provides options for you to choose which local MQTT broker to run on your core devices\. Client devices connect to the MQTT broker that runs on a core device, so choose an MQTT broker that is compatible with the client devices that you want to connect\.

**Note**  <a name="note-deploy-one-mqtt-broker"></a>
We recommend that you deploy only one MQTT broker component\. The [MQTT bridge](mqtt-bridge-component.md) and [IP detector](ip-detector-component.md) components work with only one MQTT broker component at a time\. If you deploy multiple MQTT broker components, you must configure them to use different ports\.

You can choose from the following MQTT brokers:
+ **[MQTT 3\.1\.1 broker \(Moquette\)](mqtt-broker-moquette-component.md)** – `aws.greengrass.clientdevices.mqtt.Moquette`

  Choose this option for a lightweight MQTT broker that is compliant with the MQTT 3\.1\.1 standard\. The AWS IoT Core MQTT broker and AWS IoT Device SDK are also compliant with the MQTT 3\.1\.1 standard, so you can use these features to create an application that uses MQTT 3\.1\.1 across your devices and the AWS Cloud\.
+ **[MQTT 5 broker \(EMQX\)](mqtt-broker-emqx-component.md)** – `aws.greengrass.clientdevices.mqtt.EMQX`

  Choose this option to use MQTT 5 features in communication between core devices and client devices\. This component uses more resources than the Moquette MQTT 3\.1\.1 broker, and on Linux core devices, it requires Docker\.

  MQTT 5 is backward\-compatible with MQTT 3\.1\.1, so you can connect client devices that use MQTT 3\.1\.1 to this broker\. If you run the Moquette MQTT 3\.1\.1 broker, you can replace it with the EMQX MQTT 5 broker, and client devices can continue to connect and operate as usual\.

  <a name="note-local-mqtt-broker-mqtt-5-features"></a>MQTT 5 features are limited to communication between core devices and client devices, because Greengrass uses MQTT 3\.1\.1 to connect to the AWS IoT Core MQTT broker\. As a result, when you use the [MQTT bridge component](mqtt-bridge-component.md) to relay MQTT messages between AWS IoT Core and this MQTT 5 broker, messages use MQTT 3\.1\.1 instead of MQTT 5\.
+ **Implement a custom broker**

  Choose this option to create a custom local broker component to communicate with client devices\. You can create a custom local broker that uses a protocol other than MQTT\. AWS IoT Greengrass provides a component SDK that you can use to authenticate and authorize client devices\. For more information, see [Use the AWS IoT Device SDK to communicate with the Greengrass nucleus, other components, and AWS IoT CoreCommunicate with the Greengrass nucleus, other components, and AWS IoT Core](interprocess-communication.md) and [Authenticate and authorize client devices](ipc-client-device-auth.md)\.