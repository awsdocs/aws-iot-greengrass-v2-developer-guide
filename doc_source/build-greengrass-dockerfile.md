# Build the AWS IoT Greengrass container image from a Dockerfile<a name="build-greengrass-dockerfile"></a>

AWS provides a Dockerfile that you can download and use to run AWS IoT Greengrass Core software in a Docker container\. Dockerfiles contain source code for building AWS IoT Greengrass container images\. 

Before you build an AWS IoT Greengrass container image, you must configure your Dockerfile to select the version of AWS IoT Greengrass Core software that you want to install\. You can also configure environment variables to choose how to provision resources during installation, and customize other installation options\. This section describes how to configure and build an AWS IoT Greengrass Docker image from a Dockerfile\. 



## Download the Dockerfile package<a name="download-dockerfile-package"></a>

You can download the AWS IoT Greengrass Dockerfile package from GitHub:

[https://github.com/aws-greengrass/aws-greengrass-docker/releases](https://github.com/aws-greengrass/aws-greengrass-docker/releases)

After you download the package, extract the contents to the `download-directory/aws-greengrass-docker-nucleus-version` folder on your computer\.

## Specify the AWS IoT Greengrass Core software version<a name="set-dockerfile-build-argument"></a>

Use the following build argument in the Dockerfile to specify the version of the AWS IoT Greengrass Core software that you want to use in the AWS IoT Greengrass Docker image\. By default, the Dockerfile uses the latest version of the AWS IoT Greengrass Core software\.

`GREENGRASS_RELEASE_VERSION`  
The version of the AWS IoT Greengrass Core software\. By default, the Dockerfile downloads the latest available version of the Greengrass nucleus\. Set the value to the version of the nucleus that you want to download\.

## Set environment variables<a name="set-dockerfile-environment-variables"></a>

Environment variables enable you to customize how AWS IoT Greengrass Core software is installed in the Docker container\. You can set environment variables for your AWS IoT Greengrass Docker image in various ways\. 
+ To use the same environment variables to create multiple images, set environment variables directly in the Dockerfile\.
+ If you use `docker run` to start your container, pass environment variables as arguments in the command, or set environment variables in an environment variables file and then pass the file as an argument\. For more information about setting environment variables in Docker, see the [Docker documentation](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file)\.
+ If you use `docker-compose up` to start your container, set environment variables in an environment variables file and then pass the file as an argument\. For more information about setting environment variables in Compose, see the [Docker documentation](https://docs.docker.com/compose/environment-variables/)\.

You can configure the following environment variables for the AWS IoT Greengrass Docker image\.

**Note**  
Don't modify the `TINI_KILL_PROCESS_GROUP` variable in the Dockerfile\. This variable allows forwarding `SIGTERM` to all PIDs in the PID group so that AWS IoT Greengrass Core software can shut down correctly when the Docker container is stopped\.

`GGC_ROOT_PATH`  
\(Optional\) The path to the folder within the container to use as the root for AWS IoT Greengrass Core software\.  
Default: `/greengrass/v2`

`PROVISION`  
\(Optional\) Determines whether the AWS IoT Greengrass Core provisions AWS resources\.   
+ If you specify `true`, AWS IoT Greengrass Core software registers the container image as an AWS IoT thing and provisions the AWS resources that the Greengrass core device requires\. The AWS IoT Greengrass Core software provisions an AWS IoT thing, \(optional\) an AWS IoT thing group, an IAM role, and an AWS IoT role alias\. For more information, see [Run AWS IoT Greengrass in a Docker container with automatic resource provisioning](run-greengrass-docker-automatic-provisioning.md)\.
+ If you specify `false`, then you must create a configuration file to provide to the AWS IoT Greengrass Core installer that specifies to use the AWS resources and certificates that you manually created\. For more information, see [Run AWS IoT Greengrass in a Docker container with manual resource provisioning](run-greengrass-docker-manual-provisioning.md)\.
Default: `false`

`AWS_REGION`  
\(Optional\) The AWS Region that the AWS IoT Greengrass Core software uses to retrieve or create required AWS resources\.   
Default: `us-east-1`\.

`THING_NAME`  
\(Optional\) The name of the AWS IoT thing that you register as this core device\. If the thing with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it\.   
You must specify `PROVISION=true` to apply this argument\.  
Default: `GreengrassV2IotThing_` plus a random UUID\.

`THING_GROUP_NAME`  
\(Optional\) The name of the AWS IoT thing group where you add this core device's AWS IoT If a deployment targets this thing group, this and other core devices in that group receive that deployment when it connects to AWS IoT Greengrass\. If the thing group with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it\.  
You must specify `PROVISION=true` to apply this argument\.

`TES_ROLE_NAME`  
\(Optional\) The name of the IAM role to use to acquire AWS credentials that let the Greengrass core device interact with AWS services\. If the role with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it with the `GreengrassV2TokenExchangeRoleAccess` policy\. This role doesn't have access to your S3 buckets where you host component artifacts\. So, you must add permissions to your artifacts' S3 buckets and objects when you create a component\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.  
Default: `GreengrassV2TokenExchangeRole`

`TES_ROLE_ALIAS_NAME`  
\(Optional\) The name of the AWS IoT role alias that points to the IAM role that provides AWS credentials for the Greengrass core device\. If the role alias with this name doesn't exist in your AWS account, the AWS IoT Greengrass Core software creates it and points it to the IAM role that you specify\.  
Default: `GreengrassV2TokenExchangeRoleAlias`

`COMPONENT_DEFAULT_USER`  
\(Optional\) The name or ID of the system user and group that the AWS IoT Greengrass Core software uses to run components\. Specify the user and group, separated by a colon\. The group is optional\. For example, you can specify **ggc\_user:ggc\_group** or **ggc\_user**\.  
+ If you run as root, this defaults to the user and group that the configuration file defines\. If the configuration file doesn't define a user and group, this defaults to `ggc_user:ggc_group`\. If `ggc_user` or `ggc_group` don't exist, the software creates them\.
+ If you run as a non\-root user, the AWS IoT Greengrass Core software uses that user to run components\.
+ If you don't specify a group, the AWS IoT Greengrass Core software uses the primary group of the system user\.
For more information, see [Configure the user and group that run components](configure-greengrass-core-v2.md#configure-component-user)\.

`DEPLOY_DEV_TOOLS`  
\(Optional\) Defines whether to download and deploy the Greengrass CLI component in the container image\. You can use the Greengrass CLI to develop and debug components locally\.  
Default: `false`  
Use local development tools in development environments only\. Don't specify this option on production devices\. 

`INIT_CONFIG`  
\(Optional\) The path to the configuration file to use to install the AWS IoT Greengrass Core software\. You can use this option to set up new Greengrass core devices with a specific nucleus configuration, or to specify manually provisioned resources, for example\. You must mount your configuration file to the path that you specify in this argument\. 

`TRUSTED_PLUGIN`  
\(Optional\) The path to a JAR file to load as a trusted plugin\. Use this option to provide provisioning plugin JAR files, such as to install with [fleet provisioning](fleet-provisioning.md) or [custom provisioning](custom-provisioning.md)\. 

## Specify the dependencies to install<a name="dockerfile-run-instruction"></a>

The RUN instruction in the AWS IoT Greengrass Dockerfile prepares up the container environment to run the AWS IoT Greengrass Core software installer\. You can customize the dependencies that are installed before the AWS IoT Greengrass Core software installer runs in the Docker container\. 

## Build the AWS IoT Greengrass image<a name="build-greengrass-docker-image"></a>

Use the AWS IoT Greengrass Dockerfile to build an AWS IoT Greengrass container image\. You can use the Docker CLI or the Docker Compose CLI to build the image and start the container\. You can also use the Docker CLI to build the image and then use Docker Compose to start your container from that image\.

------
#### [ Docker ]

1. On the host machine, run the following command to switch to the directory that contains the configured Dockerfile\.

   ```
   cd download-directory/aws-greengrass-docker-nucleus-version
   ```

1. Run the following command to build the AWS IoT Greengrass container image from the Dockerfile\.

   ```
   sudo docker build -t "platform/aws-iot-greengrass:nucleus-version" ./
   ```

------
#### [ Docker Compose ]

1. On the host machine, run the following command to switch to the directory that contains the Dockerfile and the Compose file\.

   ```
   cd download-directory/aws-greengrass-docker-nucleus-version
   ```

1. Run the following command to use the Compose file to build the AWS IoT Greengrass container image\.

   ```
   docker-compose -f docker-compose.yml build
   ```

------

You have successfully created the AWS IoT Greengrass container image\. The Docker image has the AWS IoT Greengrass Core software installed\. You can now run the AWS IoT Greengrass Core software in a Docker container\.