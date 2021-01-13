# Secret manager<a name="secret-manager-component"></a>

The secret manager component \(`aws.greengrass.SecretManager`\) deploys secrets from AWS Secrets Manager to Greengrass core devices\. Use this component to securely use credentials, such as passwords, in custom components on your Greengrass core devices\. For more information about Secrets Manager, see [What is AWS Secrets Manager?](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) in the *AWS Secrets Manager User Guide*\.

This component encrypts secrets on the core device to keep your credentials and passwords secure until you need to use them\.

This component has the following versions:
+ 2\.0\.x

## Requirements<a name="secret-manager-component-requirements"></a>

This component has the following requirements:
+ The [Greengrass device role](device-service-role.md) must allow the `secretmanager:GetSecretValue` action, as shown in the following example IAM policy\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "secretmanager:GetSecretValue"
        ],
        "Effect": "Allow",
        "Resource": [
          "aws:aws:secretsmanager:region:123456789012:secret:MySecret"
        ]
      }
    ]
  }
  ```
**Note**  
If you use a customer\-managed AWS Key Management Service key to encrypt secrets, the device role must also allow the `kms:Decrypt` action\.

  For more information about IAM policies for Secrets Manager, see the following in the *AWS Secrets Manager User Guide*:
  + [Authentication and access control for AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access.html)
  + [Actions, resources, and context keys you can use in an IAM policy or secret policy for AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html)
+ Custom components must define an authorization policy that allows `aws.greengrass#GetSecretValue` to get secrets that you store with this component\. In this authorization policy, you can restrict components' access to specific secrets\. For more information, see [secret manager IPC authorization](ipc-secret-manager.md#ipc-secret-manager-authorization)\.

## Configuration<a name="secret-mananger-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`cloudSecrets`  
A list of Secrets Manager secrets to deploy to the core device\. You can specify labels to define which versions of each secret to deploy\. If you don't specify a version, this component deploys the version with the staging label `AWSCURRENT` attached\. For more information, see [Staging labels](https://docs.aws.amazon.com/secretsmanager/latest/userguide/terms-concepts.html#term_staging-label) in the *AWS Secrets Manager User Guide*\.  
Each object contains the following information:    
`arn`  
The ARN of the secret to deploy\.  
`labels`  
\(Optional\) A list of labels to identify the versions of the secret to deploy to the core device\.  
Each label must be a string\.