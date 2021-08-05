# AWS managed policies for AWS IoT Greengrass<a name="security-iam-aws-managed-policies"></a>

To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the **ReadOnlyAccess** AWS managed policy provides read\-only access to all AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.

**Topics**
+ [AWS managed policy: AWSGreengrassFullAccess](#aws-managed-policies-AWSGreengrassFullAccess)
+ [AWS managed policy: AWSGreengrassReadOnlyAccess](#aws-managed-policies-AWSGreengrassReadOnlyAccess)
+ [AWS managed policy: AWSGreengrassResourceAccessRolePolicy](#aws-managed-policies-AWSGreengrassResourceAccessRolePolicy)
+ [AWS IoT Greengrass updates to AWS managed policies](#aws-managed-policy-updates)

## AWS managed policy: AWSGreengrassFullAccess<a name="aws-managed-policies-AWSGreengrassFullAccess"></a>

You can attach the `AWSGreengrassFullAccess` policy to your IAM identities\.

This policy grants administrative permissions that allow a principal full access to all AWS IoT Greengrass actions\.

**Permissions details**

This policy includes the following permissions:
+ `greengrass` – Allows principals full access to all AWS IoT Greengrass actions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "greengrass:*"
            ],
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AWSGreengrassReadOnlyAccess<a name="aws-managed-policies-AWSGreengrassReadOnlyAccess"></a>

You can attach the `AWSGreengrassReadOnlyAccess` policy to your IAM identities\.

This policy grants read\-only permissions that allow a principal to view, but not modify, information in AWS IoT Greengrass\. For example, principals with these permissions can view the list of components deployed to a Greengrass core device, but can't create a deployment to change the components that run on that device\.

**Permissions details**

This policy includes the following permissions:
+ `greengrass` – Allows principals to perform actions that return either a list of items or details about an item\. This includes API operations that start with `List` or `Get`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "greengrass:List*",
                "greengrass:Get*"
            ],
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AWSGreengrassResourceAccessRolePolicy<a name="aws-managed-policies-AWSGreengrassResourceAccessRolePolicy"></a>

You can attach the `AWSGreengrassResourceAccessRolePolicy` policy to your IAM entities\. AWS IoT Greengrass also attaches this policy to a service role that allows AWS IoT Greengrass to perform actions on your behalf\. For more information, see [Greengrass service role](greengrass-service-role.md)\.

This policy grants administrative permissions that allow AWS IoT Greengrass to perform essential tasks, such as retrieving your Lambda functions, managing AWS IoT device shadows, and verifying Greengrass client devices\.

**Permissions details**

This policy includes the following permissions\.
+ `greengrass` – Manage Greengrass resources\.
+ `iot` \(`*Shadow`\) – Manage AWS IoT shadows that have special identifiers in their names\. These permissions are required so that AWS IoT Greengrass can communicate with core devices\.
+ `iot` \(`DescribeThing` and `DescribeCertificate`\) – Retrieve information about AWS IoT things and certificates\. These permissions are required so that AWS IoT Greengrass can verify client devices that connect to a core device\. For more information, see [Interact with local IoT devices](interact-with-local-iot-devices.md)\.
+ `lambda` – Retrieve information about AWS Lambda functions\. This permission is required so that AWS IoT Greengrass V1 can deploy Lambda functions to Greengrass cores\. For more information, see [Run Lambda function on the AWS IoT Greengrass core](https://docs.aws.amazon.com/greengrass/v1/developerguide/lambda-functions.html) in the *AWS IoT Greengrass V1 Developer Guide*\.
+ `secretsmanager` – Retrieve the value of AWS Secrets Manager secrets whose names start with `greengrass-`\. This permission is required so that AWS IoT Greengrass V1 can deploy Secrets Manager secrets to Greengrass cores\. For more information, see [Deploy secrets to the AWS IoT Greengrass core](https://docs.aws.amazon.com/greengrass/v1/developerguide/secrets.html) in the *AWS IoT Greengrass V1 Developer Guide*\.
+ `s3` – Retrieve files objects from S3 buckets whose names contain `greengrass` or `sagemaker`\. These permissions are required so that AWS IoT Greengrass V1 can deploy machine learning resources that you store in S3 buckets\. For more information, see [Machine learning resources](https://docs.aws.amazon.com/greengrass/v1/developerguide/ml-inference.html#ml-resources) in the *AWS IoT Greengrass V1 Developer Guide*\.
+ `sagemaker` – Retrieve information about Amazon SageMaker machine learning inference models\. This permission is required so that AWS IoT Greengrass V1 can deploy ML models to Greengrass cores\. For more information, see [Perform machine learning inference](https://docs.aws.amazon.com/greengrass/v1/developerguide/ml-inference.html) in the *AWS IoT Greengrass V1 Developer Guide*\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowGreengrassAccessToShadows",
            "Action": [
                "iot:DeleteThingShadow",
                "iot:GetThingShadow",
                "iot:UpdateThingShadow"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:iot:*:*:thing/GG_*",
                "arn:aws:iot:*:*:thing/*-gcm",
                "arn:aws:iot:*:*:thing/*-gda",
                "arn:aws:iot:*:*:thing/*-gci"
            ]
        },
        {
            "Sid": "AllowGreengrassToDescribeThings",
            "Action": [
                "iot:DescribeThing"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:iot:*:*:thing/*"
        },
        {
            "Sid": "AllowGreengrassToDescribeCertificates",
            "Action": [
                "iot:DescribeCertificate"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:iot:*:*:cert/*"
        },
        {
            "Sid": "AllowGreengrassToCallGreengrassServices",
            "Action": [
                "greengrass:*"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "AllowGreengrassToGetLambdaFunctions",
            "Action": [
                "lambda:GetFunction",
                "lambda:GetFunctionConfiguration"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "AllowGreengrassToGetGreengrassSecrets",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:secretsmanager:*:*:secret:greengrass-*"
        },
        {
            "Sid": "AllowGreengrassAccessToS3Objects",
            "Action": [
                "s3:GetObject"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::*Greengrass*",
                "arn:aws:s3:::*GreenGrass*",
                "arn:aws:s3:::*greengrass*",
                "arn:aws:s3:::*Sagemaker*",
                "arn:aws:s3:::*SageMaker*",
                "arn:aws:s3:::*sagemaker*"
            ]
        },
        {
            "Sid": "AllowGreengrassAccessToS3BucketLocation",
            "Action": [
                "s3:GetBucketLocation"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Sid": "AllowGreengrassAccessToSageMakerTrainingJobs",
            "Action": [
                "sagemaker:DescribeTrainingJob"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:sagemaker:*:*:training-job/*"
            ]
        }
    ]
}
```

## AWS IoT Greengrass updates to AWS managed policies<a name="aws-managed-policy-updates"></a>

You can view details about updates to AWS managed policies for AWS IoT Greengrass from the time this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the [AWS IoT Greengrass V2 document history page](document-history.md)\.


| Change | Description | Date | 
| --- | --- | --- | 
|  AWS IoT Greengrass started tracking changes  |  AWS IoT Greengrass started tracking changes for its AWS managed policies\.  |  July 2, 2021  | 