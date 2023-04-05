# Install AWS IoT Greengrass Core software with custom resource provisioning<a name="custom-provisioning"></a>

This feature is available for v2\.4\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

The AWS IoT Greengrass Core software installer provides a Java interface that you can implement in a custom plugin that provisions required AWS resources\. You can develop a provisioning plugin to use custom X\.509 client certificates or to run complex provisioning steps that other installation processes don't support\. For more information, see [Create your own client certificates](https://docs.aws.amazon.com/iot/latest/developerguide/device-certs-your-own.html) in the *AWS IoT Core Developer Guide*\.

To run a custom provisioning plugin when you install the AWS IoT Greengrass Core software, you create a JAR file that you provide to the installer\. The installer runs the plugin, and the plugin returns a provisioning configuration that defines the AWS resources for the Greengrass core device\. The installer uses this information to configure the AWS IoT Greengrass Core software on the device\. For more information, see [Develop custom provisioning plugins](develop-custom-provisioning-plugins.md)\.

**Important**  <a name="install-greengrass-core-requirements-note"></a>
Before you download the AWS IoT Greengrass Core software, check that your core device meets the [requirements](setting-up.md#greengrass-v2-requirements) to install and run the AWS IoT Greengrass Core software v2\.0\.

**Topics**
+ [Prerequisites](#custom-provisioning-prerequisites)
+ [Set up the device environment](#set-up-device-environment)
+ [Download the AWS IoT Greengrass Core software](#download-greengrass-core-v2)
+ [Install the AWS IoT Greengrass Core software](#run-greengrass-core-v2-installer-custom)
+ [Develop custom provisioning plugins](develop-custom-provisioning-plugins.md)

## Prerequisites<a name="custom-provisioning-prerequisites"></a>

To install the AWS IoT Greengrass Core software with custom provisioning, you must have the following:
+ A JAR file for a custom provisioning plugin that implements the `DeviceIdentityInterface`\. The custom provisioning plugin must return values for each system and nucleus configuration parameter\. Otherwise, you must provide those values in the configuration file during installation\. For more information, see [Develop custom provisioning plugins](develop-custom-provisioning-plugins.md)\.

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

   When the installation completes, run the following command to verify that Java runs on your Linux device\.

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

1. Check whether Java is available on the [PATH](https://en.wikipedia.org/wiki/PATH_(variable)) system variable, and add it if not\. The LocalSystem account runs the AWS IoT Greengrass Core software, so you must add Java to the PATH system variable instead of the PATH user variable for your user\. Do the following:

   1. Press the Windows key to open the start menu\.

   1. Type **environment variables** to search for the system options from the start menu\.

   1. In the start menu search results, choose **Edit the system environment variables** to open the **System properties** window\.

   1. Choose **Environment variables\.\.\.** to open the **Environment Variables** window\.

   1. Under **System variables**, select **Path**, and then choose **Edit**\. In the **Edit environment variable** window, you can view each path on a separate line\.

   1. Check if the path to the Java installation's `bin` folder is present\. The path might look similar to the following example\.

      ```
      C:\Program Files\Amazon Corretto\jdk11.0.13_8\bin
      ```

   1. If the Java installation's `bin` folder is missing from **Path**, choose **New** to add it, and then choose **OK**\.

1. <a name="set-up-windows-device-environment-open-cmd"></a>Open the Windows Command Prompt \(`cmd.exe`\) as an administrator\.

1. <a name="set-up-windows-device-environment-create"></a>Create the default user in the LocalSystem account on the Windows device\. Replace *password* with a secure password\.

   ```
   net user /add ggc_user password
   ```
**Tip**  <a name="windows-password-expiration-tip"></a>
Depending on your Windows configuration, the user's password might be set to expire at a date in the future\. To ensure your Greengrass applications continue to operate, track when the password expires, and update it before it expires\. You can also set the user's password to never expire\.  
To check when a user and its password expire, run the following command\.  

     ```
     net user ggc_user | findstr /C:expires
     ```
To set a user's password to never expire, run the following command\.  

     ```
     wmic UserAccount where "Name='ggc_user'" set PasswordExpires=False
     ```

1. <a name="set-up-windows-device-psexec"></a>Download and install the [PsExec utility](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) from Microsoft on the device\. 

1. <a name="set-up-windows-device-credentials"></a>Use the PsExec utility to store the user name and password for the default user in the Credential Manager instance for the LocalSystem account\. Replace *password* with the user's password that you set earlier\.

   ```
   psexec -s cmd /c cmdkey /generic:ggc_user /user:ggc_user /pass:password
   ```

   If the **PsExec License Agreement** opens, choose **Accept** to agree to the license and run the command\.
**Note**  
On Windows devices, the LocalSystem account runs the Greengrass nucleus, and you must use the PsExec utility to store the default user information in the LocalSystem account\. Using the Credential Manager application stores this information in the Windows account of the currently logged on user, instead of the LocalSystem account\.

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

**\(Optional\) To verify the Greengrass nucleus software signature**
**Note**  
This feature is available with Greengrass nucleus version 2\.9\.5 and later\.

1. Use the following command to verify your Greengrass nucleus artifact's signature:

------
#### [ Linux or Unix ]

   ```
   jarsigner -verify -certs -verbose greengrass-nucleus-latest.zip
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   The file name might look different depending on the JDK version you install\. Replace *`jdk17.0.6_10`* with the JDK version you installed\.

   ```
   "C:\Program Files\Amazon Corretto\jdk17.0.6_10\bin\jarsigner.exe" -verify -certs -verbose greengrass-nucleus-latest.zip
   ```

------
#### [ PowerShell ]

   The file name might look different depending on the JDK version you install\. Replace *`jdk17.0.6_10`* with the JDK version you installed\.

   ```
   'C:\Program Files\Amazon Corretto\jdk17.0.6_10\bin\jarsigner.exe' -verify -certs -verbose greengrass-nucleus-latest.zip
   ```

------

1. The `jarsigner` invocation yields output that indicates the results of the verification\.

   1. If the Greengrass nucleus zip file is signed, the output contains the following statement:

      ```
      jar verified.
      ```

   1. If the Greengrass nucleus zip file isn't signed, the output contains the following statement:

      ```
      jar is unsigned.
      ```

1. If you provided the Jarsigner `-certs` option along with `-verify` and `-verbose` options, the output also includes detailed signer certificate information\.

## Install the AWS IoT Greengrass Core software<a name="run-greengrass-core-v2-installer-custom"></a>

Run the installer with arguments that specify the following actions:
+ Install from a partial configuration file that specifies to use your custom provisioning plugin to provision AWS resources\. The AWS IoT Greengrass Core software uses a configuration file that specifies the configuration of every Greengrass component on the device\. The installer creates a complete configuration file from the partial configuration file that you provide and the AWS resources that the custom provisioning plugin creates\.
+ <a name="install-argument-component-default-user"></a>Specify to use the `ggc_user` system user to run software components on the core device\. On Linux devices, this command also specifies to use the `ggc_group` system group, and the installer creates the system user and group for you\.
+ <a name="install-argument-system-service"></a>Set up the AWS IoT Greengrass Core software as a system service that runs at boot\. On Linux devices, this requires the [Systemd](https://en.wikipedia.org/wiki/Systemd) init system\.
**Important**  <a name="windows-system-service-requirement-important-note"></a>
On Windows core devices, you must set up the AWS IoT Greengrass Core software as a system service\.

For more information about the arguments that you can specify, see [Installer arguments](configure-installer.md)\.

**Note**  
<a name="jvm-tuning-note"></a>If you are running AWS IoT Greengrass on a device with limited memory, you can control the amount of memory that AWS IoT Greengrass Core software uses\. To control memory allocation, you can set JVM heap size options in the `jvmOptions` configuration parameter in your nucleus component\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

**To install the AWS IoT Greengrass Core software \(Linux\)**

1. <a name="installer-check-greengrass-core-software-version"></a>Check the version of the AWS IoT Greengrass Core software\.
   + Replace *GreengrassInstaller* with the path to the folder that contains the software\.

   ```
   java -jar ./GreengrassInstaller/lib/Greengrass.jar --version
   ```

1. Use a text editor to create a configuration file named `config.yaml` to provide to the installer\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano GreengrassInstaller/config.yaml
   ```

   Copy the following YAML content into the file\.

   ```
   ---
   system:
     rootpath: "/greengrass/v2"
     # The following values are optional. Return them from the provisioning plugin or set them here.
     # certificateFilePath: ""
     # privateKeyPath: ""
     # rootCaPath: ""
     # thingName: ""
   services:
     aws.greengrass.Nucleus:
       version: "2.9.4"
       configuration:
         # The following values are optional. Return them from the provisioning plugin or set them here.
         # awsRegion: ""
         # iotRoleAlias: ""
         # iotDataEndpoint: ""
         # iotCredEndpoint: ""
     com.example.CustomProvisioning:
       configuration:
         # You can specify configuration parameters to provide to your plugin.
         # pluginParameter: ""
   ```

   Then, do the following:
   + Replace *2\.9\.4* with the version of the AWS IoT Greengrass Core software\.
   + Replace each instance of */greengrass/v2* with the Greengrass root folder\.
   + \(Optional\) Specify system and nucleus configuration values\. You must set these values if your provisioning plugin doesn't provide them\.
   + \(Optional\) Specify configuration parameters to provide to your provisioning plugin\.
**Note**  
In this configuration file, you can customize other configuration options, such as the ports and network proxy to use, as shown in the following example\. For more information, see [Greengrass nucleus configuration](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration)\.  

   ```
   ---
   system:
     rootpath: "/greengrass/v2"
     # The following values are optional. Return them from the provisioning plugin or set them here.
     # certificateFilePath: ""
     # privateKeyPath: ""
     # rootCaPath: ""
     # thingName: ""
   services:
     aws.greengrass.Nucleus:
       version: "2.9.4"
       configuration:
         mqtt:
           port: 443
         greengrassDataPlanePort: 443
         networkProxy:
           noProxyAddresses: "http://192.168.0.1,www.example.com"
           proxy:
             url: "http://my-proxy-server:1100"
             username: "Mary_Major"
             password: "pass@word1357"
         # The following values are optional. Return them from the provisioning plugin or set them here.
         # awsRegion: ""
         # iotRoleAlias: ""
         # iotDataEndpoint: ""
         # iotCredEndpoint: ""
     com.example.CustomProvisioning:
       configuration:
         # You can specify configuration parameters to provide to your plugin.
         # pluginParameter: ""
   ```

1. Run the installer\. Specify `--trusted-plugin` to provide your custom provisioning plugin, and specify `--init-config` to provide the configuration file\.
   + Replace */greengrass/v2* or *C:\\greengrass\\v2* with the Greengrass root folder\.
   + Replace each instance of *GreengrassInstaller* with the folder where you unpacked the installer\.
   + Replace the path to the custom provisioning plugin JAR file with the path to your plugin's JAR file\.

------
#### [ Linux or Unix ]

   ```
   sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE \
     -jar ./GreengrassInstaller/lib/Greengrass.jar \
     --trusted-plugin /path/to/com.example.CustomProvisioning.jar \
     --init-config ./GreengrassInstaller/config.yaml \
     --component-default-user ggc_user:ggc_group \
     --setup-system-service true
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   java -Droot="C:\greengrass\v2" "-Dlog.store=FILE" ^
     -jar ./GreengrassInstaller/lib/Greengrass.jar ^
     --trusted-plugin /path/to/com.example.CustomProvisioning.jar ^
     --init-config ./GreengrassInstaller/config.yaml ^
     --component-default-user ggc_user ^
     --setup-system-service true
   ```

------
#### [ PowerShell ]

   ```
   java -Droot="C:\greengrass\v2" "-Dlog.store=FILE" `
     -jar ./GreengrassInstaller/lib/Greengrass.jar `
     --trusted-plugin /path/to/com.example.CustomProvisioning.jar `
     --init-config ./GreengrassInstaller/config.yaml `
     --component-default-user ggc_user `
     --setup-system-service true
   ```

------
**Important**  <a name="windows-system-service-installer-argument-important-note"></a>
On Windows core devices, you must specify `--setup-system-service true` to set up the AWS IoT Greengrass Core software as a system service\.

   <a name="installer-setup-system-service-output-message"></a>If you specify `--setup-system-service true`, the installer prints `Successfully set up Nucleus as a system service` if it set up and ran the software as a system service\. Otherwise, the installer doesn't output any message if it installs the software successfully\.
**Note**  <a name="installer-deploy-dev-tools-without-provision"></a>
You can't use the `deploy-dev-tools` argument to deploy local development tools when you run the installer without the `--provision true` argument\. For information about deploying the Greengrass CLI directly on your device, see [Greengrass Command Line Interface](gg-cli.md)\.

1. <a name="installer-verify-installation"></a>Verify the installation by viewing the files in the root folder\.

------
#### [ Linux or Unix ]

   ```
   ls /greengrass/v2
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   dir C:\greengrass\v2
   ```

------
#### [ PowerShell ]

   ```
   ls C:\greengrass\v2
   ```

------

   If the installation succeeded, the root folder contains several folders, such as `config`, `packages`, and `logs`\.

<a name="install-greengrass-core-run-software"></a>If you installed the AWS IoT Greengrass Core software as a system service, the installer runs the software for you\. Otherwise, you must run the software manually\. For more information, see [Run the AWS IoT Greengrass Core software](run-greengrass-core-v2.md)\.

<a name="install-greengrass-core-next-steps-intro"></a>For more information about how to configure and use the software and AWS IoT Greengrass, see the following:<a name="install-greengrass-core-next-steps-links"></a>
+ [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)
+ [Develop AWS IoT Greengrass components](develop-greengrass-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
+ [Greengrass Command Line Interface](gg-cli.md)