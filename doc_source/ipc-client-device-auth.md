# Authenticate and authorize client devices<a name="ipc-client-device-auth"></a>

**Note**  
This feature is available for v2\.6\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

Use the client device auth IPC service to develop a custom local broker component where local IoT devices, such as client devices, can connect\.

To use these IPC operations, include version 2\.2\.0 or later of the [client device auth component](client-device-auth-component.md) as a dependency in your custom component\. You can then use IPC operations in your custom components to do the following:
+ Verify the identity of client devices that connect to the core device\.
+ Create a session for a client device to connect to the core device\.
+ Verify whether a client device has permission to perform an action\.
+ Receive a notification when the core device's server certificate rotates\.

**Topics**
+ [Minimum SDK versions](#ipc-client-device-auth-sdk-versions)
+ [Authorization](#ipc-client-device-auth-authorization)
+ [VerifyClientDeviceIdentity](#ipc-operation-verifyclientdeviceidentity)
+ [GetClientDeviceAuthToken](#ipc-operation-getclientdeviceauthtoken)
+ [AuthorizeClientDeviceAction](#ipc-operation-authorizeclientdeviceaction)
+ [SubscribeToCertificateUpdates](#ipc-operation-subscribetocertificateupdates)

## Minimum SDK versions<a name="ipc-client-device-auth-sdk-versions"></a>

The following table lists the minimum versions of the AWS IoT Device SDK that you must use to interact with the client device auth IPC service\.


| SDK | Minimum version | 
| --- | --- | 
|  [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2)  |  v1\.9\.3  | 
|  [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2)  |  v1\.11\.3  | 
|  [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2)  |  v1\.18\.3  | 

## Authorization<a name="ipc-client-device-auth-authorization"></a>

To use the client device auth IPC service in a custom component, you must define authorization policies that allow your component to perform these operations\. For information about defining authorization policies, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies)\.

Authorization policies for client device authentication and authorization have the following properties\.

**IPC service identifier:** `aws.greengrass.clientdevices.Auth`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#VerifyClientDeviceIdentity`  |  Allows a component to verify the identity of a client device\.  |  `*`  | 
|  `aws.greengrass#GetClientDeviceAuthToken`  |  Allows a component to validate a client device's credentials and create a session for that client device\.  |  `*`  | 
|  `aws.greengrass#AuthorizeClientDeviceAction`  |  Allows a component to verify whether a client device has permission to perform an action\.  |  `*`  | 
|  `aws.greengrass#SubscribeToCertificateUpdates`  |  Allows a component to receive notifications when the core device's server certificate rotates\.  |  `*`  | 
|  `*`  |  Allows a component to perform all client device auth IPC service operations\.  |  `*`  | 

### Authorization policy examples<a name="ipc-client-device-auth-authorization-policy-examples"></a>

You can reference the following authorization policy example to help you configure authorization policies for your components\.

**Example authorization policy**  
The following example authorization policy allows a component to perform all client device auth IPC operations\.  

```
{
  "accessControl": {
    "aws.greengrass.clientdevices.Auth": {
      "com.example.MyLocalBrokerComponent:clientdevices:1": {
        "policyDescription": "Allows access to authenticate and authorize client devices.",
        "operations": [
          "aws.greengrass#VerifyClientDeviceIdentity",
          "aws.greengrass#GetClientDeviceAuthToken",
          "aws.greengrass#AuthorizeClientDeviceAction",
          "aws.greengrass#SubscribeToCertificateUpdates"
        ],
        "resources": [
          "*"
        ]
      }
    }
  }
}
```

## VerifyClientDeviceIdentity<a name="ipc-operation-verifyclientdeviceidentity"></a>

Verify the identity of a client device\. This operation verifies whether the client device is a valid AWS IoT thing\.

### Request<a name="ipc-operation-verifyclientdeviceidentity-request"></a>

This operation's request has the following parameters:

`credential`  
The client device's credentials\. This object, `ClientDeviceCredential`, contains the following information:    
`clientDeviceCertificate` \(Python: `client_device_certificate`\)  
The client device's X\.509 device certificate\.

### Response<a name="ipc-operation-verifyclientdeviceidentity-response"></a>

This operation's response has the following information:

`isValidClientDevice` \(Python: `is_valid_client_device`\)  
Whether the client device's identity is valid\.

## GetClientDeviceAuthToken<a name="ipc-operation-getclientdeviceauthtoken"></a>

Validates a client device's credentials and creates a session for the client device\. This operation returns a session token that you can use in subsequent requests to [authorize client device actions](#ipc-operation-authorizeclientdeviceaction)\.

To successfully connect a client device, the [client device auth component](client-device-auth-component.md#client-device-auth-component-configuration) must grant the `mqtt:connect` permission for the client ID that the client device uses\.

### Request<a name="ipc-operation-getclientdeviceauthtoken-request"></a>

This operation's request has the following parameters:

`credential`  
The client device's credentials\. This object, `CredentialDocument`, contains the following information:    
`mqttCredential` \(Python: `mqtt_credential`\)  
The client device's MQTT credentials\. Specify the client ID and certificate that the client device uses to connect\. This object, `MQTTCredential`, contains the following information:    
`clientId` \(Python: `client_id`\)  
The client ID to use to connect\.  
`certificatePem` \(Python: `certificate_pem`\)  
The X\.509 device certificate to use to connect\.  
`username`  
This property isn't currently used\.  
`password`  
This property isn't currently used\.

### Response<a name="ipc-operation-getclientdeviceauthtoken-response"></a>

This operation's response has the following information:

`clientDeviceAuthToken` \(Python: `client_device_auth_token`\)  
The session token for the client device\. You can use this session token in subsequent requests to authorize this client device's actions\.

## AuthorizeClientDeviceAction<a name="ipc-operation-authorizeclientdeviceaction"></a>

Verify whether a client device has permission to perform an action on a resource\. *Client device authorization policies* specify the permissions that client devices can perform while connected to a core device\. You define client device authorization policies when you configure the [client device auth component](client-device-auth-component.md#client-device-auth-component-configuration)\.

### Request<a name="ipc-operation-authorizeclientdeviceaction-request"></a>

This operation's request has the following parameters:

`clientDeviceAuthToken` \(Python: `client_device_auth_token`\)  
The session token for the client device\.

`operation`  
The operation to authorize\.

`resource`  
The resource where the client device performs the operation\.

### Response<a name="ipc-operation-authorizeclientdeviceaction-response"></a>

This operation's response has the following information:

`isAuthorized` \(Python: `is_authorized`\)  
Whether the client device is authorized to perform the operation on the resource\.

## SubscribeToCertificateUpdates<a name="ipc-operation-subscribetocertificateupdates"></a>

Subscribe to receive the core device's new server certificate each time it rotates\. When the server certificate rotates, brokers must reload using the new server certificate\.

The [client device auth component](client-device-auth-component.md) rotates server certificates every 7 days by default\. You can configure the rotation interval to between 2 and 10 days\.

<a name="ipc-subscribe-operation-note"></a>This operation is a subscription operation where you subscribe to a stream of event messages\. To use this operation, define a stream response handler with functions that handle event messages, errors, and stream closure\. For more information, see [Subscribe to IPC event streams](interprocess-communication.md#ipc-subscribe-operations)\.

**Event message type:** `CertificateUpdateEvent`

### Request<a name="ipc-operation-subscribetocertificateupdates-request"></a>

This operation's request has the following parameters:

`certificateOptions` \(Python: `certificate_options`\)  
The types of certificate updates to subscribe to\. This object, `CertificateOptions`, contains the following information:    
`certificateType` \(Python: `certificate_type`\)  
The type of certificate updates to subscribe to\. Choose the following option:  
+ `SERVER`

### Response<a name="ipc-operation-subscribetocertificateupdates-response"></a>

This operation's response has the following information:

`messages`  
The stream of messages\. This object, `CertificateUpdateEvent`, contains the following information:    
`certificateUpdate` \(Python: `certificate_update`\)  
The information about the new certificate\. This object, `CertificateUpdate`, contains the following information:    
`certificate`  
The certificate\.  
`privateKey` \(Python: `private_key`\)  
The certificate's private key\.  
`publicKey` \(Python: `public_key`\)  
The certificate's public key\.  
`caCertificates` \(Python: `ca_certificates`\)  
The list of certificate authority \(CA\) certificates in the certificate's CA certificate chain\.