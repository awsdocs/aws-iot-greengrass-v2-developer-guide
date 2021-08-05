# Prerequisites for running the AWS IoT Greengrass qualification suite<a name="dev-tst-prereqs"></a>

This section describes the prerequisites for using AWS IoT Device Tester \(IDT\) for AWS IoT Greengrass\.

## Download the latest version of AWS IoT Device Tester for AWS IoT Greengrass<a name="install-dev-tst-gg"></a>

Download the [latest version](dev-test-versions.md) of IDT and extract the software into a location \(*<device\-tester\-extract\-location>*\) on your file system where you have read/write permissions\. 

**Note**  
<a name="unzip-package-to-local-drive"></a>IDT does not support being run by multiple users from a shared location, such as an NFS directory or a Windows network shared folder\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.  
Windows has a path length limitation of 260 characters\. If you are using Windows, extract IDT to a root directory like `C:\ ` or `D:\` to keep your paths under the 260 character limit\.

## Download the AWS IoT Greengrass software<a name="config-gg"></a>

IDT for AWS IoT Greengrass V2 tests your device for compatibility with a specific version of AWS IoT Greengrass\. Run the following command to download the AWS IoT Greengrass Core software to a file named `aws.greengrass.nucleus.zip`\. Replace *version* with a [supported nucleus component version](dev-test-versions.md) for your IDT version\. 

```
curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-version.zip > aws.greengrass.nucleus.zip
```

Place the downloaded `aws.greengrass.nucleus.zip` file in the `<device-tester-extract-location>/products/` folder\.

**Note**  
Do not place multiple files in this directory for the same operating system and architecture\. 

## Create and configure an AWS account<a name="config-aws-account-for-idt"></a>

Before you can use AWS IoT Device Tester for AWS IoT Greengrass V2, you must perform the following steps:

1. [Set up an AWS account\.](#create-aws-account-for-idt) If you already have an AWS account, skip to step 2\.

1. [Configure permissions for IDT\.](#configure-idt-permissions)

These account permissions allow IDT to access AWS services and create AWS resources, such as AWS IoT things and AWS IoT Greengrass components, on your behalf\.

<a name="idt-aws-credentials"></a>To create these resources, IDT for AWS IoT Greengrass V2 uses the AWS credentials configured in the `config.json` file to make API calls on your behalf\. These resources are provisioned at various times during a test\.

**Note**  
Although most tests qualify for [AWS Free Tier](http://aws.amazon.com/free), you must provide a credit card when you sign up for an AWS account\. For more information, see [ Why do I need a payment method if my account is covered by the Free Tier?](http://aws.amazon.com/premiumsupport/knowledge-center/free-tier-payment-method/)\.

### Step 1: Set up an AWS account<a name="create-aws-account-for-idt"></a>

In this step, create and configure an AWS account\. If you already have an AWS account, skip to [Step 2: Configure permissions for IDT](#configure-idt-permissions)\.

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user that follows and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add user**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \- job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

### Step 2: Configure permissions for IDT<a name="configure-idt-permissions"></a>

In this step, configure the permissions that IDT for AWS IoT Greengrass V2 uses to run tests and collect IDT usage data\. You can use the [AWS Management Console](#configure-idt-permissions-console) or [AWS Command Line Interface \(AWS CLI\)](#configure-idt-permissions-cli) to create an IAM policy and a test user for IDT, and then attach policies to the user\. If you already created a test user for IDT, skip to [Configure your device to run IDT tests](device-config-setup.md)\.

#### To configure permissions for IDT \(console\)<a name="configure-idt-permissions-console"></a>

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam)\.

1. Create a customer managed policy that grants permissions to create roles with specific permissions\. 

   1. In the navigation pane, choose **Policies**, and then choose **Create policy**\.

   1. On the **JSON** tab, replace the placeholder content with the following policy\.

      ```
      <a name="customer-managed-policy-cli"></a>{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/idt-*",
            "Condition": {
              "StringEquals": {
                "iam:PassedToService": [
                  "iot.amazonaws.com",
                  "lambda.amazonaws.com",
                  "greengrass.amazonaws.com"
                ]
              }
            }
          },
          {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": [
              "lambda:CreateFunction",
              "lambda:PublishVersion",
              "iot:DeleteCertificate",
              "lambda:DeleteFunction",
              "lambda:GetFunction",
              "execute-api:Invoke",
              "iot:UpdateCertificate"
            ],
            "Resource": [
              "arn:aws:execute-api:us-east-1:098862408343:9xpmnvs5h4/prod/POST/metrics",
              "arn:aws:lambda:*:*:function:idt-*",
              "arn:aws:iot:*:*:cert/*"
            ]
          },
          {
            "Sid": "VisualEditor3",
            "Effect": "Allow",
            "Action": [
              "iot:CreateThing",
              "iot:DeleteThing"
            ],
            "Resource": [
              "arn:aws:iot:*:*:thing/idt-*",
              "arn:aws:iot:*:*:cert/*"
            ]
          },
          {
            "Sid": "VisualEditor4",
            "Effect": "Allow",
            "Action": [
              "iot:AttachPolicy",
              "iot:DetachPolicy",
              "iot:DeletePolicy"
            ],
            "Resource": [
              "arn:aws:iot:*:*:policy/idt-*",
              "arn:aws:iot:*:*:cert/*"
            ]
          },
          {
            "Sid": "VisualEditor5",
            "Effect": "Allow",
            "Action": [
              "iot:CreateJob",
              "iot:DescribeJob",
              "iot:DescribeJobExecution",
              "iot:ListJobs*",
              "iot:DeleteJob"
            ],
            "Resource": [
              "arn:aws:iot:*:*:thing/idt-*",
              "arn:aws:iot:*:*:job/*",
              "arn:aws:iot:*:*:thinggroup/*"
            ]
          },
          {
            "Sid": "VisualEditor6",
            "Effect": "Allow",
            "Action": [
              "iot:DescribeEndpoint",
              "greengrass:*",
              "iam:ListAttachedRolePolicies",
              "iam:CreateRole",
              "iam:CreatePolicy",
              "iam:ListPolicies",
              "iam:ListRolePolicies",
              "iam:AttachRolePolicy",
              "iam:ListEntitiesForPolicy",
              "iam:GetRole",
              "iam:PassRole",
              "iam:DeleteRole",
              "iam:DeletePolicy",
              "iam:DetachRolePolicy",
              "iam:TagRole",
              "iam:TagPolicy",
              "iot:CreateRoleAlias",
              "iot:DescribeThingGroup",
              "iot:CreateThingGroup",
              "iot:AddThingToThingGroup",
              "iot:AttachPolicy",
              "iot:ListAttachedPolicies",
              "iot:CreateThing",
              "iot:DescribeRoleAlias",
              "iot:DescribeEndpoint",
              "iot:DeleteThing",
              "iot:DeleteThingGroup",
              "iot:DeleteRoleAlias",
              "iot:DeletePolicy",
              "iot:DetachPolicy",
              "iot:DetachThingPrincipal",
              "iot:RemoveThingFromThingGroup",
              "iot:UpdateCertificate",
              "iot:DescribeThing",
              "iot:DeleteCertificate",
              "iot:CreatePolicy",
              "iot:GetPolicy",
              "iot:GetThingShadow",
              "iot:CreateKeysAndCertificate",
              "iot:ListThings",
              "iot:UpdateThingShadow",
              "iot:Publish",
              "iot:CreateCertificateFromCsr",
              "iot:ListTagsForResource",
              "iot:TagResource",
              "iot:ListThingPrincipals",
              "iot-device-tester:SendMetrics",
              "iot-device-tester:SupportedVersion",
              "iot-device-tester:LatestIdt",
              "iot-device-tester:CheckVersion",
              "iot-device-tester:DownloadTestSuite"
            ],
            "Resource": "*"
          },
          {
            "Sid": "VisualEditor7",
            "Effect": "Allow",
            "Action": [
              "iot:DetachThingPrincipal",
              "iot:AttachThingPrincipal"
            ],
            "Resource": [
              "arn:aws:iot:*:*:thing/idt-*",
              "arn:aws:iot:*:*:cert/*"
            ]
          },
          {
            "Sid": "VisualEditor8",
            "Effect": "Allow",
            "Action": [
              "s3:GetObject",
              "s3:PutObject",
              "s3:AbortMultipartUpload",
              "s3:ListMultipartUploadParts",
              "s3:DeleteObjectVersion",
              "s3:PutObjectTagging",
              "s3:ListBucketVersions",
              "s3:CreateBucket",
              "s3:DeleteObject",
              "s3:DeleteBucket",
              "s3:ListBucket",
              "s3:PutBucketTagging"
            ],
            "Resource": "arn:aws:s3:::idt*"
          },
          {
            "Sid": "VisualEditor9",
            "Effect": "Allow",
            "Action": [
              "iot:CreateThingGroup",
              "iot:AddThingToThingGroup",
              "iot:DescribeThingGroup"
            ],
            "Resource": "arn:aws:iot:*:*:thinggroup/*"
          },
          {
            "Sid": "VisualEditor10",
            "Effect": "Allow",
            "Action": [
              "iot:AddThingToThingGroup"
            ],
            "Resource": "arn:aws:iot:*:*:thing/idt-*"
          },
          {
            "Sid": "VisualEditor11",
            "Effect": "Allow",
            "Action": [
              "iot:DescribeRoleAlias"
            ],
            "Resource": "arn:aws:iot:*:*:rolealias/*"
          }
        ]
      }
      ```

   1. Choose **Review policy**\.

   1. For **Name**, enter **IDTGreengrassIAMPermissions**\. Under **Summary**, review the permissions granted by your policy\.

   1. Choose **Create policy**\.

1. Create an IAM user and attach the permissions required by IDT for AWS IoT Greengrass\.

   1. Create an IAM user\. Follow steps 1 through 5 in [Creating IAM users \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console) in the *IAM User Guide*\.

   1. Attach the permissions to your IAM user:

      1. On the **Set permissions ** page, choose **Attach existing policies to user directly**\.

      1. Search for the **IDTGreengrassIAMPermissions** policy that you created in the previous step\. Select the check box\.

   1. Choose **Next: Tags**\.

   1. Choose **Next: Review** to view a summary of your choices\.

   1. Choose **Create user**\.

   1. To view the user's access keys \(access key IDs and secret access keys\), choose **Show** next to the password and access key\. To save the access keys, choose **Download\.csv** and save the file to a secure location\. You use this information later to configure your AWS credentials file\.

1. <a name="aws-account-config-next-steps"></a>Next step: Configure your [physical device](device-config-setup.md)\.

#### To configure permissions for IDT \(AWS CLI\)<a name="configure-idt-permissions-cli"></a>

1. On your computer, install and configure the AWS CLI if it's not already installed\. Follow the steps in [ Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.
**Note**  
The AWS CLI is an open source tool that you can use to interact with AWS services from your command\-line shell\.

1. Create a customer managed policy that grants permissions to manage IDT and AWS IoT Greengrass roles\.

   1. Open a text editor and save the following policy contents in a JSON file\. 

      ```
      <a name="customer-managed-policy-cli"></a>{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/idt-*",
            "Condition": {
              "StringEquals": {
                "iam:PassedToService": [
                  "iot.amazonaws.com",
                  "lambda.amazonaws.com",
                  "greengrass.amazonaws.com"
                ]
              }
            }
          },
          {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": [
              "lambda:CreateFunction",
              "lambda:PublishVersion",
              "iot:DeleteCertificate",
              "lambda:DeleteFunction",
              "lambda:GetFunction",
              "execute-api:Invoke",
              "iot:UpdateCertificate"
            ],
            "Resource": [
              "arn:aws:execute-api:us-east-1:098862408343:9xpmnvs5h4/prod/POST/metrics",
              "arn:aws:lambda:*:*:function:idt-*",
              "arn:aws:iot:*:*:cert/*"
            ]
          },
          {
            "Sid": "VisualEditor3",
            "Effect": "Allow",
            "Action": [
              "iot:CreateThing",
              "iot:DeleteThing"
            ],
            "Resource": [
              "arn:aws:iot:*:*:thing/idt-*",
              "arn:aws:iot:*:*:cert/*"
            ]
          },
          {
            "Sid": "VisualEditor4",
            "Effect": "Allow",
            "Action": [
              "iot:AttachPolicy",
              "iot:DetachPolicy",
              "iot:DeletePolicy"
            ],
            "Resource": [
              "arn:aws:iot:*:*:policy/idt-*",
              "arn:aws:iot:*:*:cert/*"
            ]
          },
          {
            "Sid": "VisualEditor5",
            "Effect": "Allow",
            "Action": [
              "iot:CreateJob",
              "iot:DescribeJob",
              "iot:DescribeJobExecution",
              "iot:ListJobs*",
              "iot:DeleteJob"
            ],
            "Resource": [
              "arn:aws:iot:*:*:thing/idt-*",
              "arn:aws:iot:*:*:job/*",
              "arn:aws:iot:*:*:thinggroup/*"
            ]
          },
          {
            "Sid": "VisualEditor6",
            "Effect": "Allow",
            "Action": [
              "iot:DescribeEndpoint",
              "greengrass:*",
              "iam:ListAttachedRolePolicies",
              "iam:CreateRole",
              "iam:CreatePolicy",
              "iam:ListPolicies",
              "iam:ListRolePolicies",
              "iam:AttachRolePolicy",
              "iam:ListEntitiesForPolicy",
              "iam:GetRole",
              "iam:PassRole",
              "iam:DeleteRole",
              "iam:DeletePolicy",
              "iam:DetachRolePolicy",
              "iam:TagRole",
              "iam:TagPolicy",
              "iot:CreateRoleAlias",
              "iot:DescribeThingGroup",
              "iot:CreateThingGroup",
              "iot:AddThingToThingGroup",
              "iot:AttachPolicy",
              "iot:ListAttachedPolicies",
              "iot:CreateThing",
              "iot:DescribeRoleAlias",
              "iot:DescribeEndpoint",
              "iot:DeleteThing",
              "iot:DeleteThingGroup",
              "iot:DeleteRoleAlias",
              "iot:DeletePolicy",
              "iot:DetachPolicy",
              "iot:DetachThingPrincipal",
              "iot:RemoveThingFromThingGroup",
              "iot:UpdateCertificate",
              "iot:DescribeThing",
              "iot:DeleteCertificate",
              "iot:CreatePolicy",
              "iot:GetPolicy",
              "iot:GetThingShadow",
              "iot:CreateKeysAndCertificate",
              "iot:ListThings",
              "iot:UpdateThingShadow",
              "iot:Publish",
              "iot:CreateCertificateFromCsr",
              "iot:ListTagsForResource",
              "iot:TagResource",
              "iot:ListThingPrincipals",
              "iot-device-tester:SendMetrics",
              "iot-device-tester:SupportedVersion",
              "iot-device-tester:LatestIdt",
              "iot-device-tester:CheckVersion",
              "iot-device-tester:DownloadTestSuite"
            ],
            "Resource": "*"
          },
          {
            "Sid": "VisualEditor7",
            "Effect": "Allow",
            "Action": [
              "iot:DetachThingPrincipal",
              "iot:AttachThingPrincipal"
            ],
            "Resource": [
              "arn:aws:iot:*:*:thing/idt-*",
              "arn:aws:iot:*:*:cert/*"
            ]
          },
          {
            "Sid": "VisualEditor8",
            "Effect": "Allow",
            "Action": [
              "s3:GetObject",
              "s3:PutObject",
              "s3:AbortMultipartUpload",
              "s3:ListMultipartUploadParts",
              "s3:DeleteObjectVersion",
              "s3:PutObjectTagging",
              "s3:ListBucketVersions",
              "s3:CreateBucket",
              "s3:DeleteObject",
              "s3:DeleteBucket",
              "s3:ListBucket",
              "s3:PutBucketTagging"
            ],
            "Resource": "arn:aws:s3:::idt*"
          },
          {
            "Sid": "VisualEditor9",
            "Effect": "Allow",
            "Action": [
              "iot:CreateThingGroup",
              "iot:AddThingToThingGroup",
              "iot:DescribeThingGroup"
            ],
            "Resource": "arn:aws:iot:*:*:thinggroup/*"
          },
          {
            "Sid": "VisualEditor10",
            "Effect": "Allow",
            "Action": [
              "iot:AddThingToThingGroup"
            ],
            "Resource": "arn:aws:iot:*:*:thing/idt-*"
          },
          {
            "Sid": "VisualEditor11",
            "Effect": "Allow",
            "Action": [
              "iot:DescribeRoleAlias"
            ],
            "Resource": "arn:aws:iot:*:*:rolealias/*"
          }
        ]
      }
      ```

   1. Run the following command to create a customer managed policy named `IDTGreengrassIAMPermissions`\. Replace `policy.json` with the full path to the JSON file that you created in the previous step\. 

      ```
      aws iam create-policy --policy-name IDTGreengrassIAMPermissions --policy-document file://policy.json
      ```

1. Create an IAM user and attach the permissions required by IDT for AWS IoT Greengrass\.

   1. Create an IAM user\. In this example setup, the user is named `IDTGreengrassUser`\.

      ```
      aws iam create-user --user-name IDTGreengrassUser
      ```

   1. Attach the `IDTGreengrassIAMPermissions` policy you created in step 2 to your IAM user\. Replace *<account\-id>* in the command with the ID of your AWS account\.

      ```
      aws iam attach-user-policy --user-name IDTGreengrassUser --policy-arn arn:aws:iam::<account-id>:policy/IDTGreengrassIAMPermissions
      ```

1. Create a secret access key for the user\.

   ```
   aws iam create-access-key --user-name IDTGreengrassUser
   ```

   Store the output in a secure location\. You use this information later to configure your AWS credentials file\.

1. <a name="aws-account-config-next-steps"></a>Next step: Configure your [physical device](device-config-setup.md)\.