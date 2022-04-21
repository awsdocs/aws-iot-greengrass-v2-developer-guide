# Key management for the Greengrass core device<a name="key-management"></a>

It's the responsibility of the customer to guarantee secure storage of cryptographic \(public and private\) keys on the Greengrass core device\. AWS IoT Greengrass uses public and private keys for the following scenario:
+ The IoT client key is used with the IoT certificate to authenticate the Transport Layer Security \(TLS\) handshake when a Greengrass core connects to AWS IoT Core\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.
**Note**  
The key and certificate are also referred to as the core private key and the core device certificate\.

A Greengrass core device supports private key storage using file system permissions or a [hardware security module](hardware-security.md)\. If you use file system\-based private keys, you are responsible for their secure storage on the core device\.