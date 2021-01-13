# Retrieve secret values<a name="ipc-secret-manager"></a>

Use the secret manager IPC service to retrieve secret values from secrets on the core device\. You use the [secret manager component](secret-manager-component.md) to deploy encrypted secrets to core devices\. Then, you can use an IPC operation to decrypt the secret and use its value in your custom components\.

**Topics**
+ [Authorization](#ipc-secret-manager-authorization)
+ [Operations](#ipc-secret-manager-operations)

## Authorization<a name="ipc-secret-manager-authorization"></a>

To use secret manager in a custom component, you must define authorization policies that allow your component to get the value of secrets that you store on the core device\. Authorization policies for secret manager have the following properties\.

**IPC service identifier:** `aws.greengrass.SecretManager`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#GetSecretValue` or `*`  |  Allows a component to get the value of secrets that are encrypted on the core device\.  |  A Secrets Manager secret ARN, or `*` to allow access to all secrets\.  | 

## Operations<a name="ipc-secret-manager-operations"></a>

Use the following operation to get the values of secrets\.

**Topics**
+ [GetSecretValue](#ipc-operation-getsecretvalue)

### GetSecretValue<a name="ipc-operation-getsecretvalue"></a>

Gets the value of a secret that you store on the core device\.

This operation is similar to the Secrets Manager operation that you can use to get the value of a secret in the AWS Cloud\. For more information, see [GetSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html) in the *AWS Secrets Manager API Reference*\.

#### Request<a name="ipc-operation-getsecretvalue-request"></a>

This operation's request has the following parameters:

`secretId`  
The name of the secret to get\. You can specify either the Amazon Resource Name \(ARN\) or the friendly name of the secret\.

`versionId`  
\(Optional\) The ID of the version to get\.  
You can specify either `versionId` or `versionStage`\.  
If you don't specify `versionId` or `versionStage`, this operation defaults to the version with the `AWSCURRENT` label\.

`versionStage`  
\(Optional\) The staging label of the version to get\.  
You can specify either `versionId` or `versionStage`\.  
If you don't specify `versionId` or `versionStage`, this operation defaults to the version with the `AWSCURRENT` label\.

#### Response<a name="ipc-operation-getsecretvalue-response"></a>

This operation's response has the following information:

`secretId`  
The ID of the secret\.

`versionId`  
The ID of this version of the secret\.

`versionStage`  
The list of staging labels attached to this version of the secret\.

`secretValue`  
The value of this version of the secret\.

#### Examples<a name="ipc-operation-getsecretvalue-examples"></a>

The following example demonstrates how to call this operation in custom component code\.

```
GreengrassCoreIPCClient greengrassCoreIPCClient = new GreengrassCoreIPCClient(eventStreamRpcConnection);
GetSecretValueRequest request = new GetSecretValueRequest();
request.setSecretId(secretId);
request.setVersionStage(versionStage);
GetSecretValueResponse result = greengrassCoreIPCClient.getSecretValue(request, Optional.empty()).getResponse().get();
System.out.println("secretString=" + result.getSecretValue().getSecretString());
```