# Install AWS IoT Greengrass Core software with automatic resource provisioning<a name="quick-installation"></a>

The AWS IoT Greengrass Core software includes an installer that sets up your device as a Greengrass core device\. To set up a device quickly, the installer can provision the AWS IoT thing, AWS IoT thing group, IAM role, and AWS IoT role alias that the core device requires to operate\. The installer can also deploy the local development tools to the core device, so you can use the device to develop and test custom software components\. The installer requires AWS credentials to provision these resources and create the deployment\.

If you can't provide AWS credentials to the device, you can provision the AWS resources that the core device requires to operate\. You can also deploy the development tools to a core device to use as a development device\. This enables you to provide fewer permissions to the device when you run the installer\. For more information, see [Install AWS IoT Greengrass Core software with manual resource provisioning](manual-installation.md)\.

**Important**  <a name="install-greengrass-core-requirements-note"></a>
Before you download the AWS IoT Greengrass Core software, check that your core device meets the [requirements](setting-up.md#greengrass-v2-requirements) to install and run the AWS IoT Greengrass Core software v2\.0\.

**Topics**
+ [Provide AWS credentials to the device](#provide-installer-aws-credentials)
+ [Download the AWS IoT Greengrass Core software](#download-greengrass-core-v2)
+ [Install the AWS IoT Greengrass Core software](#run-greengrass-core-v2-installer)

## Provide AWS credentials to the device<a name="provide-installer-aws-credentials"></a>

Provide your AWS credentials to your device so that the installer can provision the required AWS resources\. For more information about the required permissions, see [Minimal IAM policy for installer to provision resources](provision-minimal-iam-policy.md)\.

**Note**  
The installer doesn't save or store your credentials\.

**To provide AWS credentials to the device**
+ On your device, provide AWS credentials by doing one of the following:<a name="installer-export-aws-credentials"></a>
  + Use long\-term credentials from an IAM user:

    1. Provide the access key ID and secret access key for your IAM user\. For more information about how to retrieve long\-term credentials, see [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the *IAM User Guide*\.

    1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

       ```
       export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
       export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
       ```
  + \(Recommended\) Use temporary security credentials from an IAM role:

    1. Provide the access key ID, secret access key, and session token from an IAM role that you assume\. For more information about how to retrieve these credentials, see [Using temporary security credentials with the AWS CLI](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html#using-temp-creds-sdk-cli) in the *IAM User Guide*\.

    1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

       ```
       export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
       export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
       export AWS_SESSION_TOKEN=AQoDYXdzEJr1K...o5OytwEXAMPLE=
       ```

## Download the AWS IoT Greengrass Core software<a name="download-greengrass-core-v2"></a>

You can download the latest version of the AWS IoT Greengrass Core software from the following location:
+ [https://d2s8p88vqu9w66\.cloudfront\.net/releases/greengrass\-nucleus\-latest\.zip](https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip)

**Note**  
You can download a specific version of the AWS IoT Greengrass Core software from the following location\. Replace *version* with the version to download\.  

```
https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-version.zip
```

**To download the AWS IoT Greengrass Core software \(Linux\)**

1. On your device, download the AWS IoT Greengrass Core software to a file named `greengrass-nucleus-latest-zip`\.

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

   <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

1. Unzip the AWS IoT Greengrass Core software to a folder on your device\. Replace *GreengrassCore* with the folder that you want to use\.

   ```
   unzip greengrass-nucleus-latest.zip -d GreengrassCore && rm greengrass-nucleus-latest.zip
   ```
**Tip**  
You can run the following command to see the version of the AWS IoT Greengrass Core software\.  

   ```
   java -jar ./GreengrassCore/lib/Greengrass.jar --version
   ```

## Install the AWS IoT Greengrass Core software<a name="run-greengrass-core-v2-installer"></a>

Run the installer with arguments that specify to do the following:
+ Create the AWS resources that the core device requires to operate\.
+ <a name="install-argument-component-default-user"></a>Use the `ggc_user` system user and `ggc_group` system group to run software components on the core device\. The installer creates this default user and group if they don't exist\.
+ <a name="install-argument-setup-system-service"></a>Install the software as a system service that runs on boot, if your device has the [systemd](https://en.wikipedia.org/wiki/Systemd) init system\.

To set up a development device with local development tools, specify the `--deploy-dev-tools true` argument\. The local development tools can take up to a minute to deploy after the installation completes\. 

For more information about the arguments that you can specify, see [Installer arguments](configure-installer.md)\.

**Note**  
<a name="jvm-tuning-note"></a>If you are running AWS IoT Greengrass on a device with limited memory, you can control the amount of memory that AWS IoT Greengrass Core software uses\. To control memory allocation, you can set JVM heap size options in the `jvmOptions` configuration parameter in your nucleus component\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

**To install the AWS IoT Greengrass Core software \(Linux\)**

1. Run the AWS IoT Greengrass Core installer\. Replace argument values in your command as follows\.<a name="installer-replace-arguments"></a>

   1. */greengrass/v2*: The path to the root folder to use to install the AWS IoT Greengrass Core software\.

   1. *GreengrassCore*\. The path to the folder where you unpacked the AWS IoT Greengrass Core software installer\.

   1. *region*\. The AWS Region in which to find or create resources\.

   1. *MyGreengrassCore*\. The name of the AWS IoT thing for your Greengrass core device\. If the thing doesn't exist, the installer creates it\. The installer downloads the certificates to authenticate as the AWS IoT thing\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.
**Note**  <a name="install-argument-thing-name-constraint"></a>
The thing name can't contain colon \(`:`\) characters\.

   1. *MyGreengrassCoreGroup*\. The name of AWS IoT thing group for your Greengrass core device\. If the thing group doesn't exist, the installer creates it and adds the thing to it\. If the thing group exists and has an active deployment, the core device downloads and runs the software that the deployment specifies\.
**Note**  <a name="install-argument-thing-group-name-constraint"></a>
The thing group name can't contain colon \(`:`\) characters\.

   1. *GreengrassV2TokenExchangeRole*\. The name of the IAM role that allows the Greengrass core device to get temporary AWS credentials\. If the role doesn't exist, the installer creates it and creates and attaches a policy named `GreengrassV2TokenExchangeRoleAccess`\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

   1. *GreengrassCoreTokenExchangeRoleAlias*\. The alias to the IAM role that allows the Greengrass core device to get temporary credentials later\. If the role alias doesn't exist, the installer creates it and points it to the IAM role that you specify\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

   ```
   sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE \
     -jar ./GreengrassCore/lib/Greengrass.jar \
     --aws-region region \
     --thing-name MyGreengrassCore \
     --thing-group-name MyGreengrassCoreGroup \
     --tes-role-name GreengrassV2TokenExchangeRole \
     --tes-role-alias-name GreengrassCoreTokenExchangeRoleAlias \
     --component-default-user ggc_user:ggc_group \
     --provision true \
     --setup-system-service true
   ```

   The installer prints the following messages if it succeeds:
   + If you specify `--provision`, the installer prints `Successfully configured Nucleus with provisioned resource details` if it configured the resources successfully\.
   + If you specify `--deploy-dev-tools`, the installer prints `Configured Nucleus to deploy aws.greengrass.Cli component` if it created the deployment successfully\.
   + If you specify `--setup-system-service true`, the installer prints `Successfully set up Nucleus as a system service` if it set up and ran the software as a service\.
   + If you don't specify `--setup-system-service true`, the installer prints `Launched Nucleus successfully` if it succeeded and ran the software\.

1. Skip this step if you installed [Greengrass nucleus](greengrass-nucleus-component.md) v2\.0\.4 or later\. If you downloaded the latest version of the software, you installed v2\.0\.4 or later\.

   Run the following command to set the required file permissions for your AWS IoT Greengrass Core software root folder\. Replace */greengrass/v2* with the root folder that you specified in your installation command, and replace */greengrass* with the parent folder for your root folder\.

   ```
   sudo chmod 755 /greengrass/v2 && sudo chmod 755 /greengrass
   ```

<a name="install-greengrass-core-run-software"></a>If you installed the AWS IoT Greengrass Core software as a system service, the installer runs the software for you\. Otherwise, you must run the software manually\. For more information, see [Run the AWS IoT Greengrass Core software](run-greengrass-core-v2.md)\.

**Note**  
By default, the IAM role that the installer creates doesn't allow access to component artifacts in S3 buckets\. To deploy custom components that define artifacts in Amazon S3, you must add permissions to the role to allow your core device to retrieve component artifacts\. For more information, see [Allow access to S3 buckets for component artifacts](device-service-role.md#device-service-role-access-s3-bucket)\.  
If you don't yet have an S3 bucket for component artifacts, you can add these permissions later after you create a bucket\.

<a name="install-greengrass-core-next-steps-intro"></a>For more information about how to configure and use the software and AWS IoT Greengrass, see the following:<a name="install-greengrass-core-next-steps-links"></a>
+ [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)
+ [Manage AWS IoT Greengrass components](manage-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
+ [Greengrass Command Line Interface](gg-cli.md)