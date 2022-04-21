# Greengrass service role<a name="greengrass-service-role"></a>

<a name="greengrass-service-role-intro"></a>The Greengrass service role is an AWS Identity and Access Management \(IAM\) service role that authorizes AWS IoT Greengrass to access resources from AWS services on your behalf\. This role makes it possible for AWS IoT Greengrass to verify the identity of client devices and manage core device connectivity information\.

**Note**  
AWS IoT Greengrass V1 also uses this role to perform essential tasks\. For more information, see [Greengrass service role](https://docs.aws.amazon.com/greengrass/v1/developerguide/service-role.html) in the *AWS IoT Greengrass V1 Developer Guide*\.

To allow AWS IoT Greengrass to access your resources, the Greengrass service role must be associated with your AWS account and specify AWS IoT Greengrass as a trusted entity\. The role must include the [AWSGreengrassResourceAccessRolePolicy](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AWSGreengrassResourceAccessRolePolicy) managed policy or a custom policy that defines equivalent permissions for the AWS IoT Greengrass features that you use\. AWS maintains this policy, which defines the set of permissions that AWS IoT Greengrass uses to access your AWS resources\. For more information, see [AWS managed policy: AWSGreengrassResourceAccessRolePolicy](security-iam-aws-managed-policies.md#aws-managed-policies-AWSGreengrassResourceAccessRolePolicy)\.

You can reuse the same Greengrass service role across AWS Regions, but you must associate it with your account in every AWS Region where you use AWS IoT Greengrass\. If the service role isn't configured in the current AWS Region, core devices fail to verify client devices and fail to update connectivity information\.

The following sections describe how to create and manage the Greengrass service role with the AWS Management Console or AWS CLI\.

**Topics**
+ [Manage the Greengrass service role \(console\)](#manage-greengrass-service-role-console)
+ [Manage the Greengrass service role \(CLI\)](#manage-service-role-cli)
+ [See also](#service-role-see-also)

**Note**  
In addition to the service role that authorizes service\-level access, you assign a *token exchange role* to Greengrass core devices\. The token exchange role is a separate IAM role that controls how Greengrass components and Lambda functions on the core device can access AWS services\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

## Manage the Greengrass service role \(console\)<a name="manage-greengrass-service-role-console"></a>

The AWS IoT console makes it easy to manage your Greengrass service role\. For example, when you configure client device discovery for a core device, the console checks whether your AWS account is attached to a Greengrass service role in the current AWS Region\. If not, the console can create and configure a service role for you\. For more information, see [Create the Greengrass service role \(console\)](#create-greengrass-service-role-console)\.

You can use the console for the following role management tasks:

**Topics**
+ [Find your Greengrass service role \(console\)](#get-greengrass-service-role-console)
+ [Create the Greengrass service role \(console\)](#create-greengrass-service-role-console)
+ [Change the Greengrass service role \(console\)](#update-greengrass-service-role-console)
+ [Detach the Greengrass service role \(console\)](#remove-greengrass-service-role-console)

**Note**  
The user who is signed in to the console must have permissions to view, create, or change the service role\.

### Find your Greengrass service role \(console\)<a name="get-greengrass-service-role-console"></a>

Use the following steps to find the service role that AWS IoT Greengrass uses in the current AWS Region\.

1. <a name="open-iot-console"></a>Navigate to the [AWS IoT console](https://console.aws.amazon.com/iot)\.

1. <a name="open-iot-settings"></a>In the navigation pane, choose **Settings**\.

1. Scroll to the **Greengrass service role** section to see your service role and its policies\.

   If you don't see a service role, the console can create or configure one for you\. For more information, see [Create the Greengrass service role](#create-greengrass-service-role-console)\.

### Create the Greengrass service role \(console\)<a name="create-greengrass-service-role-console"></a>

The console can create and configure a default Greengrass service role for you\. This role has the following properties\.


| Property | Value | 
| --- | --- | 
| Name | Greengrass\_ServiceRole | 
| Trusted entity | AWS service: greengrass | 
| Policy | [AWSGreengrassResourceAccessRolePolicy](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AWSGreengrassResourceAccessRolePolicy) | 

**Note**  
If you create this role with the [AWS IoT Greengrass V1 device setup script](https://docs.aws.amazon.com/greengrass/v1/developerguide/quick-start.html), the role name is `GreengrassServiceRole_random-string`\.

When you configure client device discovery for a core device, the console checks whether a Greengrass service role is associated with your AWS account in the current AWS Region\. If not, the console prompts you to allow AWS IoT Greengrass to read and write to AWS services on your behalf\.

If you grant permission, the console checks whether a role named `Greengrass_ServiceRole` exists in your AWS account\.
+ If the role exists, the console attaches the service role to your AWS account in the current AWS Region\.
+ If the role doesn't exist, the console creates a default Greengrass service role and attaches it to your AWS account in the current AWS Region\.

**Note**  
If you want to create a service role with custom role policies, use the IAM console to create or modify the role\. For more information, see [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) or [Modifying a role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_modify.html) in the *IAM User Guide*\. Make sure that the role grants permissions that are equivalent to the `AWSGreengrassResourceAccessRolePolicy` managed policy for the features and resources that you use\. We recommend that you also include the `aws:SourceArn` and `aws:SourceAccount` global condition context keys in your trust policy to help prevent the *confused deputy* security problem\. The condition context keys restrict access to allow only those requests that come from the specified account and Greengrass workspace\. For more information about the confused deputy problem, see [Cross\-service confused deputy prevention](cross-service-confused-deputy-prevention.md)\.  
If you create a service role, return to the AWS IoT console and attach the role to your AWS account\. You can do this under **Greengrass service role** on the **Settings** page\.

### Change the Greengrass service role \(console\)<a name="update-greengrass-service-role-console"></a>

Use the following procedure to choose a different Greengrass service role to attach to your AWS account in the AWS Region currently selected in the console\.

1. <a name="open-iot-console"></a>Navigate to the [AWS IoT console](https://console.aws.amazon.com/iot)\.

1. <a name="open-iot-settings"></a>In the navigation pane, choose **Settings**\.

1. Under **Greengrass service role**, choose **Change role**\.

   The **Update Greengrass service role** dialog box opens and shows the IAM roles in your AWS account that define AWS IoT Greengrass as a trusted entity\.

1. Choose the Greengrass service role to attach\.

1. Choose **Attach role**\.

### Detach the Greengrass service role \(console\)<a name="remove-greengrass-service-role-console"></a>

Use the following procedure to detach the Greengrass service role from your AWS account in the current AWS Region\. This revokes permissions for AWS IoT Greengrass to access AWS services in the current AWS Region\.

**Important**  
Detaching the service role might interrupt active operations\.

1. <a name="open-iot-console"></a>Navigate to the [AWS IoT console](https://console.aws.amazon.com/iot)\.

1. <a name="open-iot-settings"></a>In the navigation pane, choose **Settings**\.

1. Under **Greengrass service role**, choose **Detach role**\.

1. In the confirmation dialog box, choose **Detach**\.

**Note**  
If you no longer need the role, you can delete it in the IAM console\. For more information, see [Deleting roles or instance profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_delete.html) in the *IAM User Guide*\.  
Other roles might allow AWS IoT Greengrass to access your resources\. To find all roles that allow AWS IoT Greengrass to assume permissions on your behalf, in the IAM console, on the **Roles** page, look for roles that include **AWS service: greengrass** in the **Trusted entities** column\.

## Manage the Greengrass service role \(CLI\)<a name="manage-service-role-cli"></a>

In the following procedures, we assume that the AWS Command Line Interface is installed and configured to use your AWS account\. For more information, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) and [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html) in the *AWS Command Line Interface User Guide*\.

You can use the AWS CLI for the following role management tasks:

**Topics**
+ [Get the Greengrass service role \(CLI\)](#get-service-role)
+ [Create the Greengrass service role \(CLI\)](#create-service-role)
+ [Remove the Greengrass service role \(CLI\)](#remove-service-role)

### Get the Greengrass service role \(CLI\)<a name="get-service-role"></a>

Use the following procedure to find out if a Greengrass service role is associated with your AWS account in an AWS Region\.
+ Get the service role\. Replace *region* with your AWS Region \(for example, `us-west-2`\)\.

  ```
  aws greengrassv2 get-service-role-for-account --region region
  ```

  If a Greengrass service role is already associated with your account, the request returns the following role metadata\.

  ```
  {
    "associatedAt": "timestamp",
    "roleArn": "arn:aws:iam::account-id:role/path/role-name"
  }
  ```

  If the request doesn't return role metadata, then you must create the service role \(if it doesn't exist\) and associate it with your account in the AWS Region\.

### Create the Greengrass service role \(CLI\)<a name="create-service-role"></a>

Use the following steps to create a role and associate it with your AWS account\.

**To create the service role using IAM**

1. Create a role with a trust policy that allows AWS IoT Greengrass to assume the role\. This example creates a role named `Greengrass_ServiceRole`, but you can use a different name\. We recommend that you also include the `aws:SourceArn` and `aws:SourceAccount` global condition context keys in your trust policy to help prevent the *confused deputy* security problem\. The condition context keys restrict access to allow only those requests that come from the specified account and Greengrass workspace\. For more information about the confused deputy problem, see [Cross\-service confused deputy prevention](cross-service-confused-deputy-prevention.md)\.

------
#### [ Linux or Unix ]

   ```
   aws iam create-role --role-name Greengrass_ServiceRole --assume-role-policy-document '{
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": "greengrass.amazonaws.com"
         },
         "Action": "sts:AssumeRole",
         "Condition": {
           "ArnLike": {
             "aws:SourceArn": "arn:aws:greengrass:region:account-id:*"
           },
           "StringEquals": {
             "aws:SourceAccount": "account-id"
           }
         }
       }
     ]
   }'
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws iam create-role --role-name Greengrass_ServiceRole --assume-role-policy-document "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"greengrass.amazonaws.com\"},\"Action\":\"sts:AssumeRole\",\"Condition\":{\"ArnLike\":{\"aws:SourceArn\":\"arn:aws:greengrass:region:account-id:*\"},\"StringEquals\":{\"aws:SourceAccount\":\"account-id\"}}}]}"
   ```

------
#### [ PowerShell ]

   ```
   aws iam create-role --role-name Greengrass_ServiceRole --assume-role-policy-document '{
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": "greengrass.amazonaws.com"
         },
         "Action": "sts:AssumeRole",
         "Condition": {
           "ArnLike": {
             "aws:SourceArn": "arn:aws:greengrass:region:account-id:*"
           },
           "StringEquals": {
             "aws:SourceAccount": "account-id"
           }
         }
       }
     ]
   }'
   ```

------

1. Copy the role ARN from the role metadata in the output\. You use the ARN to associate the role with your account\.

1. Attach the `AWSGreengrassResourceAccessRolePolicy` policy to the role\.

   ```
   aws iam attach-role-policy --role-name Greengrass_ServiceRole --policy-arn arn:aws:iam::aws:policy/service-role/AWSGreengrassResourceAccessRolePolicy
   ```

**To associate the service role with your AWS account**
+ Associate the role with your account\. Replace *role\-arn* with the service role ARN and *region* with your AWS Region \(for example, `us-west-2`\)\.

  ```
  aws greengrassv2 associate-service-role-to-account --role-arn role-arn --region region
  ```

  If successful, the request returns the following response\.

  ```
  {
    "associatedAt": "timestamp"
  }
  ```

### Remove the Greengrass service role \(CLI\)<a name="remove-service-role"></a>

Use the following steps to disassociate the Greengrass service role from your AWS account\.
+ Disassociate the service role from your account\. Replace *region* with your AWS Region \(for example, `us-west-2`\)\.

  ```
  aws greengrassv2 disassociate-service-role-from-account --region region
  ```

  If successful, the following response is returned\.

  ```
  {
    "disassociatedAt": "timestamp"
  }
  ```
**Note**  
You should delete the service role if you're not using it in any AWS Region\. First use [https://docs.aws.amazon.com/cli/latest/reference/iam/delete-role-policy.html](https://docs.aws.amazon.com/cli/latest/reference/iam/delete-role-policy.html) to detach the `AWSGreengrassResourceAccessRolePolicy` managed policy from the role, and then use [https://docs.aws.amazon.com/cli/latest/reference/iam/delete-role.html](https://docs.aws.amazon.com/cli/latest/reference/iam/delete-role.html) to delete the role\. For more information, see [Deleting roles or instance profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_delete.html) in the *IAM User Guide*\.

## See also<a name="service-role-see-also"></a>
+ [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*
+ [Modifying a role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_modify.html) in the *IAM User Guide*
+ [Deleting roles or instance profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_delete.html) in the *IAM User Guide*
+ AWS IoT Greengrass commands in the *AWS CLI Command Reference*
  + [associate\-service\-role\-to\-account](https://docs.aws.amazon.com/cli/latest/reference/greengrassv2/associate-service-role-to-account.html)
  + [disassociate\-service\-role\-from\-account](https://docs.aws.amazon.com/cli/latest/reference/greengrassv2/disassociate-service-role-from-account.html)
  + [get\-service\-role\-for\-account](https://docs.aws.amazon.com/cli/latest/reference/greengrassv2/get-service-role-for-account.html)
+ IAM commands in the *AWS CLI Command Reference*
  + [attach\-role\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/attach-role-policy.html)
  + [create\-role](https://docs.aws.amazon.com/cli/latest/reference/iam/create-role.html)
  + [delete\-role](https://docs.aws.amazon.com/cli/latest/reference/iam/delete-role.html)
  + [delete\-role\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/delete-role-policy.html)