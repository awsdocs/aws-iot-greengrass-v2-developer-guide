# Encryption in transit<a name="encryption-in-transit"></a>

AWS IoT Greengrass has two modes of communication where data is in transit:
+ [Data in transit over the internet](#data-in-transit-internet)\. Communication between a Greengrass core and AWS IoT Greengrass over the internet is encrypted\.
+ [Data on the core device](#data-in-transit-locally)\. Communication between components on the Greengrass core device is not encrypted\.

## Data in transit over the internet<a name="data-in-transit-internet"></a>

AWS IoT Greengrass uses Transport Layer Security \(TLS\) to encrypt all communication over the internet\. All data sent to the AWS Cloud is sent over a TLS connection using MQTT or HTTPS protocols, so it is secure by default\. AWS IoT Greengrass uses the AWS IoT transport security model\. For more information, see [Transport security](https://docs.aws.amazon.com/iot/latest/developerguide/transport-security.html) in the *AWS IoT Core Developer Guide*\.

## Data on the core device<a name="data-in-transit-locally"></a>

AWS IoT Greengrass doesn't encrypt data exchanged locally on the Greengrass core device because the data doesn't leave the device\. This includes communication between user\-defined components, the AWS IoT device SDK, and public components, such as stream manager\.