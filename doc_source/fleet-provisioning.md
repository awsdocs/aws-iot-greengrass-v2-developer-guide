# Install AWS IoT Greengrass Core software with AWS IoT fleet provisioning<a name="fleet-provisioning"></a>

This feature is available for v2\.4\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

With AWS IoT fleet provisioning, you can configure AWS IoT to generate and securely deliver X\.509 device certificates and private keys to your devices when they connect to AWS IoT for the first time\. AWS IoT provides client certificates that are signed by the Amazon Root certificate authority \(CA\)\. You can also configure AWS IoT to specify thing groups, thing types, and permissions for Greengrass core devices that you provision with fleet provisioning\. You define a *provisioning template* to define how AWS IoT provisions each device\. The provisioning template specifies the thing, policy, and certificate resources to create for a device when provisioning\. For more information, see [Provisioning templates](https://docs.aws.amazon.com/iot/latest/developerguide/provision-template.html) in the *AWS IoT Core Developer Guide*\.

AWS IoT Greengrass provides an AWS IoT fleet provisioning plugin that you can use to install the AWS IoT Greengrass Core software using AWS resources created by AWS IoT fleet provisioning\. The fleet provisioning plugin uses *provisioning by claim*\. Devices use a provisioning claim certificate and private key to obtain a unique X\.509 device certificate and private key that they can use for regular operations\. You can embed the claim certificate and private key in each device during manufacturing, so your customers can activate devices later when each device comes online\. You can use the same claim certificate and private key for multiple devices\. For more information, see [Provisioning by claim](https://docs.aws.amazon.com/iot/latest/developerguide/provision-wo-cert.html#claim-based) in the *AWS IoT Core Developer Guide*\.

To install the AWS IoT Greengrass Core software with AWS IoT fleet provisioning, you must set up resources in your AWS account that AWS IoT uses to provision Greengrass core devices\. These resources include a provisioning template, claim certificates, and a [token exchange IAM role](device-service-role.md)\. After you create these resources, you can reuse them to provision multiple core devices in a fleet\. For more information, see [Set up AWS IoT fleet provisioning for Greengrass core devices](fleet-provisioning-setup.md)\.

**Important**  <a name="install-greengrass-core-requirements-note"></a>
Before you download the AWS IoT Greengrass Core software, check that your core device meets the [requirements](setting-up.md#greengrass-v2-requirements) to install and run the AWS IoT Greengrass Core software v2\.0\.

**Topics**
+ [Prerequisites](#fleet-provisioning-prerequisites)
+ [Retrieve AWS IoT endpoints](#retrieve-iot-endpoints)
+ [Download certificates to the device](#download-claim-certificates)
+ [Download the AWS IoT Greengrass Core software](#download-greengrass-core-v2)
+ [Download the AWS IoT fleet provisioning plugin](#download-fleet-provisioning-plugin)
+ [Install the AWS IoT Greengrass Core software](#run-greengrass-core-v2-installer-fleet)
+ [Set up AWS IoT fleet provisioning for Greengrass core devices](fleet-provisioning-setup.md)
+ [Configure the AWS IoT fleet provisioning plugin](fleet-provisioning-configuration.md)

## Prerequisites<a name="fleet-provisioning-prerequisites"></a>

To install the AWS IoT Greengrass Core software with AWS IoT fleet provisioning, you must first [set up AWS IoT fleet provisioning for Greengrass core devices](fleet-provisioning-setup.md)\. After you complete these steps once, you can use fleet provisioning to install the AWS IoT Greengrass Core software on any number of devices\.

## Retrieve AWS IoT endpoints<a name="retrieve-iot-endpoints"></a>

Get the AWS IoT endpoints for your AWS account, and save them to use later\. Your device uses these endpoints to connect to AWS IoT\. Do the following:

1. Get the AWS IoT data endpoint for your AWS account\.

   ```
   aws iot describe-endpoint --endpoint-type iot:Data-ATS
   ```

   The response looks similar to the following example, if the request succeeds\.

   ```
   {
     "endpointAddress": "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
   }
   ```

1. Get the AWS IoT credentials endpoint for your AWS account\.

   ```
   aws iot describe-endpoint --endpoint-type iot:CredentialProvider
   ```

   The response looks similar to the following example, if the request succeeds\.

   ```
   {
     "endpointAddress": "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
   }
   ```

## Download certificates to the device<a name="download-claim-certificates"></a>

The device uses a claim certificate and private key to authenticate its request to provision AWS resources and acquire an X\.509 device certificate\. You can embed the claim certificate and private key into the device during manufacturing, or copy the certificate and key to the device during installation\. In this section, you copy the claim certificate and private key to the device\. You also download the Amazon Root certificate authority \(CA\) certificate to the device\.

**Important**  <a name="installation-fleet-provisioning-secure-claim-private-keys"></a>
Provisioning claim private keys should be secured at all times, including on Greengrass core devices\. We recommend that you use Amazon CloudWatch metrics and logs to monitor for indications of misuse, such as unauthorized use of the claim certificate to provision devices\. If you detect misuse, disable the provisioning claim certificate so that it can't be used for device provisioning\. For more information, see [Monitoring AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/monitoring_overview.html) in the *AWS IoT Core Developer Guide*\.

**To download claim certificates to the device**

1. Copy the claim certificate and private key to the device\.

   For example, you might run the `scp` command on your development computer to copy the claim certificate and private key from a folder named `claim-certs` on your development computer to the device\.
   + Replace *device\-ip\-address* with the IP address of your device\.

   ```
   scp -r claim-certs/ device-ip-address:~
   ```

1. <a name="installation-create-greengrass-root-folder"></a>Create the Greengrass root folder on the device\. You'll later install the AWS IoT Greengrass Core software to this folder\.
   + Replace */greengrass/v2* with the folder to use\.

   ```
   sudo mkdir -p /greengrass/v2
   ```

1. <a name="installation-set-greengrass-root-folder-permissions"></a>Set the permissions of the parent of the Greengrass root folder\.
   + Replace */greengrass* with the parent of the root folder\.

   ```
   sudo chmod 755 /greengrass
   ```

1. Move the claim certificates to the Greengrass root folder\.
   + Replace */greengrass/v2* with the Greengrass root folder\.

   ```
   sudo mv ~/claim-certs /greengrass/v2
   ```

1. <a name="installation-download-root-ca-certificate"></a>Download the Amazon root certificate authority \(CA\) certificate\. AWS IoT certificates are associated with Amazon's root CA certificate by default\.

   ```
   sudo curl -o /greengrass/v2/AmazonRootCA1.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
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

## Download the AWS IoT fleet provisioning plugin<a name="download-fleet-provisioning-plugin"></a>

You can download the latest version of the AWS IoT fleet provisioning plugin from the following location:
+ [https://d2s8p88vqu9w66\.cloudfront\.net/releases/aws\-greengrass\-FleetProvisioningByClaim/fleetprovisioningbyclaim\-latest\.jar](https://d2s8p88vqu9w66.cloudfront.net/releases/aws-greengrass-FleetProvisioningByClaim/fleetprovisioningbyclaim-latest.jar)

**To download the AWS IoT fleet provisioning plugin \(Linux\)**
+ On your device, download the AWS IoT fleet provisioning plugin to a file named `aws.greengrass.FleetProvisioningByClaim.jar`\. Replace *GreengrassCore* with the folder that you want to use\.

  ```
  curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/aws-greengrass-FleetProvisioningByClaim/fleetprovisioningbyclaim-latest.jar > GreengrassCore/aws.greengrass.FleetProvisioningByClaim.jar
  ```

  <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

## Install the AWS IoT Greengrass Core software<a name="run-greengrass-core-v2-installer-fleet"></a>

Run the installer with arguments that specify the following actions:
+ Install from a partial configuration file that specifies to use the fleet provisioning plugin to provision AWS resources\. The AWS IoT Greengrass Core software uses a configuration file that specifies the configuration of every Greengrass component on the device\. The installer creates a complete configuration file from the partial configuration file that you provide and the AWS resources that the fleet provisioning plugin creates\.
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

   Copy the following YAML content into the file\. This partial configuration file specifies parameters for the fleet provisioning plugin\. For more information about the options that you can specify, see [Configure the AWS IoT fleet provisioning plugin](fleet-provisioning-configuration.md)\.

   ```
   ---
   services:
     aws.greengrass.Nucleus:
       version: "2.4.0"
     aws.greengrass.FleetProvisioningByClaim:
       configuration:
         rootPath: /greengrass/v2
         awsRegion: "us-west-2"
         iotDataEndpoint: "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
         iotCredentialEndpoint: "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
         iotRoleAlias: "GreengrassCoreTokenExchangeRoleAlias"
         provisioningTemplate: "GreengrassFleetProvisioningTemplate"
         claimCertificatePath: "/greengrass/v2/claim-certs/claim.pem.crt"
         claimCertificatePrivateKeyPath: "/greengrass/v2/claim-certs/claim.private.pem.key"
         rootCaPath: "/greengrass/v2/AmazonRootCA1.pem"
         templateParameters:
           ThingName: "MyGreengrassCore"
           ThingGroupName: "MyGreengrassCoreGroup"
   ```

   Then, do the following:
   + Replace *2\.4\.0* with the version of the AWS IoT Greengrass Core software\.
   + Replace each instance of */greengrass/v2* with the Greengrass root folder\.
   + Replace *us\-west\-2* with the AWS Region where you created the provisioning template and other resources\.
   + Replace the `iotDataEndpoint` with your AWS IoT data endpoint\.
   + Replace the `iotCredentialEndpoint` with your AWS IoT credentials endpoint\.
   + Replace *GreengrassCoreTokenExchangeRoleAlias* with the name of the token exchange role alias\.
   + Replace *GreengrassFleetProvisioningTemplate* with the name of the fleet provisioning template\.
   + Replace the `claimCertificatePath` with the path to the claim certificate on the device\.
   + Replace the `claimCertificatePrivateKeyPath` with the path to the claim certificate private key on the device\.
   + Replace the template parameters \(`templateParameters`\) with the values to use to provision the device\. This example refers to the [example template](fleet-provisioning-setup.md#example-fleet-provisioning-template) that defines `ThingName` and `ThingGroupName` parameters\.
**Note**  
In this configuration file, you can customize other configuration options such as the ports and network proxy to use, as shown in the following example\. For more information, see [Greengrass nucleus configuration](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration)\.  

   ```
   ---
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
     aws.greengrass.FleetProvisioningByClaim:
       configuration:
         rootPath: /greengrass/v2
         awsRegion: "us-west-2"
         iotDataEndpoint: "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
         iotCredentialEndpoint: "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
         iotRoleAlias: "GreengrassCoreTokenExchangeRoleAlias"
         provisioningTemplate: "GreengrassFleetProvisioningTemplate"
         claimCertificatePath: "/greengrass/v2/claim-certs/claim.pem.crt"
         claimCertificatePrivateKeyPath: "/greengrass/v2/claim-certs/claim.private.pem.key"
         rootCaPath: "/greengrass/v2/AmazonRootCA1.pem"
         templateParameters:
           ThingName: "MyGreengrassCore"
           ThingGroupName: "MyGreengrassCoreGroup"
         mqttPort: 443
         proxyUrl: "http://my-proxy-server:1100"
         proxyUserName: "Mary_Major"
         proxyPassword: "pass@word1357"
   ```

1. Run the installer\. Specify `--trusted-plugin` to provide the fleet provisioning plugin, and specify `--init-config` to provide the configuration file\.
   + Replace */greengrass/v2* with the Greengrass root folder\.
   + Replace each instance of *GreengrassCore* with the folder where you unpacked the installer\.

   ```
   sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE \
     -jar ./GreengrassCore/lib/Greengrass.jar \
     --trusted-plugin ./GreengrassCore/aws.greengrass.FleetProvisioningByClaim.jar \
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