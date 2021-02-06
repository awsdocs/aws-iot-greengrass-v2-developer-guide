# Authorize core devices to interact with AWS services<a name="device-service-role"></a>

AWS IoT Greengrass core devices use the AWS IoT Core credentials provider to authorize calls to AWS services\. The AWS IoT Core credentials provider enables devices to use their X\.509 certificates as the unique device identity to authenticate AWS requests\. This eliminates the need to store an AWS access key ID and secret access key on your AWS IoT Greengrass core devices\. For more information, see [Authorizing direct calls to AWS services](https://docs.aws.amazon.com/iot/latest/developerguide/authorizing-direct-aws.html) in the *AWS IoT Core Developer Guide*\.

When you run the AWS IoT Greengrass Core software, you can choose to provision the AWS resources that the core device requires\. This includes the AWS Identity and Access Management \(IAM\) role that your core device assumes through the AWS IoT Core credentials provider\. Use the `--provision true` argument to configure a role and policies that allow the core device to get temporary AWS credentials\. This argument also configures an AWS IoT role alias that points to this IAM role\. You can specify the name of the IAM role and AWS IoT role alias to use\. If you specify `--provision true` without these other name parameters, the Greengrass core device creates and uses the following default resources:
+ IAM role: `GreengrassV2TokenExchangeRole`

  This role has a policy named `GreengrassV2TokenExchangeRoleAccess` and a trust relationship that allows `credentials.iot.amazonaws.com` to assume the role\. The policy includes the minimum permissions for the core device\.
**Important**  
This policy doesn't include access to files in S3 buckets\. You must add permissions to the role to allow core devices to retrieve component artifacts from S3 buckets\. For more information, see [Access to S3 buckets for component artifacts](#device-service-role-access-s3-bucket)\.
+ AWS IoT role alias: `GreengrassV2TokenExchangeRoleAlias`

  This role alias refers to the IAM role\.

For more information, see [Install the AWS IoT Greengrass Core software](getting-started.md#install-greengrass-v2)\.

You can also set the role alias for an existing core device\. To do so, configure the `iotRoleAlias` configuration parameter of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

You can acquire temporary AWS credentials for this IAM role to perform AWS operations in your custom components\. For more information, see [Interact with AWS services](interact-with-aws-services.md)\.

**Topics**
+ [Service role permissions for core devices](#device-service-role-permissions)
+ [Access to S3 buckets for component artifacts](#device-service-role-access-s3-bucket)

## Service role permissions for core devices<a name="device-service-role-permissions"></a>

The role allows the following service to assume the role:
+ `credentials.iot.amazonaws.com`

If you use the AWS IoT Greengrass Core software to create this role, it uses the following permissions policy to allow core devices to connect and send logs to AWS\. The policy's name defaults to the name of the IAM role ending with `Access`\. For example, if you use the default IAM role name, then this policy's name is `GreengrassV2TokenExchangeRoleAccess`\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iot:DescribeCertificate",
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "logs:DescribeLogStreams",
        "iot:Connect",
        "iot:Publish",
        "iot:Subscribe",
        "iot:Receive",
        "s3:GetBucketLocation"
      ],
      "Resource": "*"
    }
  ]
}
```

## Access to S3 buckets for component artifacts<a name="device-service-role-access-s3-bucket"></a>

The default core device role doesn't allow core devices to access S3 buckets\. To deploy components that have artifacts in S3 buckets, you must add the `s3:GetObject` permission to allow core devices to download component artifacts\. You can add a new policy to the core device role to grant this permission\.

**To add a policy that allows access to component artifacts in Amazon S3**

1. Create a file called `component-artifact-policy.json` and copy the following JSON into the file\. This policy allows access to all files in an S3 bucket\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the S3 bucket to allow the core device to access\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "s3:GetObject"
         ],
         "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
       }
     ]
   }
   ```

1. Run the following command to create the policy from the policy document in `component-artifact-policy.json`\.

   ```
   aws iam create-policy \
     --policy-name MyGreengrassV2ComponentArtifactPolicy \
     --policy-document file://component-artifact-policy.json
   ```

   Copy the policy Amazon Resource Name \(ARN\) from the policy metadata in the output\. You use this ARN to attach this policy to the core device role in the next step\.

1. Run the following command to attach the policy to the core device role\. Replace *MyGreengrassV2TokenExchangeRole* with the name of the role that you specified when you ran the AWS IoT Greengrass Core software\. Then, replace the policy ARN with the ARN from the previous step\.

   ```
   aws iam attach-role-policy \
     --role-name GreengrassV2TokenExchangeRole \
     --policy-arn arn:aws:iam::123456789012:policy/MyGreengrassV2ComponentArtifactPolicy
   ```

   If the command has no output, it succeeded, and your core device can access artifacts that you upload to this S3 bucket\.