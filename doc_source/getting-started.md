# Getting started with AWS IoT Greengrass V2<a name="getting-started"></a>

You can complete this getting started tutorial to learn about the basic features of AWS IoT Greengrass V2\. In this tutorial, you do the following:
+ Install and configure the AWS IoT Greengrass Core software on a Linux device, such as a Raspberry Pi, or a Windows device\. This device is a Greengrass core device\.
+ Develop a Hello World component on your Greengrass core device\. Components are software modules that run on Greengrass core devices\.
+ Upload that component to AWS IoT Greengrass V2 in the AWS Cloud\.
+ Deploy that component from the AWS Cloud to your Greengrass core device\.

**Note**  
This tutorial describes how to set up a development environment and explore the features of AWS IoT Greengrass\. For more information about how to set up and configure production devices, see the following:  
[Setting up AWS IoT Greengrass core devices](setting-up.md)
[Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)

You can expect to spend 20 to 30 minutes on this tutorial\.

## Prerequisites<a name="getting-started-prerequisites"></a>

To complete this getting started tutorial, you need the following:
+ An AWS account\. If you don't have one, see [Step 1: Set up an AWS account](#getting-started-set-up-aws-account)\.
+ <a name="requirement-supported-region"></a>The use of an [AWS Region](https://en.wikipedia.org/wiki/Amazon_Web_Services#Availability_and_topology) that supports AWS IoT Greengrass V2\. For the list of supported Regions, see [AWS IoT Greengrass V2 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/greengrassv2.html) in the *AWS General Reference*
+ An AWS Identity and Access Management \(IAM\) user with administrator permissions\.
+ A Windows, macOS, or Unix\-like development computer with an internet connection\.
+ A device to set up as a Greengrass core device, such as a Raspberry Pi with [Raspberry Pi OS](https://www.raspberrypi.org/downloads/) \(previously called Raspbian\), or a Windows 10 device\. You must have administrator permissions on this device, or the ability to acquire administrator privileges, such as through `sudo`\.

  You can also choose to use a different device that meets the requirements to install and run the AWS IoT Greengrass Core software and has an internet connection to the same network as your development computer\. For more information, see the environment setup information for your device type in [Setting up AWS IoT Greengrass core devices](setting-up.md)\.

  If your development computer meets these requirements, you can set it up as your Greengrass core device in this tutorial\.
+ [Python](https://www.python.org/downloads/) 3\.5 or later installed for all users on the device and added to the `PATH` environment variable\. On Windows, you must also have the Python Launcher for Windows installed for all users\.
**Important**  
On Windows, Python doesn't install for all users by default\. When you install Python, you must customize the installation to configure it for the AWS IoT Greengrass Core software to run Python scripts\. For example, if you use the graphical Python installer, do the following:  
Select **Install launcher for all users \(recommended\)**\.
Choose **Customize installation**\.
Choose **Next**\.
Select **Install for all users**\.
Select **Add Python to environment variables**\.
Choose **Install**\.
For more information, see [Using Python on Windows](https://docs.python.org/3/using/windows.html) in the *Python 3 documentation*\.
+ AWS Command Line Interface \(AWS CLI\) installed and configured with credentials on your development computer and on your device\. Make sure you use the same AWS Region to configure the AWS CLI on your development computer and on your device\. To use AWS IoT Greengrass V2 with the AWS CLI, you must have one of the following versions or later:<a name="minimum-aws-cli-versions"></a>
  + Minimum AWS CLI V1 version: v1\.18\.197
  + Minimum AWS CLI V2 version: v2\.1\.11
**Tip**  <a name="tip-check-aws-cli-version"></a>
You can run the following command to check the version of the AWS CLI that you have\.  

  ```
  aws --version
  ```

  For more information, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS Command Line Interface User Guide*\.
**Note**  
If you use a Raspberry Pi or another 32\-bit ARM device, install AWS CLI V1\. AWS CLI V2 isn't available for 32\-bit ARM devices\. For more information, see [Installing, updating, and uninstalling the AWS CLI version 1](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv1.html)\.

## Step 1: Set up an AWS account<a name="getting-started-set-up-aws-account"></a>

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

## Step 2: Set up your environment<a name="getting-started-set-up-environment"></a>

Follow the steps in this section to set up a Linux or Windows device to use as your AWS IoT Greengrass core device\.

### Set up a Linux device \(Raspberry Pi\)<a name="getting-started-set-up-raspberry-pi"></a>

These steps assume that you use a Raspberry Pi with Raspberry Pi OS\. If you use a different device or operating system, consult the relevant documentation for your device\.

**To set up a Raspberry Pi for AWS IoT Greengrass V2**

1. Enable SSH on your Raspberry Pi to remotely connect to it\. For more information, see [SSH \(Secure shell\)](https://www.raspberrypi.org/documentation/remote-access/ssh/) in the *Raspberry Pi Documentation*\.

1. Find the IP address of your Raspberry Pi to connect to it with SSH\. To do so, you can run the following command on your Raspberry Pi\.

   ```
   hostname -I
   ```

1. Connect to your Raspberry Pi with SSH\. 

   On your development computer, run the following command\. Replace *username* with the name of the user to sign in, and replace *pi\-ip\-address* with the IP address that you found in the previous step\.

   ```
   ssh username@pi-ip-address
   ```

   The default Raspberry Pi user name and password are **pi** and **raspberry**, respectively\.
**Important**  
If your development computer uses an earlier version of Windows, you might not have the `ssh` command, or you might have `ssh` but can't connect to your Raspberry Pi\. To connect to your Raspberry Pi, you can install and configure [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), which is a no\-cost, open source SSH client\. Consult the [PuTTY documentation](https://tartarus.org/~simon/putty-snapshots/htmldoc/Chapter2.html#gs) to connect to your Raspberry Pi\.

1. Install the Java runtime, which AWS IoT Greengrass Core software requires to run\. On your Raspberry Pi, use the following commands to install Java 11\.

   ```
   sudo apt install default-jdk
   ```

   When the installation completes, run the following command to verify that Java runs on your Raspberry Pi\.

   ```
   java -version
   ```

   The command prints the version of Java that runs on the device\. The output might look similar to the following example\.

   ```
   openjdk version "11.0.9.1" 2020-11-04
   OpenJDK Runtime Environment (build 11.0.9.1+1-post-Debian-1deb10u2)
   OpenJDK 64-Bit Server VM (build 11.0.9.1+1-post-Debian-1deb10u2, mixed mode)
   ```

### Set up a Windows device<a name="getting-started-set-up-windows"></a><a name="set-up-windows-device-environment-procedure"></a>

**To set up a Windows device for AWS IoT Greengrass V2**

1. Install the Java runtime, which AWS IoT Greengrass Core software requires to run\. We recommend that you use [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\.\. You must use a 64\-bit version of the Java runtime on Windows devices\.

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

## Step 3: Install the AWS IoT Greengrass Core software<a name="install-greengrass-v2"></a>

Follow the steps in this section to set up your Raspberry Pi as a AWS IoT Greengrass core device that you can use for local development\. In this section, you download and run an installer that does the following to configure the AWS IoT Greengrass Core software for your device:
+ Installs the Greengrass nucleus component\. The nucleus is a mandatory component and is the minimum requirement to run the AWS IoT Greengrass Core software on a device\. For more information, see [Greengrass nucleus component](greengrass-nucleus-component.md)\.
+ Registers your device as an AWS IoT thing and downloads a digital certificate that allows your device to connect to AWS\. For more information, see [Device authentication and authorization for AWS IoT Greengrass](device-auth.md)\.
+ Adds the device's AWS IoT thing to a thing group, which is a group or fleet of AWS IoT things\. Thing groups enable you to manage fleets of Greengrass core devices\. When you deploy software components to your devices, you can choose to deploy to individual devices or to groups of devices\. For more information, see [Managing devices with AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/iot-thing-management.html) in the *AWS IoT Core Developer Guide*\.
+ Creates the IAM role that allows your Greengrass core device to interact with AWS services\. By default, this role allows your device to interact with AWS IoT and send logs to Amazon CloudWatch Logs\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.
+ Installs the AWS IoT Greengrass command line interface \(`greengrass-cli`\), which you can use to test custom components that you develop on the core device\. For more information, see [Greengrass Command Line Interface](gg-cli.md)\.

### Install the AWS IoT Greengrass Core software \(console\)<a name="install-greengrass-v2-console"></a>

1. Sign in to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. 

1. Under **Get started with Greengrass**, choose **Set up one core device**\.

1. Under **Step 1: Register a Greengrass core device**, for **Core device name**, enter the name of the AWS IoT thing for your Greengrass core device\. If the thing doesn't exist, the installer creates it\.

1. Under **Step 2: Add to a thing group to apply a continuous deployment**, for **Thing group**, choose the AWS IoT thing group to which you want to add your core device\. 
   + If you select **Enter a new group name**, then in **Thing group name**, enter the name of the new group to create\. The installer creates the new group for you\.
   + If you select **Select an existing group**, then in **Thing group name**, choose the existing group that you want to use\.
   + If you select **No group**, then the installer doesn't add the core device to a thing group\.

1. Under **Step 3: Install the Greengrass Core software**, complete the following steps\.

   1. Choose your core device's operating system: **Linux** or **Windows**\.

   1. <a name="installer-export-aws-credentials"></a>Provide your AWS credentials to the device so that the installer can provision the AWS IoT and IAM resources for your core device\. To increase security, you can get credentials for an IAM role that allows only the minimum permissions necessary to provision\. For more information, see [Minimal IAM policy for installer to provision resources](provision-minimal-iam-policy.md)\.
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

        1. Provide the access key ID, secret access key, and session token from an IAM role that you assume\. For more information about how to retrieve these credentials, see [Using temporary security credentials with the AWS CLI](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html#using-temp-creds-sdk-cli) in the *IAM User Guide*\.

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

   1. Under **Run the installer**, complete the following steps\.

      1. Under **Download the installer**, choose **Copy** and run the copied command on your core device\. This command downloads the latest version of the AWS IoT Greengrass Core software and unzips it on your device\.

      1. Under **Run the installer**, choose **Copy**, and run the copied command on your core device\. This command uses the AWS IoT thing and thing group names that you specified earlier to run the AWS IoT Greengrass Core software installer and set up AWS resources for your core device\.

         This command also does the following:
         + <a name="install-argument-system-service"></a>Set up the AWS IoT Greengrass Core software as a system service that runs as boot\. On Linux devices, this requires the [Systemd](https://en.wikipedia.org/wiki/Systemd) init system\.
         + <a name="install-argument-dev-tools"></a>Deploy the [AWS IoT Greengrass CLI component](gg-cli.md), which is a command\-line tool that enables you to develop custom Greengrass components on the core device\.
         + <a name="install-argument-component-default-user"></a>Specify to use the `ggc_user` system user to run software components on the core device\. On Linux devices, this command also specifies to use the `ggc_group` system group, and the installer creates the system user and group for you\.

         When you run this command, you should see the following messages to indicate that the installer succeeded\.

         ```
         Successfully configured Nucleus with provisioned resource details!
         Configured Nucleus to deploy aws.greengrass.Cli component
         Successfully set up Nucleus as a system service
         ```
**Note**  <a name="installer-linux-no-systemd-message"></a>
If you have a Linux device and it doesn't have [systemd](https://en.wikipedia.org/wiki/Systemd), the installer won't set up the software as a system service, and you won't see the success message for setting up the nucleus as a system service\.

### Install the AWS IoT Greengrass Core software \(CLI\)<a name="install-greengrass-v2-cli"></a>

**To install and configure the AWS IoT Greengrass Core software**

1. On your Greengrass core device, run the following command to switch to the home directory\.

------
#### [ Linux or Unix ]

   ```
   cd ~
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   cd %USERPROFILE%
   ```

------
#### [ PowerShell ]

   ```
   cd ~
   ```

------

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

1. Run the following command to launch the AWS IoT Greengrass Core software installer\. This command does the following:
   + <a name="install-argument-aws-resources"></a>Create the AWS resources that the core device requires to operate\.
   + <a name="install-argument-system-service"></a>Set up the AWS IoT Greengrass Core software as a system service that runs as boot\. On Linux devices, this requires the [Systemd](https://en.wikipedia.org/wiki/Systemd) init system\.
   + <a name="install-argument-dev-tools"></a>Deploy the [AWS IoT Greengrass CLI component](gg-cli.md), which is a command\-line tool that enables you to develop custom Greengrass components on the core device\.
   + <a name="install-argument-component-default-user"></a>Specify to use the `ggc_user` system user to run software components on the core device\. On Linux devices, this command also specifies to use the `ggc_group` system group, and the installer creates the system user and group for you\.

   Replace argument values in your command as follows\.<a name="installer-replace-arguments"></a>

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
     --setup-system-service true \
     --deploy-dev-tools true
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
     --setup-system-service true ^
     --deploy-dev-tools true
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
     --setup-system-service true `
     --deploy-dev-tools true
   ```

------
**Note**  
<a name="jvm-tuning-note"></a>If you are running AWS IoT Greengrass on a device with limited memory, you can control the amount of memory that AWS IoT Greengrass Core software uses\. To control memory allocation, you can set JVM heap size options in the `jvmOptions` configuration parameter in your nucleus component\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

   When you run this command, you should see the following messages to indicate that the installer succeeded\.

   ```
   Successfully configured Nucleus with provisioned resource details!
   Configured Nucleus to deploy aws.greengrass.Cli component
   Successfully set up Nucleus as a system service
   ```
**Note**  <a name="installer-linux-no-systemd-message"></a>
If you have a Linux device and it doesn't have [systemd](https://en.wikipedia.org/wiki/Systemd), the installer won't set up the software as a system service, and you won't see the success message for setting up the nucleus as a system service\.

### \(Optional\) Run the Greengrass software \(Linux\)<a name="run-the-software"></a>

If you installed the software as a system service, the installer runs the software for you\. Otherwise, you must run the software\. To see if the installer set up the software as a system service, look for the following line in the installer output\.

```
Successfully set up Nucleus as a system service
```

If you don't see this message, do the following to run the software:

1. Run the following command to run the software\.

   ```
   sudo /greengrass/v2/alts/current/distro/bin/loader
   ```

   The software prints the following message if it launches successfully\.

   ```
   Launched Nucleus successfully.
   ```

1. You must leave the current command shell open to keep the AWS IoT Greengrass Core software running\. If you use SSH to connect to the core device, run the following command on your development computer to open a second SSH session that you can use to run additional commands on the core device\. Replace *username* with the name of the user to sign in, and replace *pi\-ip\-address* with the IP address of the device\.

   ```
   ssh username@pi-ip-address
   ```

For more information about how to interact with the Greengrass system service, see [Configure the Greengrass nucleus as a system service](configure-greengrass-core-v2.md#configure-system-service)\.

### Verify the Greengrass CLI installation on the device<a name="verify-local-development-tools"></a>

The Greengrass CLI can take up to a minute to deploy\. Run the following command to check the status of the deployment\. Replace *MyGreengrassCore* with the name of your core device\.

```
aws greengrassv2 list-effective-deployments --core-device-thing-name MyGreengrassCore
```

The `coreDeviceExecutionStatus` indicates the status of the deployment to the core device\. When the status is `SUCCEEDED`, run the following command to verify that the Greengrass CLI is installed and runs\. Replace */greengrass/v2* with the path to the root folder\.

------
#### [ Linux or Unix ]

```
/greengrass/v2/bin/greengrass-cli help
```

------
#### [ Windows Command Prompt \(CMD\) ]

```
C:\greengrass\v2\bin\greengrass-cli help
```

------
#### [ PowerShell ]

```
C:\greengrass\v2\bin\greengrass-cli help
```

------

The command outputs help information for the Greengrass CLI\. If the `greengrass-cli` isn't found, the deployment might have failed to install the Greengrass CLI\. For more information, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\.

You can also run the following command to manually deploy the AWS IoT Greengrass CLI to your device\.
+ Replace *region* with the AWS Region that you use\. Make sure that you use the same AWS Region that you used to configure the AWS CLI on your device\.
+ Replace *account\-id* with your AWS account ID\.
+ Replace *MyGreengrassCore* with the name of your core device\.

------
#### [ Linux, macOS, or Unix ]

```
aws greengrassv2 create-deployment \
  --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" \
  --components '{
    "aws.greengrass.Cli": {
      "componentVersion": "2.5.0"
    }
  }'
```

------
#### [ Windows Command Prompt \(CMD\) ]

```
aws greengrassv2 create-deployment ^
  --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" ^
  --components "{\"aws.greengrass.Cli\":{\"componentVersion\":\"2.5.0\"}}"
```

------
#### [ PowerShell ]

```
aws greengrassv2 create-deployment `
  --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" `
  --components '{\"aws.greengrass.Cli\":{\"componentVersion\":\"2.5.0\"}}'
```

------

**Tip**  
You can add `/greengrass/v2/bin` \(Linux\) or `C:\greengrass\v2\bin` \(Windows\) to your `PATH` environment variable to run `greengrass-cli` without its absolute path\.

The AWS IoT Greengrass Core software and local development tools run on your device\. Next, you can develop a Hello World AWS IoT Greengrass component on your device\.

## Step 4: Develop and test a component on your device<a name="create-first-component"></a>

A component is a software module that runs on AWS IoT Greengrass core devices\. Components enable you to create and manage complex applications as discrete building blocks that you can reuse from one Greengrass core device to another\. Every component is composed of a *recipe* and *artifacts*\.
+ <a name="component-recipe-definition"></a>**Recipes**

  Every component contains a recipe file, which defines its metadata\. The recipe also specifies the component's configuration parameters, component dependencies, lifecycle, and platform compatibility\. The component lifecycle defines the commands that install, run, and shut down the component\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.

  You can define recipes in [JSON](https://en.wikipedia.org/wiki/JSON) or [YAML](https://en.wikipedia.org/wiki/YAML) format\.
+ <a name="component-artifacts-definition"></a>**Artifacts**

  Components can have any number of artifacts, which are component binaries\. Artifacts can include scripts, compiled code, static resources, and any other files that a component consumes\. Components can also consume artifacts from component dependencies\.

With AWS IoT Greengrass, you can use the Greengrass CLI to develop and test components locally on a Greengrass core device without interaction with the AWS Cloud\. When you complete your local component, you can use the component recipe and artifacts to create that component in the AWS IoT Greengrass service in the AWS Cloud, and then deploy it to all of your Greengrass core devices\. For more information about components, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md)\.

In this section, you learn how to create and run a basic Hello World component locally on your core device\.

**To develop a Hello World component on your device**

1. <a name="create-component-recipes-artifacts-folder-step"></a>Create a folder for your components with subfolders for recipes and artifacts\. Run the following commands on your Greengrass core device to create these folders and change to the component folder\. Replace *\~/greengrassv2* or *%USERPROFILE%\\greengrassv2* with the path to the folder to use for local development\.

------
#### [ Linux or Unix ]

   ```
   mkdir -p ~/greengrassv2/{recipes,artifacts}
   cd ~/greengrassv2
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   mkdir %USERPROFILE%\greengrassv2\recipes, %USERPROFILE%\greengrassv2\artifacts
   cd %USERPROFILE%\greengrassv2
   ```

------
#### [ PowerShell ]

   ```
   mkdir ~/greengrassv2/recipes, ~/greengrassv2/artifacts
   cd ~/greengrassv2
   ```

------

1. <a name="create-component-recipe-file-step"></a>Use a text editor to create a recipe file that defines your component's metadata, parameters, dependencies, lifecycle, and platform capability\. Include the component version in the recipe file name so that you can identify which recipe reflects which component version\. You can choose YAML or JSON format for your recipe\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

------
#### [ JSON ]

   ```
   nano recipes/com.example.HelloWorld-1.0.0.json
   ```

------
#### [ YAML ]

   ```
   nano recipes/com.example.HelloWorld-1.0.0.yaml
   ```

------
**Note**  
<a name="semver-para"></a>AWS IoT Greengrass uses semantic versions for components\. Semantic versions follow a *major*\.*minor*\.*patch* number system\. For example, version `1.0.0` represents the first major release for a component\. For more information, see the [semantic version specification](https://semver.org/)\.

1. Paste the following recipe into the file\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.HelloWorld",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "My first AWS IoT Greengrass component.",
     "ComponentPublisher": "Amazon",
     "ComponentConfiguration": {
       "DefaultConfiguration": {
         "Message": "world"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Run": "python3 -u {artifacts:path}/hello_world.py \"{configuration:/Message}\""
         }
       },
       {
         "Platform": {
           "os": "windows"
         },
         "Lifecycle": {
           "Run": "py -3 -u {artifacts:path}/hello_world.py \"{configuration:/Message}\""
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
   ComponentName: com.example.HelloWorld
   ComponentVersion: '1.0.0'
   ComponentDescription: My first AWS IoT Greengrass component.
   ComponentPublisher: Amazon
   ComponentConfiguration:
     DefaultConfiguration:
       Message: world
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Run: |
           python3 -u {artifacts:path}/hello_world.py "{configuration:/Message}"
     - Platform:
         os: windows
       Lifecycle:
         Run: |
           py -3 -u {artifacts:path}/hello_world.py "{configuration:/Message}"
   ```

------

   This recipe's `ComponentConfiguration` section defines a parameter, `Message`, that defaults to `world`\. The `Manifests` section defines a *manifest*, which is a set of lifecycle instructions and artifacts for a platform\. You can define multiple manifests to specify different install instructions for various platforms, for example\. In the manifest, the `Lifecycle` section instructs the Greengrass core device to run the Hello World script with the `Message` parameter value as an argument\.

1. Run the following command to create a folder for the component artifacts\.

------
#### [ Linux or Unix ]

   ```
   mkdir -p artifacts/com.example.HelloWorld/1.0.0
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   mkdir artifacts\com.example.HelloWorld\1.0.0
   ```

------
#### [ PowerShell ]

   ```
   mkdir artifacts\com.example.HelloWorld\1.0.0
   ```

------
**Important**  <a name="local-artifact-folder-name-requirements"></a>
You must use the following format for the artifact folder path\. Include the component name and version that you specify in the recipe\.  

   ```
   artifacts/componentName/componentVersion/
   ```

1. Use a text editor to create a Python script artifact file for your Hello World component\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

   Copy and paste the following Python script into the file\.

   ```
   import sys
   
   message = "Hello, %s!" % sys.argv[1]
   
   # Print the message to stdout, which Greengrass saves in a log file.
   print(message)
   ```

1. Use the local AWS IoT Greengrass CLI to manage components on your Greengrass core device\.

   Run the following command to deploy the component to the AWS IoT Greengrass core\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with your AWS IoT Greengrass V2 root folder, and replace *\~/greengrassv2* or *%USERPROFILE%\\greengrassv2* with your component development folder\.

------
#### [ Linux or Unix ]

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create \
     --recipeDir ~/greengrassv2/recipes \
     --artifactDir ~/greengrassv2/artifacts \
     --merge "com.example.HelloWorld=1.0.0"
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment create ^
     --recipeDir %USERPROFILE%\greengrassv2\recipes ^
     --artifactDir %USERPROFILE%\greengrassv2\artifacts ^
     --merge "com.example.HelloWorld=1.0.0"
   ```

------
#### [ PowerShell ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment create `
     --recipeDir ~/greengrassv2/recipes `
     --artifactDir ~/greengrassv2/artifacts `
     --merge "com.example.HelloWorld=1.0.0"
   ```

------

   This command adds the component that uses the recipe in `recipes` and the Python script in `artifacts`\. The `--merge` option adds or updates the component and version that you specify\.

1. The AWS IoT Greengrass Core software saves stdout from component process to log files in the `logs` folder\. Run the following command to verify that the Hello World component runs and prints messages\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.HelloWorld.log
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   type C:\greengrass\v2\logs\com.example.HelloWorld.log
   ```

   <a name="windows-cmd-type-observe-logs"></a>The `type` command writes the file's contents to the terminal\. Run this command multiple times to observe changes in the file\.

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2\logs\com.example.HelloWorld.log -Tail 10 -Wait
   ```

------

   You should see messages similar to the following example\.

   ```
   Hello, world!
   ```
**Note**  
If the file doesn't exist, the local deployment may not be complete yet\. If the file doesn't exist within 15 seconds, the deployment likely failed\. This can occur if your recipe isn't valid, for example\. Run the following command to view the AWS IoT Greengrass core log file\. This file includes logs from the Greengrass core device's deployment service\.  

   ```
   sudo tail -f /greengrass/v2/logs/greengrass.log
   ```

   ```
   type C:\greengrass\v2\logs\greengrass.log
   ```
<a name="windows-cmd-type-observe-logs"></a>The `type` command writes the file's contents to the terminal\. Run this command multiple times to observe changes in the file\.

   ```
   gc C:\greengrass\v2\logs\greengrass.log -Tail 10 -Wait
   ```

1. Modify the local component to iterate and test your code\. Open `hello_world.py` in a text editor, and add the following code at line 4 to edit the message that the AWS IoT Greengrass core logs\.

   ```
   message += " Greetings from your first Greengrass component."
   ```

   The `hello_world.py` script should now have the following contents\.

   ```
   import sys
   
   message = "Hello, %s!" % sys.argv[1]
   message += " Greetings from your first Greengrass component."
   
   # Print the message to stdout, which Greengrass saves in a log file.
   print(message)
   ```

1. Run the following command to update the component with your changes\.

------
#### [ Linux or Unix ]

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create \
     --recipeDir ~/greengrassv2/recipes \
     --artifactDir ~/greengrassv2/artifacts \
     --merge "com.example.HelloWorld=1.0.0"
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment create ^
     --recipeDir %USERPROFILE%\greengrassv2\recipes ^
     --artifactDir %USERPROFILE%\greengrassv2\artifacts ^
     --merge "com.example.HelloWorld=1.0.0"
   ```

------
#### [ PowerShell ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment create `
     --recipeDir ~/greengrassv2/recipes `
     --artifactDir ~/greengrassv2/artifacts `
     --merge "com.example.HelloWorld=1.0.0"
   ```

------

   This command updates the `com.example.HelloWorld` component with the latest Hello World artifact\.

1. Run the following command to restart the component\. When you restart a component, the core device uses the latest changes\.

------
#### [ Linux or Unix ]

   ```
   sudo /greengrass/v2/bin/greengrass-cli component restart \
     --names "com.example.HelloWorld"
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   C:\greengrass\v2\bin\greengrass-cli component restart ^
     --names "com.example.HelloWorld"
   ```

------
#### [ PowerShell ]

   ```
   C:\greengrass\v2\bin\greengrass-cli component restart `
     --names "com.example.HelloWorld"
   ```

------

1. Check the log again to verify that the Hello World component prints the new message\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.HelloWorld.log
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   type C:\greengrass\v2\logs\com.example.HelloWorld.log
   ```

   <a name="windows-cmd-type-observe-logs"></a>The `type` command writes the file's contents to the terminal\. Run this command multiple times to observe changes in the file\.

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2\logs\com.example.HelloWorld.log -Tail 10 -Wait
   ```

------

   You should see messages similar to the following example\.

   ```
   Hello, world! Greetings from your first Greengrass component.
   ```

1. You can update the component's configuration parameters to test different configurations\. When you deploy a component, you can specify a *configuration update*, which defines how to modify the component's configuration on the core device\. You can specify which configuration values to reset to default values and the new configuration values to merge onto the core device\. For more information, see [Update component configurations](update-component-configurations.md)\.

   Do the following:

   1. Use a text editor to create a file called `hello-world-config-update.json` to contain the configuration update

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano hello-world-config-update.json
      ```

   1. Copy and paste the following JSON object into the file\. This JSON object defines a configuration update that merges the value `friend` to the `Message` parameter to update its value\. This configuration update doesn't specify any values to reset\. You don't need to reset the `Message` parameter because the merge update replaces the existing value\.

      ```
      {
        "com.example.HelloWorld": {
          "MERGE": {
            "Message": "friend"
          }
        }
      }
      ```

   1. Run the following command to deploy the configuration update to the Hello World component\.

------
#### [ Linux or Unix ]

      ```
      sudo /greengrass/v2/bin/greengrass-cli deployment create \
        --merge "com.example.HelloWorld=1.0.0" \
        --update-config hello-world-config-update.json
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      C:\greengrass\v2\bin\greengrass-cli deployment create ^
        --merge "com.example.HelloWorld=1.0.0" ^
        --update-config hello-world-config-update.json
      ```

------
#### [ PowerShell ]

      ```
      C:\greengrass\v2\bin\greengrass-cli deployment create `
        --merge "com.example.HelloWorld=1.0.0" `
        --update-config hello-world-config-update.json
      ```

------

   1. Check the log again to verify that the Hello World component outputs the new message\.

------
#### [ Linux or Unix ]

      ```
      sudo tail -f /greengrass/v2/logs/com.example.HelloWorld.log
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      type C:\greengrass\v2\logs\com.example.HelloWorld.log
      ```

      <a name="windows-cmd-type-observe-logs"></a>The `type` command writes the file's contents to the terminal\. Run this command multiple times to observe changes in the file\.

------
#### [ PowerShell ]

      ```
      gc C:\greengrass\v2\logs\com.example.HelloWorld.log -Tail 10 -Wait
      ```

------

      You should see messages similar to the following example\.

      ```
      Hello, friend! Greetings from your first Greengrass component.
      ```

1. After you finish testing your component, remove it from your core device\. Run the following command\.

------
#### [ Linux or Unix ]

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create --remove="com.example.HelloWorld"
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment create --remove="com.example.HelloWorld"
   ```

------
#### [ PowerShell ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment create --remove="com.example.HelloWorld"
   ```

------
**Important**  
This step is required for you to deploy the component back to the core device after you upload it to AWS IoT Greengrass\. Otherwise, the deployment fails with a version compatibility error because the local deployment specifies a different version of the component\.

   Run the following command and verify that the `com.example.HelloWorld` component doesn't appear in the list of components on your device\.

------
#### [ Linux or Unix ]

   ```
   sudo /greengrass/v2/bin/greengrass-cli component list
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   C:\greengrass\v2\bin\greengrass-cli component list
   ```

------
#### [ PowerShell ]

   ```
   C:\greengrass\v2\bin\greengrass-cli component list
   ```

------

Your Hello World component is complete, and you can now upload it to the AWS IoT Greengrass cloud service\. Then, you can deploy the component to Greengrass core devices\.

## Step 5: Create your component in the AWS IoT Greengrass service<a name="upload-first-component"></a>

When you finish developing a component on your core device, you can upload it to the AWS IoT Greengrass service in the AWS Cloud\. You can also directly create the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. AWS IoT Greengrass provides a component management service that hosts your components so that you can deploy them to individual devices or fleets of devices\. To upload a component to the AWS IoT Greengrass service, you complete the following steps:
+ Upload component artifacts to an S3 bucket\.
+ Add each artifact's Amazon Simple Storage Service \(Amazon S3\) URI to the component recipe\.
+ Create a component in AWS IoT Greengrass from the component recipe\.

In this section, you complete these steps on your Greengrass core device to upload your Hello World component to the AWS IoT Greengrass service\.

### Create your component in AWS IoT Greengrass \(console\)<a name="upload-first-component-console"></a>

1. Use an S3 bucket in your AWS account to host AWS IoT Greengrass component artifacts\. When you deploy the component to a core device, the device downloads the component's artifacts from the bucket\.

   You can use an existing S3 bucket, or you can create a new bucket\. 

   1. In the [Amazon S3 console](https://console.aws.amazon.com/s3), under **Buckets**, choose **Create bucket**\.

   1. For **Bucket name**, enter a unique bucket name\. For example, you can use **greengrass\-component\-artifacts\-*123456789012*\-*region***\. Replace *123456789012* with your AWS account ID and *region* with the AWS Region that you use for this tutorial\.

   1. For **AWS region**, select the AWS Region that you use for this tutorial\.

   1. Choose **Create bucket**\.

   1. Under **Buckets**, choose the bucket that you created, upload the `hello_world.py` script to the `artifacts/com.example.HelloWorld/1.0.0` folder in the bucket\. For information about uploading objects to S3 buckets, see [Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html) in the *Amazon Simple Storage Service User Guide*\.

   1. Copy the S3 URI of the `hello_world.py` object in the S3 bucket\. This URI should look similar to the following example\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the S3 bucket\.

      ```
      s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
      ```

1. Allow the core device to access component artifacts in the S3 bucket\.

   Each core device has a [core device IAM role](device-service-role.md) that allows it to interact with AWS IoT and send logs to the AWS Cloud\. This device role doesn't allow access to S3 buckets by default, so you must create and attach a policy that allows the core device to retrieve component artifacts from the S3 bucket\.

   If your device's role already allows access to the S3 bucket, you can skip this step\. Otherwise, create an IAM policy that allows access and attach it to the role, as follows:

   1. In the [IAM console](https://console.aws.amazon.com/iam) navigation menu, choose **Policies**, and then choose **Create policy**\.

   1. On the **JSON** tab, replace the placeholder content with the following policy\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the bucket to use\.

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

   1. Choose **Next: Tags**, and then choose **Next: Review**\.

   1. For **Name**, enter **MyGreengrassV2ComponentArtifactPolicy**\.

   1. Choose **Create policy**\.

   1. In the [IAM console](https://console.aws.amazon.com/iam) navigation menu, choose **Role**, and then choose the *GreengrassV2TokenExchangeRole* role that you specified when you ran the AWS IoT Greengrass Core software\.

   1. Under **Permissions**, choose **Attach policies**\.

   1. On the **Attach Permissions** page, select the check box next to the `MyGreengrassV2ComponentArtifactPolicy` policy that you created, and then choose **Attach policy**\.

1. Use the component recipe to create a component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

   1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**, and then choose **Create component**\.

   1. Under **Component information**, choose **Enter recipe as JSON**\. The placeholder recipe should look similar to the following example\. 

      ```
      {
        "RecipeFormatVersion": "2020-01-25",
        "ComponentName": "com.example.HelloWorld",
        "ComponentVersion": "1.0.0",
        "ComponentDescription": "My first Greengrass component.",
        "ComponentPublisher": "Amazon",
        "ComponentConfiguration": {
          "DefaultConfiguration": {
            "Message": "world"
          }
        },
        "Manifests": [
          {
            "Lifecycle": {
              "Run": "python3 -u {artifacts:path}/hello_world.py \"{configuration:/Message}\""
            },
            "Artifacts": [
              {
                "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py"
              }
            ]
          }
        ]
      }
      ```

   1. Replace the placeholder URI with S3 URI of your `hello_world.py` object\. 

   1. Choose **Create component**\. 

   1. On the **com\.example\.HelloWorld** component page, verify that the **Status** of the component is **Deployable**\.

### Create your component in AWS IoT Greengrass \(AWS CLI\)<a name="upload-first-component-cli"></a>

**To upload your Hello World component**

1. Use an S3 bucket in your AWS account to host AWS IoT Greengrass component artifacts\. When you deploy the component to a core device, the device downloads the component's artifacts from the bucket\.

   You can use an existing S3 bucket, or run the following command to create a bucket\. This command creates a bucket with your AWS account ID and AWS Region to form a unique bucket name\. Replace *123456789012* with your AWS account ID and *region* with the AWS Region that you use for this tutorial\.

   ```
   aws s3 mb s3://greengrass-component-artifacts-123456789012-region
   ```

   The command outputs the following information if the request succeeds\.

   ```
   make_bucket: greengrass-component-artifacts-123456789012-region
   ```

1. Allow the core device to access component artifacts in the S3 bucket\. 

   Each core device has a [core device IAM role](device-service-role.md) that allows it to interact with AWS IoT and send logs to the AWS Cloud\. This device role doesn't allow access to S3 buckets by default, so you must create and attach a policy that allows the core device to retrieve component artifacts from the S3 bucket\.

   If your device's role already allows access to the S3 bucket, you can skip this step\. Otherwise, create an IAM policy that allows access and attach it to the role, as follows:

   1. Create a file called `component-artifact-policy.json` and copy the following JSON into the file\. This policy allows access to all files in the S3 bucket that you created in the previous step\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the bucket to use\.

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

------
#### [ Linux or Unix ]

      ```
      aws iam create-policy \
        --policy-name MyGreengrassV2ComponentArtifactPolicy \
        --policy-document file://component-artifact-policy.json
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      aws iam create-policy ^
        --policy-name MyGreengrassV2ComponentArtifactPolicy ^
        --policy-document file://component-artifact-policy.json
      ```

------
#### [ PowerShell ]

      ```
      aws iam create-policy `
        --policy-name MyGreengrassV2ComponentArtifactPolicy `
        --policy-document file://component-artifact-policy.json
      ```

------

      Copy the policy Amazon Resource Name \(ARN\) from the policy metadata in the output\. You use this ARN to attach this policy to the core device role in the next step\.

   1. Run the following command to attach the policy to the core device role\. Replace *GreengrassV2TokenExchangeRole* with the name of the role that you specified when you ran the AWS IoT Greengrass Core software\. Replace the policy ARN with the ARN from the previous step\.

------
#### [ Linux or Unix ]

      ```
      aws iam attach-role-policy \
        --role-name GreengrassV2TokenExchangeRole \
        --policy-arn arn:aws:iam::123456789012:policy/MyGreengrassV2ComponentArtifactPolicy
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      aws iam attach-role-policy ^
        --role-name GreengrassV2TokenExchangeRole ^
        --policy-arn arn:aws:iam::123456789012:policy/MyGreengrassV2ComponentArtifactPolicy
      ```

------
#### [ PowerShell ]

      ```
      aws iam attach-role-policy `
        --role-name GreengrassV2TokenExchangeRole `
        --policy-arn arn:aws:iam::123456789012:policy/MyGreengrassV2ComponentArtifactPolicy
      ```

------

      If the command has no output, it succeeded\. Your core device can now access artifacts that you upload to this S3 bucket\.

1. Upload the Hello World Python script artifact to the S3 bucket\. 

   Run the following command to upload the script to the same path in the bucket where the script exists on your AWS IoT Greengrass core\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the S3 bucket\.

------
#### [ Linux or Unix ]

   ```
   aws s3 cp \
     artifacts/com.example.HelloWorld/1.0.0/hello_world.py \
     s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws s3 cp ^
     artifacts/com.example.HelloWorld/1.0.0/hello_world.py ^
     s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

------
#### [ PowerShell ]

   ```
   aws s3 cp `
     artifacts/com.example.HelloWorld/1.0.0/hello_world.py `
     s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

------

   The command outputs a line that starts with `upload:` if the request succeeds\.

1. Add the artifact's Amazon S3 URI to the component recipe\. 

   The Amazon S3 URI is composed of the bucket name and the path to the artifact object in the bucket\. Your script artifact's Amazon S3 URI is the URI that you upload the artifact to in the previous step\. This URI should look similar to the following example\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the S3 bucket\.

   ```
   s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

   To add the artifact to the recipe, add a list of `Artifacts` that contains a structure with the Amazon S3 URI\.

------
#### [ JSON ]

   ```
   "Artifacts": [
     {
       "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py"
     }
   ]
   ```

   Open the recipe file in a text editor\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano recipes/com.example.HelloWorld-1.0.0.json
   ```

   Add the artifact to the recipe\. Your recipe file should look similar to the following example\.

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.HelloWorld",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "My first Greengrass component.",
     "ComponentPublisher": "Amazon",
     "ComponentConfiguration": {
       "DefaultConfiguration": {
         "Message": "world"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Run": "python3 -u {artifacts:path}/hello_world.py \"{configuration:/Message}\""
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py"
           }
         ]
       },
       {
         "Platform": {
           "os": "windows"
         },
         "Lifecycle": {
           "Run": "py -3 -u {artifacts:path}/hello_world.py \"{configuration:/Message}\""
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py"
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   Artifacts:
     - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

   Open the recipe file in a text editor\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano recipes/com.example.HelloWorld-1.0.0.yaml
   ```

   Add the artifact to the recipe\. Your recipe file should look similar to the following example\.

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.HelloWorld
   ComponentVersion: '1.0.0'
   ComponentDescription: My first AWS IoT Greengrass component.
   ComponentPublisher: Amazon
   ComponentConfiguration:
     DefaultConfiguration:
       Message: world
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Run: |
           python3 -u {artifacts:path}/hello_world.py "{configuration:/Message}"
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
     - Platform:
         os: windows
       Lifecycle:
         Run: |
           py -3 -u {artifacts:path}/hello_world.py "{configuration:/Message}"
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/hello_world.py
   ```

------

1. Create a component resource in AWS IoT Greengrass from the recipe\. Run the following command to create the component from the recipe, which you provide as a binary file\.

------
#### [ JSON ]

   ```
   aws greengrassv2 create-component-version --inline-recipe fileb://recipes/com.example.HelloWorld-1.0.0.json
   ```

------
#### [ YAML ]

   ```
   aws greengrassv2 create-component-version --inline-recipe fileb://recipes/com.example.HelloWorld-1.0.0.yaml
   ```

------

   The response looks similar to the following example if the request succeeds\.

   ```
   {
     "arn": "arn:aws:greengrass:region:123456789012:components:com.example.HelloWorld:versions:1.0.0",
     "componentName": "com.example.HelloWorld",
     "componentVersion": "1.0.0",
     "creationTimestamp": "Mon Nov 30 09:04:05 UTC 2020",
     "status": {
       "componentState": "REQUESTED",
       "message": "NONE",
       "errors": {}
     }
   }
   ```

   Copy the `arn` from the output to check the state of the component in the next step\.
**Note**  
You can also see your Hello World component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) on the **Components** page\.

1. Verify that the component creates and is ready to be deployed\. When you create a component, its state is `REQUESTED`\. Then, AWS IoT Greengrass validates that the component is deployable\. You can run the following command to query the component status and verify that your component is deployable\. Replace the `arn` with the ARN from the previous step\.

   ```
   aws greengrassv2 describe-component --arn "arn:aws:greengrass:region:123456789012:components:com.example.HelloWorld:versions:1.0.0"
   ```

   If the component validates, the response indicates that the component state is `DEPLOYABLE`\.

   ```
   {
     "arn": "arn:aws:greengrass:region:123456789012:components:com.example.HelloWorld:versions:1.0.0",
     "componentName": "com.example.HelloWorld",
     "componentVersion": "1.0.0",
     "creationTimestamp": "2020-11-30T18:04:05.823Z",
     "publisher": "Amazon",
     "description": "My first Greengrass component.",
     "status": {
       "componentState": "DEPLOYABLE",
       "message": "NONE",
       "errors": {}
     },
     "platforms": [
       {
         "os": "linux",
         "architecture": "all"
       }
     ]
   }
   ```

Your Hello World component is now available in AWS IoT Greengrass\. You can deploy it back to this Greengrass core device or to other core devices\.

## Step 6: Deploy your component<a name="deploy-first-component"></a>

With AWS IoT Greengrass, you can deploy components to individual devices or groups of devices\. When you deploy a component, AWS IoT Greengrass installs and runs that component's software on each target device\. You specify which components to deploy and the configuration update to deploy for each component\. You can also control how the deployment rolls out to the devices that the deployment targets\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

In this section, you deploy your Hello World component back to your Greengrass core device\.

### Deploy your component \(console\)<a name="deploy-first-component-console"></a>

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

1. On the **Components** page, on the **My components** tab, choose **com\.example\.HelloWorld**\.

1. On the **com\.example\.HelloWorld** page, choose **Deploy**\.

1. From **Add to deployment**, choose **Create new deployment**, then choose **Next**\. 

1. On the **Specify target** page, do the following:

   1. In the **Name** box, enter **Deployment for MyGreengrassCore**\.

   1. For **Deployment target**, choose **Core device**, and the name of the AWS IoT thing for your core device\. The default value in this tutorial is *MyGreengrassCore*\.

   1. Choose **Next**\.

1. On the **Select components** page, under **My components**, verify that the **com\.example\.HelloWorld** component is selected, and choose **Next**\.

1. On the **Configure components** page, choose **com\.example\.HelloWorld**, and do the following: 

   1. Choose **Configure component**\. 

   1. Under **Configuration update**, in **Configuration to merge**, enter the following configuration\.

      ```
      {
        "Message": "universe"
      }
      ```

      This configuration update sets the Hello World `Message` parameter to `universe` for the device in this deployment\.

   1. Choose **Confirm**\.

   1. Choose **Next**\.

1. On the **Configure advanced settings** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, choose **Deploy**

### Deploy your component \(AWS CLI\)<a name="deploy-first-component-cli"></a>

**To deploy your Hello World component**

1. On your development computer, create a file called `hello-world-deployment.json` and copy the following JSON into the file\. This file defines the components and configurations to deploy\.

   ```
   {
     "components": {
       "com.example.HelloWorld": {
         "componentVersion": "1.0.0",
         "configurationUpdate": {
           "merge": "{\"Message\":\"universe\"}"
         }
       }
     }
   }
   ```

   This configuration file specifies to deploy version `1.0.0` of the Hello World component that you developed and published in the previous procedure\. The `configurationUpdate` specifies to merge the component configuration in a JSON\-encoded string\. This configuration update sets the Hello World `Message` parameter to `universe` for the device in this deployment\.

1. Run the following command to deploy the component to your Greengrass core device\. You can deploy to things, which are individual devices, or thing groups, which are groups of devices\. Replace *MyGreengrassCore* with the name of the AWS IoT thing for your core device\.

------
#### [ Linux or Unix ]

   ```
   aws greengrassv2 create-deployment \
     --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" \
     --cli-input-json file://hello-world-deployment.json
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws greengrassv2 create-deployment ^
     --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" ^
     --cli-input-json file://hello-world-deployment.json
   ```

------
#### [ PowerShell ]

   ```
   aws greengrassv2 create-deployment `
     --target-arn "arn:aws:iot:region:account-id:thing/MyGreengrassCore" `
     --cli-input-json file://hello-world-deployment.json
   ```

------

   The command outputs a response similar to the following example\.

   ```
   {
     "deploymentId": "deb69c37-314a-4369-a6a1-3dff9fce73a9",
     "iotJobId": "b5d92151-6348-4941-8603-bdbfb3e02b75",
     "iotJobArn": "arn:aws:iot:region:account-id:job/b5d92151-6348-4941-8603-bdbfb3e02b75"
   }
   ```

1. Verify that the deployment completes successfully\. The deployment can take several minutes to complete\. Check the Hello World log to verify the change\. Run the following command on your Greengrass core device\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.HelloWorld.log
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   type C:\greengrass\v2\logs\com.example.HelloWorld.log
   ```

   <a name="windows-cmd-type-observe-logs"></a>The `type` command writes the file's contents to the terminal\. Run this command multiple times to observe changes in the file\.

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2\logs\com.example.HelloWorld.log -Tail 10 -Wait
   ```

------

   You should see messages similar to the following example\.

   ```
   Hello, universe! Greetings from your first Greengrass component.
   ```
**Note**  
If the log messages don't change, the deployment failed or didn't reach the core device\. This can occur if your core device isn't connected to the internet or doesn't have permissions to retrieve artifacts from your S3 bucket\. Run the following command on your core device to view the AWS IoT Greengrass Core software log file\. This file includes logs from the Greengrass core device's deployment service\.  

   ```
   sudo tail -f /greengrass/v2/logs/greengrass.log
   ```

   ```
   type C:\greengrass\v2\logs\greengrass.log
   ```
<a name="windows-cmd-type-observe-logs"></a>The `type` command writes the file's contents to the terminal\. Run this command multiple times to observe changes in the file\.

   ```
   gc C:\greengrass\v2\logs\greengrass.log -Tail 10 -Wait
   ```
For more information, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\.

## Next steps<a name="getting-started-next-steps"></a>

You've completed this tutorial\. The AWS IoT Greengrass Core software and your Hello World component run on your device\. Also, your Hello World component is available in the AWS IoT Greengrass cloud service to deploy to other devices\. For more information about the topics that this tutorial explores, see the following:
+ [Create local AWS IoT Greengrass components](create-components.md)
+ [Upload components to deploy to your core devicesUpload components to deploy](upload-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)