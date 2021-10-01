# Minimal IAM policy for installer to provision resources<a name="provision-minimal-iam-policy"></a>

When you install the AWS IoT Greengrass Core software, you can provision required AWS resources, such as an AWS IoT thing and an IAM role for your device\. You can also deploy local development tools to the device\. The installer requires AWS credentials so that it can perform these actions in your AWS account\. For more information, see [Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\.

The following example policy includes the minimum set of actions that the installer requires to provision these resources\. These permissions are required if you specify the `--provision` argument for the installer\.

**Note**  
The `DeployDevTools` policy statement is required only if you specify the `--deploy-dev-tools` argument for the installer\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iot:AddThingToThingGroup",
        "iot:AttachPolicy",
        "iot:AttachThingPrincipal",
        "iot:CreateKeysAndCertificate",
        "iot:CreatePolicy",
        "iot:CreateRoleAlias",
        "iot:CreateThing",
        "iot:CreateThingGroup",
        "iot:DescribeEndpoint",
        "iot:DescribeRoleAlias",
        "iot:DescribeThingGroup",
        "iot:GetPolicy",
        "iam:GetRole",
        "iam:CreateRole",
        "iam:CreatePolicy",
        "iam:AttachRolePolicy",
        "iam:GetPolicy",
        "sts:GetCallerIdentity"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "iam:PassRole",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "iam:PassedToService": "iot.amazonaws.com"
        }
      }
    },
    {
      "Sid": "DeployDevTools",
      "Effect": "Allow",
      "Action": [
        "greengrass:CreateDeployment",
        "iot:CancelJob",
        "iot:CreateJob",
        "iot:DeleteThingShadow",
        "iot:DescribeJob",
        "iot:DescribeThing",
        "iot:DescribeThingGroup",
        "iot:GetThingShadow",
        "iot:UpdateJob",
        "iot:UpdateThingShadow"
      ],
      "Resource": "*"
    }
  ]
}
```