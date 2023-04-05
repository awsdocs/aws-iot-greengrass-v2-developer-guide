# Troubleshooting identity and access issues for AWS IoT Greengrass<a name="security_iam_troubleshoot"></a>

Use the following information to help you diagnose and fix common issues that you might encounter when working with AWS IoT Greengrass and IAM\.

**Topics**
+ [I'm not authorized to perform an action in AWS IoT Greengrass](#security_iam_troubleshoot-no-permissions)
+ [I'm not authorized to perform iam:PassRole](#security_iam_troubleshoot-passrole)
+ [I'm an administrator and want to allow others to access AWS IoT Greengrass](#security_iam_troubleshoot-admin-delegate)
+ [I want to allow people outside of my AWS account to access my AWS IoT Greengrass resources](#security_iam_troubleshoot-cross-account-access)

For general troubleshooting help, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\.

## I'm not authorized to perform an action in AWS IoT Greengrass<a name="security_iam_troubleshoot-no-permissions"></a>

If you receive an error that states you're not authorized to perform an action, you must contact your administrator for assistance\. Your administrator is the person who provided you with your user name and password\.

The following example error occurs when the `mateojackson` IAM user tries to view details about a core device, but does not have `greengrass:GetCoreDevice` permissions\.

```
User: arn:aws:iam::123456789012:user/mateojackson is not authorized to perform: greengrass:GetCoreDevice on resource: arn:aws:greengrass:us-west-2:123456789012:coreDevices/MyGreengrassCore
```

In this case, Mateo asks his administrator to update his policies to allow him to access the `arn:aws:greengrass:us-west-2:123456789012:coreDevices/MyGreengrassCore` resource using the `greengrass:GetCoreDevice` action\.

The following are general IAM issues that you might encounter when working with AWS IoT Greengrass\.

## I'm not authorized to perform iam:PassRole<a name="security_iam_troubleshoot-passrole"></a>

If you receive an error that you're not authorized to perform the `iam:PassRole` action, your policies must be updated to allow you to pass a role to AWS IoT Greengrass\.

Some AWS services allow you to pass an existing role to that service instead of creating a new service role or service\-linked role\. To do this, you must have permissions to pass the role to the service\.

The following example error occurs when an IAM user named `marymajor` tries to use the console to perform an action in AWS IoT Greengrass\. However, the action requires the service to have permissions that are granted by a service role\. Mary does not have permissions to pass the role to the service\.

```
User: arn:aws:iam::123456789012:user/marymajor is not authorized to perform: iam:PassRole
```

In this case, Mary's policies must be updated to allow her to perform the `iam:PassRole` action\.

If you need help, contact your AWS administrator\. Your administrator is the person who provided you with your sign\-in credentials\.

## I'm an administrator and want to allow others to access AWS IoT Greengrass<a name="security_iam_troubleshoot-admin-delegate"></a>

To allow others to access AWS IoT Greengrass, you must create an IAM entity \(user or role\) for the person or application that needs access\. They will use the credentials for that entity to access AWS\. You must then attach a policy to the entity that grants them the correct permissions in AWS IoT Greengrass\.

To get started right away, see [Creating your first IAM delegated user and group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-delegated-user.html) in the *IAM User Guide*\.

## I want to allow people outside of my AWS account to access my AWS IoT Greengrass resources<a name="security_iam_troubleshoot-cross-account-access"></a>

You can create an IAM role that users in other accounts or people outside of your organization can use to access your AWS resources\. You can specify the who is trusted to assume the role\. For more information, see [Providing access to an IAM user in another AWS account that you own](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_aws-accounts.html) and [Providing access to AWS accounts owned by third parties](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_third-party.html) in the *IAM User Guide*\.

AWS IoT Greengrass doesn't support cross\-account access based on resource\-based policies or access control lists \(ACLs\)\.