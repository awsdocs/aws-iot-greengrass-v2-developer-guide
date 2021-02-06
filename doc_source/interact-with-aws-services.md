# Interact with AWS services<a name="interact-with-aws-services"></a>

Greengrass core devices use X\.509 certificates to connect to AWS IoT Core using TLS mutual authentication protocols\. This lets devices interact with AWS IoT without AWS credentials, which typically comprise an access key ID and a secret access key\. Other AWS services require AWS credentials instead of X\.509 certificates to call API operations at service endpoints\. AWS IoT Core has a credentials provider that enables devices to use their X\.509 certificate to authenticate AWS requests\. This eliminates the need to store AWS credentials on Greengrass core devices\. For more information, see [Authorizing direct calls to AWS services](https://docs.aws.amazon.com/iot/latest/developerguide/authorizing-direct-aws.html) in the *AWS IoT Core Developer Guide*\.

The AWS IoT credentials provider authenticates devices using an X\.509 certificate and issues AWS credentials in the form a temporary, limited\-privilege security token\. Devices can use this token to sign and authenticate any AWS request\. This requires you to create and configure an IAM role and attach appropriate IAM policies to the role\. The credentials provider assumes this role on your behalf to provide credentials to Greengrass core devices\. For more information about how to configure this role for Greengrass core devices, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

AWS IoT Greengrass V2 provides a public component, the token exchange service component, that you can define as a dependency in your custom component to interact with AWS services\. The token exchange service provides your component with an environment variable, `AWS_CONTAINER_CREDENTIALS_FULL_URI`, that defines the URI to a local server that provides AWS credentials\. When you create an AWS SDK client, the client checks for this environment variable and connects to the local server to retrieve AWS credentials and uses them to sign API requests\. This lets you use AWS SDKs and other tools to call AWS services in your components\. For more information, see [Token exchange service](token-exchange-service-component.md)\.

**Important**  <a name="token-exchange-service-aws-sdk-requirement"></a>
Support to acquire AWS credentials in this way was added to the AWS SDKs on July 13th, 2016\. Your component must use an AWS SDK version that was created on or after that date\. For more information, see [Using a supported AWS SDK](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html#task-iam-roles-minimum-sdk) in the *Amazon Elastic Container Service Developer Guide*\.

To acquire AWS credentials in your custom component, define `aws.greengrass.TokenExchangeService` as a dependency in the component recipe\. The following example recipe defines a component that installs [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) and runs a Python script that uses AWS credentials from the token exchange service to list Amazon S3 buckets\.

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.MyTokenExchangeServiceComponent",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "A component that uses the token exchange service to list S3 buckets.",
  "ComponentPublisher": "Amazon",
  "ComponentDependencies": {
    "aws.greengrass.TokenExchangeService": {
      "VersionRequirement": ">=0.0.0",
      "DependencyType": "HARD"
    }
  },
  "Manifests": [
    {
      "Lifecycle": {
        "Install": "pip3 install boto3",
        "Run": "python3 {artifacts:path}/list-s3-buckets.py"
      }
    }
  ]
}
```

------
#### [ YAML ]

```
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.MyTokenExchangeServiceComponent
ComponentVersion: '1.0.0'
ComponentDescription: A component that uses the token exchange service to list S3 buckets.
ComponentPublisher: Amazon
ComponentDependencies:
  aws.greengrass.TokenExchangeService:
    VersionRequirement: '>=0.0.0'
    DependencyType: HARD
Manifests:
  - Lifecycle:
      Install:
        pip3 install boto3
      Run: |-
        python3 {artifacts:path}/list-s3-buckets.py
```

------

This example component runs the following Python script that lists Amazon S3 buckets\.

```
import boto3
import os

try:
    print("Creating boto3 S3 client...")
    s3 = boto3.client('s3')
    print("Successfully created boto3 S3 client")
except Exception as e:
    print("Failed to create boto3 s3 client. Error: " + str(e))
    exit(1)

try:
    print("Listing S3 buckets...")
    response = s3.list_buckets()
    for bucket in response['Buckets']:
        print(f'\t{bucket["Name"]}')
    print("Successfully listed S3 buckets")
except Exception as e:
    print("Failed to list S3 buckets. Error: " + str(e))
    exit(1)
```