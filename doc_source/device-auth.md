# Device authentication and authorization for AWS IoT Greengrass<a name="device-auth"></a>

Devices in AWS IoT Greengrass environments use X\.509 certificates for authentication and AWS IoT policies for authorization\. Certificates and policies allow devices to securely connect with each other, AWS IoT Core, and AWS IoT Greengrass\.

X\.509 certificates are digital certificates that use the X\.509 public key infrastructure standard to associate a public key with the identity contained in a certificate\. X\.509 certificates are issued by a trusted entity called a certificate authority \(CA\)\. The CA maintains one or more special certificates called CA certificates that it uses to issue X\.509 certificates\. Only the certificate authority has access to CA certificates\.

AWS IoT policies define the set of operations allowed for AWS IoT devices\. Specifically, they allow and deny access to AWS IoT Core and AWS IoT Greengrass data plane operations, such as publishing MQTT messages and retrieving device shadows\.

All devices require an entry in the AWS IoT Core registry and an activated X\.509 certificate with an attached AWS IoT policy\. Devices fall into two categories:
+ **Greengrass core devices**

  Greengrass core devices use certificates and AWS IoT policies to connect to AWS IoT Core and AWS IoT Greengrass\. The certificates and policies also allow AWS IoT Greengrass to deploy components and configurations to core devices\.
+ **Client devices**

  MQTT client devices use certificates and policies to connect to AWS IoT Core and the AWS IoT Greengrass service\. This enables client devices to use the AWS IoT Greengrass cloud discovery to find and connect to a Greengrass core device\. A client device uses the same certificate to connect to the AWS IoT Core cloud service and core devices\. Client devices also use discovery information for mutual authentication with the core device\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

## X\.509 certificates<a name="x509-certificates"></a>

Communication between core devices and client devices and between devices and AWS IoT Core or AWS IoT Greengrass must be authenticated\. This mutual authentication is based on registered X\.509 device certificates and cryptographic keys\.

In an AWS IoT Greengrass environment, devices use certificates with public and private keys for the following Transport Layer Security \(TLS\) connections:
+ The AWS IoT client component on the Greengrass core device that connects to AWS IoT Core and AWS IoT Greengrass over the internet\.
+ Client devices that connect to AWS IoT Greengrass over the internet to discover core devices\.
+ The MQTT broker component on the Greengrass core connecting to Greengrass devices in the group over the local network\.

AWS IoT Greengrass core devices store certificates in the Greengrass root folder\.

### Certificate authority \(CA\) certificates<a name="ca-certificates"></a>

