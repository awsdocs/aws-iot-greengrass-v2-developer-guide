# Secret manager<a name="secret-manager-component"></a>

The secret manager component \(`aws.greengrass.SecretManager`\) deploys secrets from AWS Secrets Manager to Greengrass core devices\. Use this component to securely use credentials, such as passwords, in custom components on your Greengrass core devices\. For more information about Secrets Manager, see [What is AWS Secrets Manager?](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) in the *AWS Secrets Manager User Guide*\.

This component encrypts secrets on the core device to keep your credentials and passwords secure until you need to use them\.

**Topics**
+ [Versions](#secret-manager-component-versions)
+ [Requirements](#secret-manager-component-requirements)
+ [Dependencies](#secret-manager-component-dependencies)
+ [Configuration](#secret-mananger-component-configuration)
+ [Changelog](#secret-manager-component-changelog)

## Versions<a name="secret-manager-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Requirements<a name="secret-manager-component-requirements"></a>

To deploy a component, you must meet the requirements for the component and its [dependencies](#secret-manager-component-dependencies)\. This component has the following requirements:
+ The [Greengrass device role](device-service-role.md) must allow the `secretsmanager:GetSecretValue` action, as shown in the following example IAM policy\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "secretsmanager:GetSecretValue"
        ],
        "Effect": "Allow",
        "Resource": [
          "arn:aws:secretsmanager:region:123456789012:secret:MySecret"
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

## Dependencies<a name="secret-manager-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. You must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#secret-manager-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ >=2\.0\.6 ]

The following table lists the dependencies for version 2\.0\.6 and later versions of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.2\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | ^2\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | ^2\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0  | Hard | 

------
#### [ >=2\.0\.4 <2\.0\.5 ]

The following table lists the dependencies for version 2\.0\.4 and 2\.0\.5 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.3 <2\.1\.0  | Hard | 
| [Lambda launcher](lambda-launcher-component.md) | >=1\.0\.0  | Hard | 
| [Lambda runtimes](lambda-runtimes-component.md) | >=1\.0\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) | >=1\.0\.0  | Hard | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

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

**Example: Configuration merge update**  

```
{
  "cloudSecrets": [
    {
      "arn": "arn:aws:secretsmanager:us-west-2:123456789012:secret:MyGreengrassSecret-abcdef"
    }
  ]
}
```

## Changelog<a name="secret-manager-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.6  |  Version updated for Greengrass nucleus version 2\.1\.0 release\.  | 
|  2\.0\.5  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/secret-manager-component.html)  | 
|  2\.0\.4  |  Initial version\.  | 