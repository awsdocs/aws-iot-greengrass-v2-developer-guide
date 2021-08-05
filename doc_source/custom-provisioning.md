# Install AWS IoT Greengrass Core software with custom resource provisioning<a name="custom-provisioning"></a>

This feature is available for v2\.4\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

The AWS IoT Greengrass Core software installer provides a Java interface that you can implement in a custom plugin that provisions required AWS resources\. You can develop a provisioning plugin to use custom X\.509 client certificates or to run complex provisioning steps that other installation processes don't support\. For more information, see [Create your own client certificates](https://docs.aws.amazon.com/iot/latest/developerguide/device-certs-your-own.html) in the *AWS IoT Core Developer Guide*\.

To run a custom provisioning plugin when you install the AWS IoT Greengrass Core software, you create a JAR file that you provide to the installer\. The installer runs the plugin, and the plugin returns a provisioning configuration that defines the AWS resources for the Greengrass core device\. The installer uses this information to configure the AWS IoT Greengrass Core software on the device\. For more information, see [Develop custom provisioning plugins](develop-custom-provisioning-plugins.md)\.

**Important**  <a name="install-greengrass-core-requirements-note"></a>
Before you download the AWS IoT Greengrass Core software, check that your core device meets the [requirements](setting-up.md#greengrass-v2-requirements) to install and run the AWS IoT Greengrass Core software v2\.0\.

**Topics**
+ [Prerequisites](#custom-provisioning-prerequisites)
+ [Download the AWS IoT Greengrass Core software](#download-greengrass-core-v2)
+ [Install the AWS IoT Greengrass Core software](#run-greengrass-core-v2-installer-custom)
+ [Develop custom provisioning plugins](develop-custom-provisioning-plugins.md)

## Prerequisites<a name="custom-provisioning-prerequisites"></a>

To install the AWS IoT Greengrass Core software with custom provisioning, you must have the following:
+ A JAR file for a custom provisioning plugin that implements the `DeviceIdentityInterface`\. The custom provisioning plugin must return values for each system and nucleus configuration parameter\. Otherwise, you must provide those values in the configuration file during installation\. For more information, see [Develop custom provisioning plugins](develop-custom-provisioning-plugins.md)\.

## Download the AWS IoT Greengrass Core software<a name="download-greengrass-core-v2"></a>

You can download the latest version of the AWS IoT Greengrass Core software from the following location:
+ [https://d2s8p88vqu9w66\.cloudfront\.net/releases/greengrass\-nucleus\-latest\.zip](https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip)

**Note**  
You can download a specific version of the AWS IoT Greengrass Core software from the following location\. Replace *version* with the version to download\.  

```
https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-version.zip
```

**To download the AWS IoT Greengrass Core software \(Linux\)**

1. On your device, download the AWS IoT Greengrass Core software to a file named `greengrass-nucleus-latest.zip`\.

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

   <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

1. Unzip the AWS IoT Greengrass Core software to a folder on your device\. Replace *GreengrassCore* with the folder that you want to use\.

   ```
   unzip greengrass-nucleus-latest.zip -d GreengrassCore && rm greengrass-nucleus-latest.zip
   ```
**Important**  
If you install a version of the Greengrass nucleus earlier than v2\.4\.0, don't remove this folder after you install the AWS IoT Greengrass Core software\. The AWS IoT Greengrass Core software uses the files in this folder to run\.  
If you downloaded the latest version of the software, you install v2\.4\.0 or later, and you can remove this folder after you install the AWS IoT Greengrass Core software\.

1. \(Optional\) Run the following command to see the version of the AWS IoT Greengrass Core software\.

   ```
   java -jar ./GreengrassCore/lib/Greengrass.jar --version
   ```

## Install the AWS IoT Greengrass Core software<a name="run-greengrass-core-v2-installer-custom"></a>

Run the installer with arguments that specify the following actions:
+ Install from a partial configuration file that specifies to use your custom provisioning plugin to provision AWS resources\. The AWS IoT Greengrass Core software uses a configuration file that specifies the configuration of every Greengrass component on the device\. The installer creates a complete configuration file from the partial configuration file that you provide and the AWS resources that the custom provisioning plugin creates\.
+ <a name="install-argument-component-default-user"></a>Use the `ggc_user` system user and `ggc_group` system group to run software components on the core device\. The installer creates this default user and group if they don't exist\.
+ <a name="install-argument-setup-system-service"></a>Install the software as a system service that runs on boot, if your device has the [systemd](https://en.wikipedia.org/wiki/Systemd) init system\.

For more information about the arguments that you can specify, see [Installer arguments](configure-installer.md)\.

**Note**  
<a name="jvm-tuning-note"></a>If you are running AWS IoT Greengrass on a device with limited memory, you can control the amount of memory that AWS IoT Greengrass Core software uses\. To control memory allocation, you can set JVM heap size options in the `jvmOptions` configuration parameter in your nucleus component\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

**To install the AWS IoT Greengrass Core software \(Linux\)**

1. <a name="installer-check-greengrass-core-software-version"></a>Check the version of the AWS IoT Greengrass Core software\.
   + Replace *GreengrassCore* with the path to the folder that contains the software\.

   ```
   java -jar ./GreengrassCore/lib/Greengrass.jar --version
   ```

1. Use a text editor to create a configuration file named `config.yaml` to provide to the installer\.

   For example, on a Linux\-based system, you can run the following command to use GNU nano to create `config.yaml` in the *GreengrassCore* folder\.

   ```
   nano GreengrassCore/config.yaml
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
       version: "2.4.0"
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
   + Replace *2\.4\.0* with the version of the AWS IoT Greengrass Core software\.
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
       version: "2.4.0"
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
   + Replace */greengrass/v2* with the Greengrass root folder\.
   + Replace each instance of *GreengrassCore* with the folder where you unpacked the installer\.
   + Replace the path to the custom provisioning plugin JAR file with the path to your plugin's JAR file\.

   ```
   sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE \
     -jar ./GreengrassCore/lib/Greengrass.jar \
     --trusted-plugin /path/to/com.example.CustomProvisioning.jar \
     --init-config ./GreengrassCore/config.yaml \
     --component-default-user ggc_user:ggc_group \
     --setup-system-service true
   ```

   <a name="installer-setup-system-service-output-message"></a>If you specify `--setup-system-service true`, the installer prints `Successfully set up Nucleus as a system service` if it set up and ran the software as a system service\. Otherwise, the installer doesn't output any message if it installs the software successfully\.
**Note**  <a name="installer-deploy-dev-tools-without-provision"></a>
You can't use the `deploy-dev-tools` argument to deploy local development tools when you run the installer without the `--provision true` argument\. For information about deploying the Greengrass CLI directly on your device, see [Greengrass Command Line Interface](gg-cli.md)\.

1. <a name="installer-verify-installation"></a>Verify the installation by viewing the files in the root folder\.

   ```
   ls /greengrass/v2
   ```

   If the installation succeeded, the root folder contains several folders, such as `config`, `packages`, and `logs`\.

<a name="install-greengrass-core-run-software"></a>If you installed the AWS IoT Greengrass Core software as a system service, the installer runs the software for you\. Otherwise, you must run the software manually\. For more information, see [Run the AWS IoT Greengrass Core software](run-greengrass-core-v2.md)\.

<a name="install-greengrass-core-next-steps-intro"></a>For more information about how to configure and use the software and AWS IoT Greengrass, see the following:<a name="install-greengrass-core-next-steps-links"></a>
+ [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)
+ [Manage AWS IoT Greengrass components](manage-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
+ [Greengrass Command Line Interface](gg-cli.md)