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
Your root CA certificate type must match your endpoint\. Use an ATS root CA certificate with an ATS endpoint \(preferred\) or a VeriSign root CA certificate with a legacy endpoint\. Only some AWS Regions support legacy endpoints\. For more information, see [Use service endpoints that match the root CA certificate type](configure-greengrass-core-v2.md#certificate-endpoints)\.

## AWS IoT policies for data plane operations<a name="iot-policies"></a>

Use AWS IoT policies to authorize access to the AWS IoT Core and AWS IoT Greengrass data planes\. The AWS IoT Core data plane provides operations for devices, users, and applications\. These operations include the ability to connect to AWS IoT Core and subscribe to topics\. The AWS IoT Greengrass data plane provides operations for Greengrass devices\. These operations include the ability to resolve component dependencies and download public component artifacts\.

An AWS IoT policy is a JSON document that's similar to an IAM policy\. It contains one or more policy statements that specify the following properties:
+ `Effect`\. The access mode, which can be `Allow` or `Deny`\.
+ `Action`\. The list of actions that are allowed or denied by the policy\.
+ `Resource`\. The list of resources on which the action is allowed or denied\.

For more information, see [AWS IoT policies](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policies.html) and [AWS IoT policy actions](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policy-actions.html) in the *AWS IoT Core Developer Guide*\.

### AWS IoT Greengrass V2 policy actions<a name="greengrass-policy-actions"></a>

AWS IoT Greengrass V2 defines the following policy actions that Greengrass core devices can use in AWS IoT policies:

`greengrass:GetComponentVersionArtifact`  
Grants permission to get a presigned URL to download a public component artifact\.  
This permission is checked when a core device receives a deployment that specifies a public component that has artifacts\. If the core device already has the artifact, it doesn't download the artifact again\.

`greengrass:ResolveComponentCandidates`  
Grants permission to identify a list of components that meet the component, version, and platform requirements for a deployment\. If the requirements conflict, or no components exist that meet the requirements, then this operation returns an error and the deployment fails on the device\.  
This permission is checked when a core device receives a deployment that specifies components\.

## Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices<a name="greengrass-core-minimal-iot-policy"></a>

The following example policy includes the minimum set of actions required to support basic Greengrass functionality for your core device\.
+ The policy lists the MQTT topics and topic filters that the core device can publish messages to, subscribe to, and receive messages on, including topics used for shadow state\. To support message exchange between AWS IoT Core and Greengrass components, specify the topics and topic filters that you want to allow\. For more information, see [Publish/Subscribe policy examples](https://docs.aws.amazon.com/iot/latest/developerguide/pub-sub-policy.html) in the *AWS IoT Core Developer Guide*\.
+ The policy grants permission to publish to a topic for telemetry data\. You can remove this permission for core devices where you disable telemetry\. For more information, see [Gather system health telemetry data from AWS IoT Greengrass core devices](telemetry.md)\.
+ The policy grants permission to assume an IAM role through an AWS IoT role alias\. The core device uses this role, called the token exchange role, to acquire AWS credentials that it can use to authenticate AWS requests\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

  When you install the AWS IoT Greengrass Core software, you create and attach a second AWS IoT policy that includes only this permission\. If you include this permission in your core device's primary AWS IoT policy, you can detach and delete the other AWS IoT policy\.
+ <a name="thing-policy-variable-not-supported"></a>The use of [thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) in the AWS IoT policy for a core device is not supported\. The core uses the same device certificate to make multiple connections to AWS IoT Core but the client ID in a connection might not be an exact match of the core thing name\.

  <a name="thing-policy-variable-not-supported"></a>[Thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) aren't supported in AWS IoT policies for core devices\. The core device uses the same device certificate to make multiple connections to AWS IoT Core but the client ID in a connection might not be an exact match of the core device thing name\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "iot:Connect"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iot:Receive",
                "iot:Publish"
            ],
            "Resource": [
                "arn:aws:iot:region:account-id:topic/$aws/things/core-device-thing-name*/greengrassv2/health/json",
                "arn:aws:iot:region:account-id:topic/$aws/things/core-device-thing-name*/jobs/*",
                "arn:aws:iot:region:account-id:topic/$aws/things/core-device-thing-name*/shadow/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "iot:Subscribe"
            ],
            "Resource": [
                "arn:aws:iot:region:account-id:topicfilter/$aws/things/core-device-thing-name*/jobs/*",
                "arn:aws:iot:region:account-id:topicfilter/$aws/things/core-device-thing-name*/shadow/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "iot:AssumeRoleWithCertificate",
            "Resource": "arn:aws:iot:region:account-id:rolealias/token-exchange-role-alias-name"
        },
        {
            "Effect": "Allow",
            "Action": [
                "greengrass:GetComponentVersionArtifact",
                "greengrass:ResolveComponentCandidates"
            ],
            "Resource": "*"
        }
    ]
}
```