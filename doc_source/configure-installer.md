# Installer arguments<a name="configure-installer"></a>

The AWS IoT Greengrass Core software includes an installer that sets up the software and provisions the required AWS resources for the Greengrass core device to run\. The installer includes the following arguments that you can specify to configure the installation:

`-h`, `--help`  
\(Optional\) Show the installer's help information\.

`--version`  
\(Optional\) Show the version of the AWS IoT Greengrass Core software\.

`-Droot`  
\(Optional\) The path to the folder to use as the root for the AWS IoT Greengrass Core software\.  
This argument sets a JVM property, so you must specify it before `-jar` when you run the installer\. For example, specify `java -Droot="/greengrass/v2" -jar /path/to/Greengrass.jar`\.
Default:  
+ Linux: `~/.greengrass`
+ Windows: `%USERPROFILE%/.greengrass`

`-ar`, `--aws-region`  
The AWS Region that the AWS IoT Greengrass Core software uses to retrieve or create its required AWS resources\.

`-p`, `--provision`  
\(Optional\) You can register this device as an AWS IoT thing and provision the AWS resources that the core device requires\. If you specify `true`, the AWS IoT Greengrass Core software provisions an AWS IoT thing, \(optional\) an AWS IoT thing group, an IAM role, and an AWS IoT role alias\.  
Default: `false`

`-tn`, `--thing-name`  
\(Optional\) The name of the AWS IoT thing that you register as this core device\. If the thing with the name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it\.  
The thing name can't contain colon \(`:`\) characters\.
You must specify `--provision true` to apply this argument\.  
Default: `GreengrassV2IotThing_` plus a random UUID\.

`-tgn`, `--thing-group-name`  
\(Optional\) The name of the AWS IoT thing group where you add this core device's AWS IoT thing\. If a deployment targets this thing group, this core device receives that deployment when it connects to AWS IoT Greengrass\. If the thing group with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it\.  
The thing group name can't contain colon \(`:`\) characters\.
You must specify `--provision true` to apply this argument\.

`-tpn`, `--thing-policy-name`  
This feature is available for v2\.4\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.  
\(Optional\) The name of the AWS IoT policy to attach to this core device's AWS IoT thing certificate\. If the AWS IoT policy with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it\.  
The AWS IoT Greengrass Core software creates a permissive AWS IoT policy by default\. You can scope down this policy, or create a custom policy where you restrict permissions for your use case\. For more information, see [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy)\.  
You must specify `--provision true` to apply this argument\.  
Default: `GreengrassV2IoTThingPolicy`

`-trn`, `--tes-role-name`  
\(Optional\) The name of the IAM role to use to acquire AWS credentials that let the core device interact with AWS services\. If the role with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it with the `GreengrassV2TokenExchangeRoleAccess` policy\. This role doesn't have access to your S3 buckets where you host component artifacts\. So, you must add permissions to your artifacts' S3 buckets and objects when you create a component\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.  
You must specify `--provision true` to apply this argument\.  
Default: `GreengrassV2TokenExchangeRole`

`-tra`, `--tes-role-alias-name`  
\(Optional\) The name of the AWS IoT role alias that points to the IAM role that provides AWS credentials for this core device\. If the role alias with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it and points it to the IAM role that you specify\.  
You must specify `--provision true` to apply this argument\.  
Default: `GreengrassV2TokenExchangeRoleAlias`

`-ss`, `--setup-system-service`  
\(Optional\) You can set up the AWS IoT Greengrass Core software as a system service that runs when this device boots\. The system service name is `greengrass`\. For more information, see [Configure the Greengrass nucleus as a system service](configure-greengrass-core-v2.md#configure-system-service)\.  
On Linux operating systems, this argument requires that the systemd init system is available on the device\.  
Default: `false`

`-u`, `--component-default-user`  
The name or ID of the user that the AWS IoT Greengrass Core software uses to run components\. For example, you can specify **ggc\_user**\. This value is required when you run the installer on Windows operating systems\.  
On Linux operating systems, you can also optionally specify the group\. Specify the user and group separated by a colon\. For example, **ggc\_user:ggc\_group**\.  

The following additional considerations apply for Linux operating systems:
+ If you run as root, the default component user is the user that is defined in the configuration file\. If the configuration file doesn't define a user, this defaults to `ggc_user:ggc_group`\. If `ggc_user` or `ggc_group` don't exist, the software creates them\.
+ If you run as a non\-root user, the AWS IoT Greengrass Core software uses that user to run components\.
+ If you don't specify a group, the AWS IoT Greengrass Core software uses the primary group of the system user\.
For more information, see [Configure the user that runs components](configure-greengrass-core-v2.md#configure-component-user)\.

`-d`, `--deploy-dev-tools`  
\(Optional\) You can download and deploy the [Greengrass CLI](greengrass-cli-component.md) component to this core device\. You can use this tool to develop and debug components on this core device\.  
<a name="local-dev-tools-production-environment-warning"></a>We recommend that you use this component in only development environments, not production environments\. This component provides access to information and operations that you typically won't need in a production environment\. Follow the principle of least privilege by deploying this component to only core devices where you need it\.
You must specify `--provision true` to apply this argument\.  
Default: `false`

`-init`, `--init-config`  
\(Optional\) The path to the configuration file to use to install the AWS IoT Greengrass Core software\. You can use this option to set up new core devices with a specific nucleus configuration, for example\.   
The configuration file that you specify replaces the existing configuration file on the core device\. This erases the existing configuration, which includes the components and component configurations on the core device\.

`-tp`, `--trusted-plugin`  
\(Optional\) The path to a JAR file to load as a trusted plugin\. Use this option to provide provisioning plugin JAR files, such as to install with [fleet provisioning](fleet-provisioning.md) or [custom provisioning](custom-provisioning.md)\.

`-s`, `--start`  
\(Optional\) You can start the AWS IoT Greengrass Core software after it installs and, optionally, provisions resources\.  
Default: `true`