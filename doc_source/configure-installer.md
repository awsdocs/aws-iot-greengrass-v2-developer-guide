# Installer arguments<a name="configure-installer"></a>

The AWS IoT Greengrass Core software includes an installer that sets up the software and provisions the required AWS resources for the Greengrass core device to run\. The installer includes the following arguments that you can specify to configure the installation:

`-h`, `--help`  
\(Optional\) Show the installer's help information\.

`--version`  
\(Optional\) Show the version of the AWS IoT Greengrass Core software\.

`-Droot`  
\(Optional\) The path to the folder to use as the root for the AWS IoT Greengrass Core software\.  
This argument sets a JVM property, so you must specify it before `-jar` when you run the installer\. For example, specify `java -Droot="/greengrass/v2" -jar /path/to/Greengrass.jar`\.
Default: `~/.greengrass`

`-ar`, `--aws-region`  
The AWS Region that the AWS IoT Greengrass Core software uses to retrieve or create its required AWS resources\.

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
\(Optional\) You can download and deploy the [Greengrass CLI](greengrass-cli-component.md) component to this core device\. You can use this tool to develop and debug components on this core device\.  
The local development tools are intended for use only in development environments\. Don't specify this option on production devices\.
Default: `false`

`-init`, `--init-config`  
\(Optional\) The path to the configuration file to use to install the AWS IoT Greengrass Core software\. You can use this option to set up new core devices with a specific nucleus configuration, for example\.   
The configuration file that you specify replaces the existing configuration file on the core device\. This erases the existing configuration, which includes the components and component configurations on the core device\.

`-s`, `--start`  
\(Optional\) You can start the AWS IoT Greengrass Core software after it installs and, optionally, provisions resources\.  
Default: `true`