Greengrass core devices and client devices download a root CA certificate used for authentication with the AWS IoT Core and AWS IoT Greengrass services\. We recommend that you use an Amazon Trust Services \(ATS\) root CA certificate, such as [Amazon Root CA 1](https://www.amazontrust.com/repository/AmazonRootCA1.pem)\. For more information, see [CA certificates for server authentication](https://docs.aws.amazon.com/iot/latest/developerguide/server-authentication.html#server-authentication-certs) in the *AWS IoT Core Developer Guide*\.

Client devices also download a Greengrass core device CA certificate\. They use this certificate to validate the MQTT server certificate on the core device during mutual authentication\.

### Certificate rotation on the local MQTT broker<a name="mqtt-expiration"></a>

Greengrass core devices generate a local MQTT server certificate that client devices use for mutual authentication\. This certificate is signed by the core device CA certificate, which the core device stores in the AWS IoT Greengrass cloud\. Client devices retrieve the core device CA certificate when they discover the core device\. They use the core device CA certificate to verify the core device's MQTT server certificate when they connect to the core device\. The core device CA certificate expires after 5 years\.

The MQTT server certificate expires every 7 days\. This limited period is based on security best practices\. This rotation helps mitigate the threat of an attacker stealing the MQTT server certificate and private key to impersonate the Greengrass core device\.

When the MQTT server certificate expires, the Greengrass core device generates a new certificate and restarts the local MQTT broker\. When this happens, all client devices connected to the Greengrass core device are disconnected\. Client devices can reconnect to the Greengrass core device after a short period of time\.

## AWS IoT policies for data plane operations<a name="iot-policies"></a>

Use AWS IoT policies to authorize access to the AWS IoT Core and AWS IoT Greengrass data planes\. The AWS IoT Core data plane provides operations for devices, users, and applications\. These operations include the ability to connect to AWS IoT Core and subscribe to topics\. The AWS IoT Greengrass data plane provides operations for Greengrass devices\. These operations include the ability to resolve component dependencies and download public component artifacts\.

An AWS IoT policy is a JSON document that's similar to an [IAM policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_grammar.html#policies-grammar-json)\. It contains one or more policy statements that specify the following properties:
+ `Effect`\. The access mode, which can be `Allow` or `Deny`\.
+ `Action`\. The list of actions that are allowed or denied by the policy\.
+ `Resource`\. The list of resources on which the action is allowed or denied\.

AWS IoT policies support `*` as a wildcard character, and treat MQTT wildcard characters \(`+` and `#`\) as literal strings\. For more information about the `*` wildcard, see [Using wildcard in resource ARNs](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_resource.html#reference_policies_elements_resource_wildcards) in the *AWS Identity and Access Management User Guide*\.

For more information, see [AWS IoT policies](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policies.html) and [AWS IoT policy actions](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policy-actions.html) in the *AWS IoT Core Developer Guide*\.

**Important**  
<a name="thing-policy-variable-not-supported"></a>[Thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) aren't supported for in AWS IoT policies for core devices or Greengrass data plane operations\. Instead, you can use a wildcard that matches multiple devices that have similar names\. For example, you can specify `MyGreengrassDevice*` to match `MyGreengrassDevice1`, `MyGreengrassDevice2`, and so on\. 

**Note**  
AWS IoT Core enables you to attach AWS IoT policies to thing groups to define permissions for groups of devices\. Thing group policies don't allow access to AWS IoT Greengrass data plane operations\. To allow a thing access to an AWS IoT Greengrass data plane operation, add the permission to an AWS IoT policy that you attach to the thing's certificate\.

### AWS IoT Greengrass V2 policy actions<a name="greengrass-policy-actions"></a>

AWS IoT Greengrass V2 defines the following policy actions that Greengrass core devices and client devices can use in AWS IoT policies:Core device actions

`greengrass:GetComponentVersionArtifact`  
Grants permission to get a presigned URL to download a public component artifact\.  
This permission is evaluated when a core device receives a deployment that specifies a public component that has artifacts\. If the core device already has the artifact, it doesn't download the artifact again\.

`greengrass:ResolveComponentCandidates`  
Grants permission to identify a list of components that meet the component, version, and platform requirements for a deployment\. If the requirements conflict, or no components exist that meet the requirements, this operation returns an error and the deployment fails on the device\.  
This permission is evaluated when a core device receives a deployment that specifies components\.

`greengrass:GetDeploymentConfiguration`  
Grants permission to get a presigned URL to download a large deployment document\.  
This permission is evaluated when a core device receives a deployment that specifies a deployment document larger than 7 KB \(if the deployment targets a thing\) or 31 KB \(if the deployment targets a thing group\)\. The deployment document includes component configurations, deployment policies, and deployment metadata\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.  
This feature is available for v2\.3\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

`greengrass:ListThingGroupsForCoreDevice`  
Grants permission to get a core device's thing group hierarchy\.  
This permission is checked when a core device receives a deployment from AWS IoT Greengrass\. The core device uses this action to identify whether it was removed from a thing group since the last deployment\. If the core device was removed from a thing group, and that thing group is the target of a deployment to the core device, then the core device removes the components installed by that deployment\.  
This feature is used by v2\.5\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

`greengrass:VerifyClientDeviceIdentity`  
Grants permission to verify the identity of a client device that connects to a core device\.  
This permission is evaluated when a core device receives an MQTT connection from a client device\. The client device presents its AWS IoT device certificate\. Then, the core device sends the device certificate to the AWS IoT Greengrass cloud service to verify the client device's identity\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

`greengrass:VerifyClientDeviceIoTCertificateAssociation`  
Grants permission to verify whether a client device is associated with an AWS IoT certificate\.  
This permission is evaluated when a core device authorizes a client device to connect over MQTT\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.  
For a core device to use this operation, the [Greengrass service role](greengrass-service-role.md) must be associated to your AWS account and allow the `iot:DescribeCertificate` permission\.

`greengrass:PutCertificateAuthorities`  <a name="greengrass-put-certificate-authorities-action"></a>
Grants permission to upload certificate authority \(CA\) certificates that client devices can download to verify the core device\.  
This permission is evaluated when a core device installs and runs the [client device auth component](client-device-auth-component.md)\. This component creates a local certificate authority and uses this operation to upload its CA certificates\. Client devices download these CA certificates when they use the [Discover](#greengrass-discover-action) operation to find core devices where they can connect\. When client devices connect to an MQTT broker on a core device, they use these CA certificates to verify the identity of the core device\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

`greengrass:GetConnectivityInfo`  
Grants permission to get connectivity information for a core device\. This information describes how client devices can connect to the core device\.  
This permission is evaluated when a core device installs and runs the [client device auth component](client-device-auth-component.md)\. This component uses the connectivity information to generate valid CA certificates to upload to the AWS IoT Greengrass cloud service with the [PutCertificateAuthories](#greengrass-put-certificate-authorities-action) operation\. Client devices use these CA certificates to verify the identity of the core device\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.  
You can also use this operation on the AWS IoT Greengrass control plane to view connectivity information for a core device\. For more information, see [GetConnectivityInfo](https://docs.aws.amazon.com/greengrass/v1/apireference/getconnectivityinfo-get.html) in the *AWS IoT Greengrass V1 API Reference*\.

`greengrass:UpdateConnectivityInfo`  
Grants permission to update connectivity information for a core device\. This information describes how client devices can connect to the core device\.  
This permission is evaluated when a core device runs the [IP detector component](ip-detector-component.md)\. This component identifies the information that client devices require to connect to the core device on the local network\. Then, this component uses this operation to upload the connectivity information to the AWS IoT Greengrass cloud service, so client devices can retrieve this information with the [Discover](#greengrass-discover-action) operation\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.  
You can also use this operation on the AWS IoT Greengrass control plane to manually update connectivity information for a core device\. For more information, see [UpdateConnectivityInfo](https://docs.aws.amazon.com/greengrass/v1/apireference/updateconnectivityinfo-put.html) in the *AWS IoT Greengrass V1 API Reference*\.Client device actions

`greengrass:Discover`  <a name="greengrass-discover-action"></a>
Grants permission to discover connectivity information for core devices where a client device can connect\. This information describes how the client device can connect to the core devices\. A client device can discover only the core devices that you have associated it with by using the [BatchAssociateClientDeviceWithCoreDevice](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_BatchAssociateClientDeviceWithCoreDevice.html) operation\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.

## Update a core device's AWS IoT policy<a name="update-core-device-iot-policy"></a>

You can use the AWS IoT Greengrass and AWS IoT consoles to view and update a core device's AWS IoT policy\.

**Note**  
If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), your core device has an AWS IoT policy that allows access to all AWS IoT Greengrass actions \(`greengrass:*`\)\. You can follow these steps to restrict access to only the actions that a core device uses\.

**To review and update a core device's AWS IoT policy**

1. <a name="update-iot-policy-open-greengrass-console"></a>In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Core devices**\.

1. <a name="update-iot-policy-choose-core-device"></a>On the **Core devices** page, choose the core device to update\.

1. <a name="update-iot-policy-choose-core-device-thing"></a>On the core device details page, choose the link to the core device's **Thing**\. This link opens the thing details page in the AWS IoT console\.

1. <a name="update-iot-policy-choose-thing-security"></a>On the thing details page, choose **Certificates**\.

1. <a name="update-iot-policy-choose-thing-certificate"></a>In the **Certificates** tab, choose the thing's active certificate\.

1. <a name="update-iot-policy-choose-certificate-policies"></a>On the certificate details page, choose **Policies**\.

1. <a name="update-iot-policy-choose-policy"></a>In the **Policies** tab, choose the AWS IoT policy to review and update\. You can add the required permissions to any policy that is attached to the core device's active certificate\.
**Note**  
If you used the [AWS IoT Greengrass Core software installer to provision resources](quick-installation.md), you have two AWS IoT policies\. We recommend that you choose the policy named **GreengrassV2IoTThingPolicy**, if it exists\. Core devices that you create with the quick installer use this policy name by default\. If you add permissions to this policy, you are also granting these permissions to other core devices that use this policy\.

1. <a name="update-iot-policy-edit-policy"></a>In the policy overview, choose **Edit policy document**\.

1. Review the policy and add, remove, or edit permissions as needed\.

1. <a name="update-iot-policy-save-policy"></a>Choose **Save as new version**\.

## Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices<a name="greengrass-core-minimal-iot-policy"></a>

**Important**  
Later versions of the [Greengrass nucleus component](greengrass-nucleus-component.md) require additional permissions on the minimal AWS IoT policy\. You might need to [update your core devices' AWS IoT policies](#update-core-device-iot-policy) to grant additional permissions\.  
Core devices that run Greengrass nucleus v2\.5\.0 and later require the `greengrass:ListThingGroupsForCoreDevice` permission\.
Core devices that run Greengrass nucleus v2\.3\.0 and later require the `greengrass:GetDeploymentConfiguration` permission to support large deployment configuration documents\.

The following example policy includes the minimum set of actions required to support basic Greengrass functionality for your core device\.
+ The policy includes the `*` wildcard after the core device thing name \(For example, `core-device-thing-name*`\)\. The core device uses the same device certificate to make multiple connections to AWS IoT Core, but the client ID in a connection might not be an exact match of the core device thing name\. This wildcard allows the core device to connect when it uses a client ID with a suffix\.
+ The policy lists the MQTT topics and topic filters that the core device can publish messages to, subscribe to, and receive messages on, including topics used for shadow state\. To support message exchange between AWS IoT Core, Greengrass components, and client devices, specify the topics and topic filters that you want to allow\. For more information, see [Publish/Subscribe policy examples](https://docs.aws.amazon.com/iot/latest/developerguide/pub-sub-policy.html) in the *AWS IoT Core Developer Guide*\.
+ The policy grants permission to publish to the following topic for telemetry data\.

  ```
  $aws/things/core-device-thing-name*/greengrass/health/json
  ```

  You can remove this permission for core devices where you disable telemetry\. For more information, see [Gather system health telemetry data from AWS IoT Greengrass core devices](telemetry.md)\.
+ The policy grants permission to assume an IAM role through an AWS IoT role alias\. The core device uses this role, called the token exchange role, to acquire AWS credentials that it can use to authenticate AWS requests\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

  When you install the AWS IoT Greengrass Core software, you create and attach a second AWS IoT policy that includes only this permission\. If you include this permission in your core device's primary AWS IoT policy, you can detach and delete the other AWS IoT policy\.
+ The policy includes a section that allows AWS IoT Core to get, update, and delete the core device's shadow\. To allow shadow sync for connected devices in the Greengrass group, specify the target Amazon Resource Names \(ARNs\) in the `Resource` list \(for example, `arn:aws:iot:region:account-id:thing/device-name`\)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "iot:Connect"
            ],
            "Resource": "arn:aws:iot:region:account-id:client/core-device-thing-name*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iot:Receive",
                "iot:Publish"
            ],
            "Resource": [
                "arn:aws:iot:region:account-id:topic/$aws/things/core-device-thing-name*/greengrass/health/json",
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
            "Action": [
                "iot:GetThingShadow",
                "iot:UpdateThingShadow",
                "iot:DeleteThingShadow"
            ],
            "Resource": [
                "arn:aws:iot:region:account-id:thing/core-device-thing-name*"
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
                "greengrass:ResolveComponentCandidates",
                "greengrass:GetDeploymentConfiguration",
                "greengrass:ListThingGroupsForCoreDevice"
            ],
            "Resource": "*"
        }
    ]
}
```

## Minimal AWS IoT policy to support client devices<a name="client-device-support-minimal-iot-policy"></a>

The following example policy includes the minimum set of actions required to support interaction with client devices on a core device\. To support client devices, a core device must have the permissions in this AWS IoT policy in addition to the [Minimal AWS IoT policy for basic operation](#greengrass-core-minimal-iot-policy)\.
+ The policy allows the core device to get, update, and delete the shadow for any AWS IoT thing\. These permissions allow the core device to manage and sync shadows for client devices\. To follow best security practices, restrict the `iot:GetThingShadow`, `iot:UpdateThingShadow`, and `iot:DeleteThingShadow` operations to the minimal set of client devices that connect to the core device for your use case\.
+ The policy allows the core device to update its own connectivity information\. This permission is required only if you deploy the [IP detector component](ip-detector-component.md) to the core device\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "greengrass:PutCertificateAuthorities",
                "greengrass:VerifyClientDeviceIdentity"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "greengrass:VerifyClientDeviceIoTCertificateAssociation"
            ],
            "Resource": "arn:aws:iot:region:account-id:thing/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "greengrass:GetConnectivityInfo",
                "greengrass:UpdateConnectivityInfo"
            ],
            "Resource": [
                "arn:aws:greengrass:region:account-id:/greengrass/things/core-device-thing-name*/connectivityInfo"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "iot:GetThingShadow",
                "iot:UpdateThingShadow",
                "iot:DeleteThingShadow"
            ],
            "Resource": [
                "arn:aws:iot:region:account-id:thing/*"
            ]
        }
    ]
}
```

## Minimal AWS IoT policy for client devices<a name="client-device-minimal-iot-policy"></a>

The following example policy includes the minimum set of actions required for a client device to discover core devices where they connect and communicate over MQTT\. The client device's AWS IoT policy must include the `greengrass:Discover` action to allow the device to discover connectivity information for its associated Greengrass core devices\. In the `Resource` section, specify the Amazon Resource Name \(ARN\) of the client device, not the ARN of the Greengrass core device\.
+ The policy allows communication on all MQTT topics\. To follow best security practices, restrict the `iot:Publish`, `iot:Subscribe`, and `iot:Receive` permissions to the minimal set of topics that a client device requires for your use case\.
+ The policy allows the thing to discover core devices for all AWS IoT things\. To follow best security practices, restrict the `greengrass:Discover` permission to the client device's AWS IoT thing or a wildcard that matches a set of AWS IoT things\.
**Important**  
<a name="thing-policy-variable-not-supported"></a>[Thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) aren't supported for in AWS IoT policies for core devices or Greengrass data plane operations\. Instead, you can use a wildcard that matches multiple devices that have similar names\. For example, you can specify `MyGreengrassDevice*` to match `MyGreengrassDevice1`, `MyGreengrassDevice2`, and so on\. 
+ A client device's AWS IoT policy doesn't typically require permissions for `iot:GetThingShadow`, `iot:UpdateThingShadow`, or `iot:DeleteThingShadow` actions, because the Greengrass core device handles shadow sync operations for client devices\. To enable the core device to handle client device shadows, check that the core device's AWS IoT policy allows these actions, and that the `Resource` section includes the ARNs of the client devices\.

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
                "iot:Publish"
            ],
            "Resource": [
                "arn:aws:iot:region:account-id:topic/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "iot:Subscribe"
            ],
            "Resource": [
                "arn:aws:iot:region:account-id:topicfilter/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "iot:Receive"
            ],
            "Resource": [
                "arn:aws:iot:region:account-id:topic/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "greengrass:Discover"
            ],
            "Resource": [
                "arn:aws:iot:region:account-id:thing/*"
            ]
        }
    ]
}
```