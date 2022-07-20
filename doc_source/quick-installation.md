# Install AWS IoT Greengrass Core software with automatic resource provisioning<a name="quick-installation"></a>

The AWS IoT Greengrass Core software includes an installer that sets up your device as a Greengrass core device\. To set up a device quickly, the installer can provision the AWS IoT thing, AWS IoT thing group, IAM role, and AWS IoT role alias that the core device requires to operate\. The installer can also deploy the local development tools to the core device, so you can use the device to develop and test custom software components\. The installer requires AWS credentials to provision these resources and create the deployment\.

If you can't provide AWS credentials to the device, you can provision the AWS resources that the core device requires to operate\. You can also deploy the development tools to a core device to use as a development device\. This enables you to provide fewer permissions to the device when you run the installer\. For more information, see [Install AWS IoT Greengrass Core software with manual resource provisioning](manual-installation.md)\.

**Important**  <a name="install-greengrass-core-requirements-note"></a>
Before you download the AWS IoT Greengrass Core software, check that your core device meets the [requirements](setting-up.md#greengrass-v2-requirements) to install and run the AWS IoT Greengrass Core software v2\.0\.

**Topics**
+ [Set up the device environment](#set-up-device-environment)
+ [Provide AWS credentials to the device](#provide-installer-aws-credentials)
+ [Download the AWS IoT Greengrass Core software](#download-greengrass-core-v2)
+ [Install the AWS IoT Greengrass Core software](#run-greengrass-core-v2-installer)

## Set up the device environment<a name="set-up-device-environment"></a>

Follow the steps in this section to set up a Linux or Windows device to use as your AWS IoT Greengrass core device\.

### Set up a Linux device<a name="set-up-linux-device-environment"></a><a name="set-up-linux-device-environment-procedure"></a>

**To set up a Linux device for AWS IoT Greengrass V2**

1. Install the Java runtime, which AWS IoT Greengrass Core software requires to run\. We recommend that you use [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\. The following commands show you how to install OpenJDK on your device\.
   + For Debian\-based or Ubuntu\-based distributions:

     ```
     sudo apt install default-jdk
     ```
   + For Red Hat\-based distributions:

     ```
     sudo yum install java-11-openjdk-devel
     ```
   + For Amazon Linux 2:

     ```
     sudo amazon-linux-extras install java-openjdk11
     ```

   When the installation completes, run the following command to verify that Java runs on your Raspberry Pi\.

   ```
   java -version
   ```

   The command prints the version of Java that runs on the device\. For example, on a Debian\-based distribution, the output might look similar to the following sample\.

   ```
   openjdk version "11.0.9.1" 2020-11-04
   OpenJDK Runtime Environment (build 11.0.9.1+1-post-Debian-1deb10u2)
   OpenJDK 64-Bit Server VM (build 11.0.9.1+1-post-Debian-1deb10u2, mixed mode)
   ```

1. \(Optional\) Create the default system user and group that runs components on the device\. You can also choose to let the AWS IoT Greengrass Core software installer create this user and group during installation with the `--component-default-user` installer argument\. For more information, see [Installer arguments](configure-installer.md)\.

   ```
   sudo useradd --system --create-home ggc_user
   sudo groupadd --system ggc_group
   ```

1. Verify that the user that runs the AWS IoT Greengrass Core software \(typically `root`\), has permission to run `sudo` with any user and any group\.

   1. Run the following command to open the `/etc/sudoers` file\.

      ```
      sudo visudo
      ```

   1. Verify that the permission for the user looks like the following example\.

      ```
      root    ALL=(ALL:ALL) ALL
      ```

1. \(Optional\) To [run containerized Lambda functions](run-lambda-functions.md), you must enable [cgroups](https://en.wikipedia.org/wiki/Cgroups) v1, and you must enable and mount the *memory* and *devices* cgroups\. If you don't plan to run containerized Lambda functions, you can skip this step\.

   To enable these cgroups options, boot the device with the following Linux kernel parameters\.

   ```
   cgroup_enable=memory cgroup_memory=1 systemd.unified_cgroup_hierarchy=0
   ```

   For information about viewing and setting kernel parameters for your device, see the documentation for your operating system and boot loader\. Follow the instructions to permanently set the kernel parameters\.
**Tip: Set kernel parameters on a Raspberry Pi**  
If your device is a Raspberry Pi, you can complete the following steps to view and update its Linux kernel parameters:  
Open the `/boot/cmdline.txt` file\. This file specifies Linux kernel parameters to apply when the Raspberry Pi boots\.  
For example, on a Linux\-based system, you can run the following command to use GNU nano to open the file\.  

      ```
      sudo nano /boot/cmdline.txt
      ```
Verify that the `/boot/cmdline.txt` file contains the following kernel parameters\. The `systemd.unified_cgroup_hierarchy=0` parameter specifies to use cgroups v1 instead of cgroups v2\.  

      ```
      cgroup_enable=memory cgroup_memory=1 systemd.unified_cgroup_hierarchy=0
      ```
If the `/boot/cmdline.txt` file doesn't contain these parameters, or it contains these parameters with different values, update the file to contain these parameters and values\.
If you updated the `/boot/cmdline.txt` file, reboot the Raspberry Pi to apply the changes\.  

      ```
      sudo reboot
      ```

1. Install all other required dependencies on your device as indicated by the list of requirements in [Device requirements](setting-up.md#greengrass-v2-requirements)\.

### Set up a Windows device<a name="set-up-windows-device-environment"></a>

**Note**  
This feature is available for v2\.5\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.<a name="set-up-windows-device-environment-procedure"></a>

**To set up a Windows device for AWS IoT Greengrass V2**

1. Install the Java runtime, which AWS IoT Greengrass Core software requires to run\. We recommend that you use [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\.

1. <a name="set-up-windows-device-environment-open-cmd"></a>Open the Windows Command Prompt \(`cmd.exe`\) as an administrator\.

1. <a name="set-up-windows-device-environment-create"></a>Create the default user in the LocalSystem account on the Windows device\. Replace *password* with a secure password\.

   ```
   net user /add ggc_user password
   ```

1. <a name="set-up-windows-device-psexec"></a>Download and install the [PsExec utility](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) from Microsoft on the device\. 

1. <a name="set-up-windows-device-credentials"></a>Use the PsExec utility to store the user name and password for the default user in the Credential Manager instance for the LocalSystem account\. Replace *password* with the user's password that you set earlier\.

   ```
   psexec -s cmd /c cmdkey /generic:ggc_user /user:ggc_user /pass:password
   ```

   If the **PsExec License Agreement** opens, choose **Accept** to agree to the license and run the command\.
**Note**  
On Windows devices, the LocalSystem account runs the Greengrass nucleus, and you must use the PsExec utility to store the default user information in the LocalSystem account\. Using the Credential Manager application stores this information in the Windows account of the currently logged on user, instead of the LocalSystem account\.

## Provide AWS credentials to the device<a name="provide-installer-aws-credentials"></a>

Provide your AWS credentials to your device so that the installer can provision the required AWS resources\. For more information about the required permissions, see [Minimal IAM policy for installer to provision resources](provision-minimal-iam-policy.md)\.

**To provide AWS credentials to the device**
+ <a name="installer-export-aws-credentials"></a>Provide your AWS credentials to the device so that the installer can provision the AWS IoT and IAM resources for your core device\. To increase security, we recommend that you get temporary credentials for an IAM role that allows only the minimum permissions necessary to provision\. For more information, see [Minimal IAM policy for installer to provision resources](provision-minimal-iam-policy.md)\.
**Note**  
The installer doesn't save or store your credentials\.

  On your device, do one of the following to retrieve credentials and make them available to the AWS IoT Greengrass Core software installer:
  + Use long\-term credentials from an IAM user:

    1. Provide the access key ID and secret access key for your IAM user\. For more information about how to retrieve long\-term credentials, see [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the *IAM User Guide*\.

    1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

------
#### [ Linux or Unix ]

       ```
       export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
       export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
       ```

------
#### [ Windows Command Prompt \(CMD\) ]

       ```
       set AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
       set AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
       ```

------
#### [ PowerShell ]

       ```
       $env:AWS_ACCESS_KEY_ID="AKIAIOSFODNN7EXAMPLE"
       $env:AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
       ```

------
  + \(Recommended\) Use temporary security credentials from an IAM role:

    1. Provide the access key ID, secret access key, and session token from an IAM role that you assume\. For more information about how to retrieve these credentials, see [Requesting temporary security credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html) in the *IAM User Guide*\.

    1. Run the following commands to provide the credentials to the AWS IoT Greengrass Core software\.

------
#### [ Linux or Unix ]

       ```
       export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
       export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
       export AWS_SESSION_TOKEN=AQoDYXdzEJr1K...o5OytwEXAMPLE=
       ```

------
#### [ Windows Command Prompt \(CMD\) ]

       ```
       set AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
       set AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
       set AWS_SESSION_TOKEN=AQoDYXdzEJr1K...o5OytwEXAMPLE=
       ```

------
#### [ PowerShell ]

       ```
       $env:AWS_ACCESS_KEY_ID="AKIAIOSFODNN7EXAMPLE"
       $env:AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
       $env:AWS_SESSION_TOKEN="AQoDYXdzEJr1K...o5OytwEXAMPLE="
       ```

------

## Download the AWS IoT Greengrass Core software<a name="download-greengrass-core-v2"></a>

You can download the latest version of the AWS IoT Greengrass Core software from the following location:
+ [https://d2s8p88vqu9w66\.cloudfront\.net/releases/greengrass\-nucleus\-latest\.zip](https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip)

**Note**  
You can download a specific version of the AWS IoT Greengrass Core software from the following location\. Replace *version* with the version to download\.  

```
https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-version.zip
```

**To download the AWS IoT Greengrass Core software**

1. <a name="installation-download-ggc-software-step"></a>On your core device, download the AWS IoT Greengrass Core software to a file named `greengrass-nucleus-latest.zip`\.

------
#### [ Linux or Unix ]

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

------
#### [ PowerShell ]

   ```
   iwr -Uri https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip -OutFile greengrass-nucleus-latest.zip
   ```

------

   <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

1. <a name="installation-unzip-ggc-software-step"></a>Unzip the AWS IoT Greengrass Core software to a folder on your device\. Replace *GreengrassInstaller* with the folder that you want to use\.

------
#### [ Linux or Unix ]

   ```
   unzip greengrass-nucleus-latest.zip -d GreengrassInstaller && rm greengrass-nucleus-latest.zip
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   mkdir GreengrassInstaller && tar -xf greengrass-nucleus-latest.zip -C GreengrassInstaller && del greengrass-nucleus-latest.zip
   ```

------
#### [ PowerShell ]

   ```
   Expand-Archive -Path greengrass-nucleus-latest.zip -DestinationPath .\GreengrassInstaller
   rm greengrass-nucleus-latest.zip
   ```

------

1. \(Optional\) Run the following command to see the version of the AWS IoT Greengrass Core software\.

   ```
   java -jar ./GreengrassInstaller/lib/Greengrass.jar --version
   ```

**Important**  <a name="installer-folder-2.4.0-warning"></a>
If you install a version of the Greengrass nucleus earlier than v2\.4\.0, don't remove this folder after you install the AWS IoT Greengrass Core software\. The AWS IoT Greengrass Core software uses the files in this folder to run\.  
If you downloaded the latest version of the software, you install v2\.4\.0 or later, and you can remove this folder after you install the AWS IoT Greengrass Core software\.

## Install the AWS IoT Greengrass Core software<a name="run-greengrass-core-v2-installer"></a>

Run the installer with arguments that specify to do the following:
+ <a name="install-argument-aws-resources"></a>Create the AWS resources that the core device requires to operate\.
+ <a name="install-argument-component-default-user"></a>Specify to use the `ggc_user` system user to run software components on the core device\. On Linux devices, this command also specifies to use the `ggc_group` system group, and the installer creates the system user and group for you\.
+ <a name="install-argument-system-service"></a>Set up the AWS IoT Greengrass Core software as a system service that runs as boot\. On Linux devices, this requires the [Systemd](https://en.wikipedia.org/wiki/Systemd) init system\.

To set up a development device with local development tools, specify the `--deploy-dev-tools true` argument\. The local development tools can take up to a minute to deploy after the installation completes\. 

For more information about the arguments that you can specify, see [Installer arguments](configure-installer.md)\.

**Note**  
<a name="jvm-tuning-note"></a>If you are running AWS IoT Greengrass on a device with limited memory, you can control the amount of memory that AWS IoT Greengrass Core software uses\. To control memory allocation, you can set JVM heap size options in the `jvmOptions` configuration parameter in your nucleus component\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

**To install the AWS IoT Greengrass Core software**

1. Run the AWS IoT Greengrass Core installer\. Replace argument values in your command as follows\.<a name="installer-replace-arguments"></a>

   1. */greengrass/v2* or *C:\\greengrass\\v2*: The path to the root folder to use to install the AWS IoT Greengrass Core software\.

   1. *GreengrassInstaller*\. The path to the folder where you unpacked the AWS IoT Greengrass Core software installer\.

   1. *region*\. The AWS Region in which to find or create resources\.

   1. *MyGreengrassCore*\. The name of the AWS IoT thing for your Greengrass core device\. If the thing doesn't exist, the installer creates it\. The installer downloads the certificates to authenticate as the AWS IoT thing\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.
**Note**  <a name="install-argument-thing-name-constraint"></a>
The thing name can't contain colon \(`:`\) characters\.

   1. *MyGreengrassCoreGroup*\. The name of AWS IoT thing group for your Greengrass core device\. If the thing group doesn't exist, the installer creates it and adds the thing to it\. If the thing group exists and has an active deployment, the core device downloads and runs the software that the deployment specifies\.
**Note**  <a name="install-argument-thing-group-name-constraint"></a>
The thing group name can't contain colon \(`:`\) characters\.

   1. *GreengrassV2IoTThingPolicy*\. The name of the AWS IoT policy that allows the Greengrass core devices to communicate with AWS IoT and AWS IoT Greengrass\. If the AWS IoT policy doesn't exist, the installer creates a permissive AWS IoT policy with this name\. You can restrict this policy's permissions for you use case\. For more information, see [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy)\.

   1. *GreengrassV2TokenExchangeRole*\. The name of the IAM role that allows the Greengrass core device to get temporary AWS credentials\. If the role doesn't exist, the installer creates it and creates and attaches a policy named `GreengrassV2TokenExchangeRoleAccess`\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

   1. *GreengrassCoreTokenExchangeRoleAlias*\. The alias to the IAM role that allows the Greengrass core device to get temporary credentials later\. If the role alias doesn't exist, the installer creates it and points it to the IAM role that you specify\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

------
#### [ Linux or Unix ]

   ```
   sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE \
     -jar ./GreengrassInstaller/lib/Greengrass.jar \
     --aws-region region \
     --thing-name MyGreengrassCore \
     --thing-group-name MyGreengrassCoreGroup \
     --thing-policy-name GreengrassV2IoTThingPolicy \
     --tes-role-name GreengrassV2TokenExchangeRole \
     --tes-role-alias-name GreengrassCoreTokenExchangeRoleAlias \
     --component-default-user ggc_user:ggc_group \
     --provision true \
     --setup-system-service true
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   java -Droot="C:\greengrass\v2" "-Dlog.store=FILE" ^
     -jar ./GreengrassInstaller/lib/Greengrass.jar ^
     --aws-region region ^
     --thing-name MyGreengrassCore ^
     --thing-group-name MyGreengrassCoreGroup ^
     --thing-policy-name GreengrassV2IoTThingPolicy ^
     --tes-role-name GreengrassV2TokenExchangeRole ^
     --tes-role-alias-name GreengrassCoreTokenExchangeRoleAlias ^
     --component-default-user ggc_user ^
     --provision true ^
     --setup-system-service true
   ```

------
#### [ PowerShell ]

   ```
   java -Droot="C:\greengrass\v2" "-Dlog.store=FILE" `
     -jar ./GreengrassInstaller/lib/Greengrass.jar `
     --aws-region region `
     --thing-name MyGreengrassCore `
     --thing-group-name MyGreengrassCoreGroup `
     --thing-policy-name GreengrassV2IoTThingPolicy `
     --tes-role-name GreengrassV2TokenExchangeRole `
     --tes-role-alias-name GreengrassCoreTokenExchangeRoleAlias `
     --component-default-user ggc_user `
     --provision true `
     --setup-system-service true
   ```

------

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
+ [Develop AWS IoT Greengrass components](develop-greengrass-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
+ [Greengrass Command Line Interface](gg-cli.md)