# Device authentication and authorization for AWS IoT Greengrass<a name="device-auth"></a>

Devices in AWS IoT Greengrass environments use X\.509 certificates for authentication and AWS IoT policies for authorization\. Certificates and policies allow devices to securely connect with each other, AWS IoT Core, and AWS IoT Greengrass\.

X\.509 certificates are digital certificates that use the X\.509 public key infrastructure standard to associate a public key with the identity contained in a certificate\. X\.509 certificates are issued by a trusted entity called a certificate authority \(CA\)\. The CA maintains one or more special certificates called CA certificates that it uses to issue X\.509 certificates\. Only the certificate authority has access to CA certificates\.

AWS IoT policies define the set of operations allowed for AWS IoT devices\. Specifically, they allow and deny access to AWS IoT Core and AWS IoT Greengrass data plane operations, such as publishing MQTT messages and retrieving device shadows\.

All devices require an entry in the AWS IoT Core registry and an activated X\.509 certificate with an attached AWS IoT policy\. 

## X\.509 certificates<a name="x509-certificates"></a>

Communication between devices and AWS IoT Core or AWS IoT Greengrass must be authenticated\. This mutual authentication is based on registered X\.509 device certificates and cryptographic keys\.

In an AWS IoT Greengrass environment, devices use certificates with public and private keys for the following Transport Layer Security \(TLS\) connections:
+ The AWS IoT client component on the Greengrass core connecting to AWS IoT Core and AWS IoT Greengrass over the internet\.

AWS IoT Greengrass core devices store certificates in the root folder\.

### Certificate authority \(CA\) certificates<a name="ca-certificates"></a>

Core devices download a root CA certificate used for authentication with AWS IoT Core and AWS IoT Greengrass services\. We recommend that you use an Amazon Trust Services \(ATS\) root CA certificate, such as [Amazon Root CA 1](https://www.amazontrust.com/repository/AmazonRootCA1.pem)\. For more information, see [CA certificates for server authentication](https://docs.aws.amazon.com/iot/latest/developerguide/server-authentication.html#server-authentication-certs) in the *AWS IoT Core Developer Guide*\.

**Note**  
Your root CA certificate type must match your endpoint\. Use an ATS root CA certificate with an ATS endpoint \(preferred\) or a VeriSign root CA certificate with a legacy endpoint\. Only some AWS Regions support legacy endpoints\. For more information, see [Service endpoints must match the root CA certificate type](configure-greengrass-core-v2.md#certificate-endpoints)\.