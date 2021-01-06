# Install the AWS IoT Greengrass Core software<a name="install-greengrass-core-v2"></a>

AWS IoT Greengrass extends AWS to edge devices so that they can act on the data they generate, while they use the AWS Cloud for management, analytics, and durable storage\. Install the AWS IoT Greengrass Core software on edge devices to integrate with AWS IoT Greengrass and the AWS Cloud\.

**Important**  
Before you download the AWS IoT Greengrass Core software, check that your core device meets the [requirements](setting-up.md#greengrass-v2-requirements) to install and run the AWS IoT Greengrass Core software v2\.0\.

## Download and run the installer<a name="run-installer"></a>

The AWS IoT Greengrass Core software includes an installer that sets up your device as a Greengrass core device\. When you run the installer, you can configure options such as the root folder and the AWS Region to use\. You can specify to provision the AWS IoT thing, \(optional\) AWS IoT thing group, IAM role, and AWS IoT role alias that the core device requires to operate\. The installer can also deploy the local development tools to the core device, so you can use the device to develop and test custom software components\. The installer requires AWS credentials to provision these resources and create the deployment\.

You can download the AWS IoT Greengrass Core software from the following location:
+ [https://d2s8p88vqu9w66\.cloudfront\.net/releases/greengrass\-nucleus\-latest\.zip](https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip)

**To download and install the AWS IoT Greengrass Core software**

1. Run the following command to download the AWS IoT Greengrass Core software to a file named `greengrass-nucleus-latest-zip`\.

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

   <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

1. Run the following command to unzip the AWS IoT Greengrass Core software to a folder on your device\. Replace *GreengrassCore* with the folder that you want to use\.

   ```
   unzip greengrass-nucleus-latest.zip -d GreengrassCore && rm greengrass-nucleus-latest.zip
   ```

1. When you run the installer, you can provision required AWS resources or deploy local development tools\. If you choose either of these options, the installer requires your AWS credentials so that it can create these AWS resources for you\. For more information, see [Minimal IAM policy to provision resources](#provision-minimal-iam-policy)\.

   Do one of the following to retrieve credentials and provide them to the installer\.<a name="installer-export-aws-credentials"></a>
   + \(Recommended\) Use temporary security credentials:

     1. Provide the access key ID, secret access key, and session token from an IAM role that you assume\. For more information about how to retrieve these credentials, see [Using temporary security credentials with the AWS CLI](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html#using-temp-creds-sdk-cli) in the *IAM User Guide*\.

     1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

        ```
        export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
        export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
        export AWS_SESSION_TOKEN=AQoDYXdzEJr1K...o5OytwEXAMPLE=
        ```
   + Use long\-term credentials:

     1. Provide the access key ID and secret access key for your IAM user\. For more information about how to retrieve long\-term credentials, see [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the *IAM User Guide*\.

     1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

        ```
        export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
        export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
        ```

   The installer doesn't save or store your credentials\.

1. Run the following command to install the AWS IoT Greengrass Core software\. This command does the following:
   + Creates the AWS resources that the core device requires to operate\.
   + Specifies to use the `ggc_user` system user and `ggc_group` system group to run software components on the core device\. The installer creates this default user and group for you\.
   + Specifies to install the software as a system service that runs on boot, if your device has the [systemd](https://en.wikipedia.org/wiki/Systemd) init system\.
**Note**  
To set up a development device with local development tools, specify the `--deploy-dev-tools true` argument\. The local development tools can take up to a minute to deploy after the installation completes\.   
<a name="jvm-tuning-note"></a>If you are running AWS IoT Greengrass on a device with limited memory, you might want to control the amount of memory that AWS IoT Greengrass Core software uses\. To control memory allocation, you can set JVM heap size options in the `jvmOptions` configuration parameter in your nucleus component\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

   For more information about the arguments that you can specify, see [Configure the installer](#configure-installer)\.

   Replace argument values in your command as follows\.<a name="installer-replace-arguments"></a>

   1. *GreengrassCore*: If you unpacked the installer to a different folder, replace this value with the folder that you want to use\.

   1. *region*: Replace with the AWS Region in which to find or create resources\.

   1. */greengrass/v2*: Replace with the path to the root folder to use to install the AWS IoT Greengrass Core software\.

   1. *MyGreengrassCore*: Replace with the name of the AWS IoT thing for your Greengrass core device\. If the thing doesn't exist, the installer creates it\. The installer downloads the certificates to authenticate as the AWS IoT thing\. 

   1. *MyGreengrassCoreGroup*: Replace with the name of AWS IoT thing group for your Greengrass core device\. If the thing group doesn't exist, the installer creates it and adds the thing to it\. If the thing group exists and has an active deployment, the core device downloads and runs the software which that deployment specifies\.

   1. *MyGreengrassV2TokenExchangeRole*: Replace with the name of the IAM role that allows the Greengrass core device to get temporary AWS credentials\. If the role doesn't exist, the installer creates it and creates and attaches a policy named `MyGreengrassV2TokenExchangeRoleAccess`\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

   1. *MyGreengrassCoreTokenExchangeRoleAlias*: Replace with the alias to the IAM role that allows the Greengrass core device to get temporary credentials later\. If the role alias doesn't exist, the installer creates it and points it to the IAM role that you specify\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

   ```
   sudo -E java -Dlog.store=FILE \
     -jar ./GreengrassCore/lib/Greengrass.jar \
     --aws-region region \
     --root /greengrass/v2 \
     --thing-name MyGreengrassCore \
     --thing-group-name MyGreengrassCoreGroup \
     --tes-role-name MyGreengrassV2TokenExchangeRole \
     --tes-role-alias-name MyGreengrassCoreTokenExchangeRoleAlias \
     --component-default-user ggc_user:ggc_group \
     --provision true \
     --setup-system-service true
   ```

   The installer prints the following messages if it succeeds:
   + If you specify `--provision`, the installer prints `Successfully configured Nucleus with provisioned resource details` if it configured the resources successfully\.
   + If you specify `--deploy-dev-tools`, the installer prints `Configured Nucleus to deploy aws.greengrass.Cli component` if it created the deployment successfully\.
   + If you specify `--setup-system-service true`, the installer prints `Successfully set up Nucleus as a system service` if it set up and ran the software as a service\.
   + If you don't specify `--setup-system-service true`, the installer prints `Launched Nucleus successfully` if it succeeded and ran the software\.

1. <a name="root-file-permissions"></a>Run the following command to set the required file permissions for your AWS IoT Greengrass Core software root folder\. Replace */greengrass/v2* with the root folder that you specified in your installation command and replace */greengrass* with the parent folder for your root folder\.

   ```
   sudo chmod 755 /greengrass/v2 && sudo chmod 755 /greengrass
   ```

For more information about how to configure and use the software and AWS IoT Greengrass, see the following:
+ [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)
+ [Manage AWS IoT Greengrass components](manage-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)

## Configure the installer<a name="configure-installer"></a>

The AWS IoT Greengrass Core software includes an installer that sets up the software and provisions the required AWS resources for the Greengrass core device to run\. The installer includes the following arguments that you can specify to configure the installation:

`-h`, `--help`  
\(Optional\) Show the installer's help information\.

`--version`  
\(Optional\) Show the version of the AWS IoT Greengrass Core software\.

`-ar`, `--aws-region`  
The AWS Region that the AWS IoT Greengrass Core software uses to retrieve or create its required AWS resources\.

`-r`, `--root`  
\(Optional\) The path to the folder to use as the root for the AWS IoT Greengrass Core software\.  
Default: `~/.greengrass`

`-p`, `--provision`  
\(Optional\) You can register this device as an AWS IoT thing and provision the AWS resources that the core device requires\. If you specify `true`, the AWS IoT Greengrass Core software provisions an AWS IoT thing, \(optional\) an AWS IoT thing group, an IAM role, and an AWS IoT role alias\.  
Default: `false`

`-tn`, `--thing-name`  
\(Optional\) The name of the AWS IoT thing that you register as this core device\. If the thing with the name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it\.  
You must specify `--provision true` to apply this argument\.  
Default: `GreengrassV2IotThing_` plus a random UUID\.

`-tgn`, `--thing-group-name`  
\(Optional\) The name of the AWS IoT thing group where you add this core device's AWS IoT thing\. If a deployment targets this thing group, this core device receives that deployment when it connects to AWS IoT Greengrass\. If the thing group with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core creates it\.  
You must specify `--provision true` to apply this argument\.

`-trn`, `--tes-role-name`  
\(Optional\) The name of the IAM role to use to acquire AWS credentials that let the core device interact with AWS services\. If the role with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it with the `GreengrassV2TokenExchangeRoleAccess` policy\. This role doesn't have access to your S3 buckets where you host component artifacts\. So, you must add permissions to your artifacts' S3 buckets and objects when you create a component\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.  
Default: `GreengrassV2TokenExchangeRole`

`-tra`, `--tes-role-alias-name`  
\(Optional\) The name of the AWS IoT role alias that points to the IAM role that provides AWS credentials for this core device\. If the role alias with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it and points it to the IAM role that you specify\.  
Default: `GreengrassV2TokenExchangeRoleAlias`

`-ss`, `--setup-system-service`  
\(Optional\) You can set up the AWS IoT Greengrass Core software as a system service that runs when this device boots\. The system service name is `greengrass`\. For more information, see [Configure AWS IoT Greengrass as a system service](configure-greengrass-core-v2.md#configure-system-service)\.  
This argument requires that the systemd init system is available on the device\.  
Default: `false`

`-u`, `--component-default-user`  
\(Optional\) The name or ID of the system user and group that the AWS IoT Greengrass Core software uses to run components\. Specify the user and group separated by a colon\. The group is optional\. For example, you can specify **ggc\_user:ggc\_group** or **ggc\_user**\.  
+ If you run as root, this defaults to the user and group that the configuration file defines\. If the configuration file doesn't define a user and group, this defaults to `ggc_user:ggc_group`\. If `ggc_user` or `ggc_group` don't exist, the software creates them\.
+ If you run as a non\-root user, the AWS IoT Greengrass Core software uses that user to run components\.
+ If you don't specify a group, the AWS IoT Greengrass Core software uses the primary group of the system user\.
For more information, see [Configure the user and group that run components](configure-greengrass-core-v2.md#configure-component-user)\.

`-d`, `--deploy-dev-tools`  
\(Optional\) You can download and deploy the [Greengrass CLI](greengrass-cli-component.md) component to this core device\. You can use this tools to develop and debug components on this core device\.  
The local development tools are intended for use only in development environments\. Don't specify this option on production devices\.
Default: `false`

`-s`, `--start`  
\(Optional\) You can start the AWS IoT Greengrass Core software after it installs and, optionally, provisions resources\.  
Default: `true`

## Minimal IAM policy to provision resources<a name="provision-minimal-iam-policy"></a>

When you install the AWS IoT Greengrass Core software, you can provision required AWS resources, such as an AWS IoT thing and an IAM role for your device\. You can also deploy local development tools to the device\. The installer requires AWS credentials so that it can perform these actions in your AWS account\.

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
        "iam:PassRole",
        "iam:CreatePolicy",
        "iam:AttachRolePolicy"
      ],
      "Resource": "*"
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