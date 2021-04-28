# Run AWS IoT Greengrass in a Docker container with automatic resource provisioning<a name="run-greengrass-docker-automatic-provisioning"></a>

This tutorial shows you how to install and run AWS IoT Greengrass Core software in Docker container with automatically provisioned AWS resources and local development tools\. You can use this development environment to explore AWS IoT Greengrass features in a Docker container\.



## Prerequisites<a name="docker-automatic-provisioning-prereqs"></a>

To complete this tutorial, you need the following\.
+ A Linux\-based host computer with an internet connection\. 
+ An AWS account\. If you don't have one, see [Set up an AWS account](setting-up.md#set-up-aws-account)\. 
+ An AWS IAM user\. For information about the minimal IAM policy that the installer requires to automatically provision resources, see [Minimal IAM policy for installer to provision resources](provision-minimal-iam-policy.md)\.
+ An AWS IoT Greengrass Docker image\. You can pull an AWS IoT Greengrass Docker image from Docker Hub or Amazon Elastic Container Registry \(Amazon ECR\), or you can [build an image from the AWS IoT Greengrass Dockerfile](build-greengrass-dockerfile.md)\.
+ [Docker Engine](https://docs.docker.com/engine/install/) version 19\.03 or later installed on the host computer\.
+ \(Optional\) [Docker Compose](https://docs.docker.com/compose/install/) version 1\.25\.4 or later installed on the host computer\. Docker Compose is required only if you want to use the Docker Compose CLI to build and run your Docker images\.

## Configure your AWS credentials<a name="configure-aws-credentials-for-docker"></a>

In this step, you create a credential file on the host computer that contains your AWS security credentials\. When you run the AWS IoT Greengrass Docker image, you must mount the folder that contains this credential file to `/root/.aws/` in the Docker container\. The AWS IoT Greengrass installer uses these credentials to provision resources in your AWS account\. For information about the minimal IAM policy that the installer requires to automatically provision resources, see [Minimal IAM policy for installer to provision resources](provision-minimal-iam-policy.md)\.

1. Retrieve one of the following\.
   + Long\-term credentials for an IAM user\. For information about how to retrieve long\-term credentials, see [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the *IAM User Guide*\.
   + \(Recommended\) Temporary credentials for an IAM role\. For information about how to retrieve temporary credentials, see [Using temporary security credentials with the AWS CLI](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html#using-temp-creds-sdk-cli) in the *IAM User Guide*\.

1. Create a folder where you place your credential file\.

   ```
   mkdir greengrass-v2-credentials
   ```

1. Use a text editor to create a configuration file named `credentials` in the `greengrass-v2-credentials` folder\.

   For example, you can run the following command to use GNU nano to create the `credentials` file\. 

   ```
   nano greengrass-v2-credentials/credentials
   ```

1. Add your AWS credentials to the `credentials` file in the following format\.

   ```
   [default]
   aws_access_key_id = AKIAIOSFODNN7EXAMPLE
   aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   aws_session_token = AQoEXAMPLEH4aoAH0gNCAPy...truncated...zrkuWJOgQs8IZZaIv2BXIa2R4Olgk
   ```

**Note**  
Remove the credential file from the host computer after you start the AWS IoT Greengrass container\. If you don't remove the credential file, then your AWS credentials will remain mounted inside the container\. For more information, see [Run the AWS IoT Greengrass Core software in a container](#run-greengrass-image-automatic-provisioning)\.

## Create an environment file<a name="create-env-file-automatic-provisioning"></a>

This tutorial uses an environment file to set the environment variables that will be passed to the AWS IoT Greengrass Core software installer inside the Docker container\. You can also use [the `-e` or `--env` argument](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file) in your `docker run` command to set environment variables in the Docker container or you can set the variables in [an `environment` block](https://docs.docker.com/compose/compose-file/compose-file-v3/#environment) in the `docker-compose.yml` file\.

1. Use a text editor to create an environment file named `.env` in the folder that contains your Docker image\.

   For example, on a Linux\-based system, you can run the following command to use GNU nano to create the `.env` in the current directory\.

   ```
   nano .env
   ```

1. Copy the following content into the file\.

   ```
   GGC_ROOT_PATH=/greengrass/v2
   AWS_REGION=region
   PROVISION=true
   THING_NAME=MyGreengrassCore
   THING_GROUP_NAME=MyGreengrassCoreGroup
   TES_ROLE_NAME=GreengrassV2TokenExchangeRole
   TES_ROLE_ALIAS_NAME=GreengrassCoreTokenExchangeRoleAlias
   COMPONENT_DEFAULT_USER=ggc_user:ggc_group
   DEPLOY_DEV_TOOLS=true
   ```

   Then, replace the following values\.
   + */greengrass/v2*\. The Greengrass root folder that you want to use for installation\. You use the `GGC_ROOT` environment variable to set this value\.
   + *region*\. The AWS Region where you created the resources\.
   + *MyGreengrassCore*\. The name of the AWS IoT thing\. If the thing doesn't exist, the installer creates it\. The installer downloads the certificates to authenticate as the AWS IoT thing\. 
   + *MyGreengrassCore*\. The name of the AWS IoT thing group\. If the thing group doesn't exist, the installer creates it and adds the thing to it\. If the thing group exists and has an active deployment, the core device downloads and runs the software that the deployment specifies\.
   +  *GreengrassV2TokenExchangeRole*\. Replace with the name of the IAM token exchange role that allows the Greengrass core device to get temporary AWS credentials\. If the role doesn't exist, the installer creates it and creates and attaches a policy named *GreengrassV2TokenExchangeRole*Access\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.
   + *GreengrassCoreTokenExchangeRoleAlias*\. The token exchange role alias\. If the role alias doesn't exist, the installer creates it and points it to the IAM token exchange role that you specify\. For more information, see 
**Note**  
Setting the `DEPLOY_DEV_TOOLS` environment variable to `true` deploys local development tools that enable custom component development inside of the Docker container\. Don't use this environment variable for production devices\.

## Run the AWS IoT Greengrass Core software in a container<a name="run-greengrass-image-automatic-provisioning"></a>

You can use the Docker CLI or the Docker Compose CLI to run the AWS IoT Greengrass Core software image as a Docker container\.

------
#### [ Docker ]

1. Run the following command to start the Docker container\.

   ```
   docker run --rm --init -it --name aws-iot-greengrass \
    -v /path/to/credential/file/directory:/root/.aws/:ro \
    --env-file .env \
    x86_64/aws-iot-greengrass:nucleus-version
   ```

   This example command uses the following arguments for [docker run](https://docs.docker.com/engine/reference/commandline/run/):
   + [https://docs.docker.com/engine/reference/run/#clean-up---rm](https://docs.docker.com/engine/reference/run/#clean-up---rm)\. Cleans up the container when it exits\.
   + [https://docs.docker.com/engine/reference/run/#specify-an-init-process](https://docs.docker.com/engine/reference/run/#specify-an-init-process)\. Uses an init process in the container\. 
**Note**  
The `--init` argument is required to shut down AWS IoT Greengrass Core software when you stop the Docker container\.
   + [https://docs.docker.com/engine/reference/run/#foreground](https://docs.docker.com/engine/reference/run/#foreground)\. \(Optional\) Runs the Docker container in the foreground as an interactive process\. You can replace this with the `-d` argument to run the Docker container in detached mode instead\. For more information, see [Detached vs foreground](https://docs.docker.com/engine/reference/run/#detached-vs-foreground) in the Docker documentation\.
   + [https://docs.docker.com/engine/reference/run/#name---name](https://docs.docker.com/engine/reference/run/#name---name)\. Runs a container named `aws-iot-greengrass` 
   + [https://docs.docker.com/storage/volumes/](https://docs.docker.com/storage/volumes/)\. Mounts a volume into the Docker container to make the credential file available to AWS IoT Greengrass running inside the container\. For more information about volumes, see the [Docker documentation](https://docs.docker.com/storage/volumes/)\.
   + [https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file)\. Specifies the environment file to set the environment variables that will be passed to the AWS IoT Greengrass Core software installer inside the Docker container\.
**Note**  <a name="docker-run-cap-drop"></a>
To run your Docker container with increased security, you can use the `--cap-drop` and `--cap-add` arguments to selectively enable Linux capabilities for your container\. For more information, see [Runtime privilege and Linux capabilities](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) in the Docker documentation\.

1. Remove the credential file from `/path/to/credential/file/directory`\.

------
#### [ Docker Compose ]

1. In a text editor, open the Docker Compose file \(`docker-compose.yml`\) that is included in the Dockerfile tarball and add a `volumes` block to the file\. Your Compose file should look similar to the following example\. Replace *nucleus\-version* with the version of the Greengrass nucleus that is installed in your Docker image\.

   ```
   version: '3.7'
    
   services:
     greengrass:
       init: true
       build:
         context: .
         dockerfile: dockerfile-name
       container_name: aws-iot-greengrass
       image: x86_64/aws-iot-greengrass:nucleus-version
       volumes:
         - /path/to/credential/file/directory:/root/.aws/:ro
   ```
**Note**  <a name="docker-compose-cap-drop"></a>
To run your Docker container with increased security, you can use `cap_drop` and `cap_add` in your Compose file to selectively enable Linux capabilities for your container\. For more information, see [Runtime privilege and Linux capabilities](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) in the Docker documentation\.

1. Run the following command start the Docker container\.

   ```
   docker-compose --env-file .env up
   ```

1. Remove the credential file from `/path/to/credential/file/directory`\.

------

## Next steps<a name="run-greengrass-docker-next-steps"></a>

<a name="run-greengrass-docker-success"></a>AWS IoT Greengrass Core software is now running in a Docker container\. Run the following command to retrieve the container ID for the currently running container\.

```
docker ps
```

You can then run the following command to access the container and explore AWS IoT Greengrass Core software running inside the container\.

```
docker exec -it container-id /bin/bash
```

For information about creating a simple component, see [Create your first component](getting-started.md#create-first-component) in [Getting started with AWS IoT Greengrass V2](getting-started.md)

**Note**  <a name="run-greengrass-commands-in-docker-note"></a>
When you use `docker exec` to run commands inside the Docker container, those commands are not logged in the Docker logs\. To log your commands in the Docker logs, attach an interactive shell to the Docker container\. For more information, see [Attach an interactive shell to the Docker container](docker-troubleshooting.md#debugging-docker-attach-shell)\.

The AWS IoT Greengrass Core log file is called `greengrass.log` and is located in `/greengrass/v2/logs`\. Component log files are also located in the same directory\. To view Greengrass logs on the host, run the following command:

```
docker cp container-id:/greengrass/v2/logs
```

If you want to persist logs after a container exits or has been removed, we recommend that you bind\-mount only the `/greengrass/v2/logs` directory to a temporary logs directory on the host instead of mounting the entire Greengrass directory\. For more information, see [Persist Greengrass logs outside of the Docker container](docker-troubleshooting.md#debugging-docker-persist-logs)\.

<a name="greengrass-docker-stop"></a>To stop a running AWS IoT Greengrass Docker container, run `docker stop` or `docker-compose -f docker-compose.yml stop`\. This action sends `SIGTERM` to the Greengrass process and shuts down all associated processes that were started in the container\. The Docker container is initialized with `/dev/init` process as PID 1, which helps in removing any leftover zombie processes\.

<a name="see-docker-troubleshooting"></a>For information about troubleshooting issues with running AWS IoT Greengrass in a Docker container, see [Troubleshooting AWS IoT Greengrass in a Docker container](docker-troubleshooting.md)\.