# Run AWS IoT Greengrass in a Docker container with manual resource provisioning<a name="run-greengrass-docker-manual-provisioning"></a>

This tutorial shows you how to install and run AWS IoT Greengrass Core software in Docker container with manually provisioned AWS resources\.

**Topics**
+ [Prerequisites](#docker-manual-provisioning-prereqs)
+ [Create an AWS IoT thing](#create-iot-thing)
+ [Download the Amazon root certification authority](#download-root-ca)
+ [Retrieve AWS IoT endpoints](#retrieve-iot-endpoints)
+ [Create a token exchange role](#create-token-exchange-role)
+ [Create a configuration file](#create-docker-install-configuration-file)
+ [Create an environment file](#create-env-file-manual-provisioning)
+ [Run the AWS IoT Greengrass Core software in a container](#run-greengrass-image-manual-provisioning)
+ [Next steps](#run-greengrass-docker-next-steps)

## Prerequisites<a name="docker-manual-provisioning-prereqs"></a>

To complete this tutorial, you need the following:
+ An AWS account\. If you don't have one, see [Set up an AWS account](setting-up.md#set-up-aws-account)\. 
+ An AWS IoT Greengrass Docker image\. This tutorial shows you how to pull the AWS IoT Greengrass Docker image from Docker Hub\. You can also [pull the AWS IoT Greengrass Docker image](run-greengrass-docker.md#pull-greengrass-docker-image) from Amazon Elastic Container Registry \(Amazon ECR\), or you can [build an image from the AWS IoT Greengrass Dockerfile](build-greengrass-dockerfile.md)\.
+ <a name="docker-host-reqs"></a>A Linux\-based operating system with an internet connection\.
+ <a name="docker-engine-reqs"></a>[Docker Engine](https://docs.docker.com/engine/install/) version 18\.09 or later\.
+ <a name="docker-compose-reqs"></a>\(Optional\) [Docker Compose](https://docs.docker.com/compose/install/) version 1\.22 or later\. Docker Compose is required only if you want to use the Docker Compose CLI to run your Docker images\.

## Create an AWS IoT thing<a name="create-iot-thing"></a>

AWS IoT *things* represent devices and logical entities that connect to AWS IoT\. Greengrass core devices are AWS IoT things\. When you register a device as an AWS IoT thing, that device can use a digital certificate to authenticate with AWS\.

In this section, you create an AWS IoT thing that represents your device\.

**To create an AWS IoT thing**

1. Create an AWS IoT thing for your device\. On your development computer, run the following command\.
   + Replace *MyGreengrassCore* with the thing name to use\. This name is also the name of your Greengrass core device\.
**Note**  <a name="install-argument-thing-name-constraint"></a>
The thing name can't contain colon \(`:`\) characters\.

   ```
   aws iot create-thing --thing-name MyGreengrassCore
   ```

   The response looks similar to the following example, if the request succeeds\.

   ```
   {
     "thingName": "MyGreengrassCore",
     "thingArn": "arn:aws:iot:us-west-2:123456789012:thing/MyGreengrassCore",
     "thingId": "8cb4b6cd-268e-495d-b5b9-1713d71dbf42"
   }
   ```

1. \(Optional\) Add the AWS IoT thing to a new or existing thing group\. You use thing groups to manage fleets of Greengrass core devices\. When you deploy software components to your devices, you can target individual devices or groups of devices\. You can add a device to a thing group with an active Greengrass deployment to deploy that thing group's software components to the device\. Do the following:

   1. \(Optional\) Create an AWS IoT thing group\.
      + Replace *MyGreengrassCoreGroup* with the name of the thing group to create\.
**Note**  <a name="install-argument-thing-group-name-constraint"></a>
The thing group name can't contain colon \(`:`\) characters\.

      ```
      aws iot create-thing-group --thing-group-name MyGreengrassCoreGroup
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "thingGroupName": "MyGreengrassCoreGroup",
        "thingGroupArn": "arn:aws:iot:us-west-2:123456789012:thinggroup/MyGreengrassCoreGroup",
        "thingGroupId": "4df721e1-ff9f-4f97-92dd-02db4e3f03aa"
      }
      ```

   1. Add the AWS IoT thing to a thing group\.
      + Replace *MyGreengrassCore* with the name of your AWS IoT thing\.
      + Replace *MyGreengrassCoreGroup* with the name of the thing group\.

      ```
      aws iot add-thing-to-thing-group --thing-name MyGreengrassCore --thing-group-name MyGreengrassCoreGroup
      ```

      The command doesn't have any output if the request succeeds\.

## Download the Amazon root certification authority<a name="download-root-ca"></a>

In the previous step, you downloaded the certificates for your AWS IoT thing\. In this step, you download the Amazon root certificate authority \(CA\) certificate\. AWS IoT certificates are associated with Amazon's root CA certificate by default\.

Run the following command to download the root CA certificate\.

```
curl -o ./greengrass-v2-certs/AmazonRootCA1.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
```

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

## Create a token exchange role<a name="create-token-exchange-role"></a>

<a name="installation-create-token-exchange-role-intro"></a>Greengrass core devices use an IAM service role, called the *token exchange role*, to authorize calls to AWS services\. The device uses the AWS IoT credentials provider to get temporary AWS credentials for this role, which allows the device to interact with AWS IoT, send logs to Amazon CloudWatch Logs, and download custom component artifacts from Amazon S3\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

<a name="installation-create-token-exchange-role-alias-intro"></a>You use an AWS IoT *role alias* to configure the token exchange role for Greengrass core devices\. Role aliases enable you to change the token exchange role for a device but keep the device configuration the same\. For more information, see [Authorizing direct calls to AWS services](https://docs.aws.amazon.com/iot/latest/developerguide/authorizing-direct-aws.html) in the *AWS IoT Core Developer Guide*\.

In this section, you create a token exchange IAM role and an AWS IoT role alias that points to the role\. If you have already set up a Greengrass core device, you can use its token exchange role and role alias instead of creating new ones\. Then, you configure your device's AWS IoT thing to use that role and alias\.

**To create a token exchange IAM role**

1. <a name="create-token-exchange-role-create-iam-role"></a>Create an IAM role that your device can use as a token exchange role\. Do the following:

   1. Create a file that contains the trust policy document that the token exchange role requires\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano device-role-trust-policy.json
      ```

      Copy the following JSON into the file\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "credentials.iot.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
          }
        ]
      }
      ```

   1. Create the token exchange role with the trust policy document\.
      + Replace *GreengrassV2TokenExchangeRole* with the name of the IAM role to create\.

      ```
      aws iam create-role --role-name GreengrassV2TokenExchangeRole --assume-role-policy-document file://device-role-trust-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "Role": {
          "Path": "/",
          "RoleName": "GreengrassV2TokenExchangeRole",
          "RoleId": "AROAZ2YMUHYHK5OKM77FB",
          "Arn": "arn:aws:iam::123456789012:role/GreengrassV2TokenExchangeRole",
          "CreateDate": "2021-02-06T00:13:29+00:00",
          "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
              {
                "Effect": "Allow",
                "Principal": {
                  "Service": "credentials.iot.amazonaws.com"
                },
                "Action": "sts:AssumeRole"
              }
            ]
          }
        }
      ```

   1. Create a file that contains the access policy document that the token exchange role requires\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano device-role-access-policy.json
      ```

      Copy the following JSON into the file\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "iot:DescribeCertificate",
              "logs:CreateLogGroup",
              "logs:CreateLogStream",
              "logs:PutLogEvents",
              "logs:DescribeLogStreams",
              "s3:GetBucketLocation"
            ],
            "Resource": "*"
          }
        ]
      }
      ```
**Note**  
This access policy doesn't allow access to component artifacts in S3 buckets\. To deploy custom components that define artifacts in Amazon S3, you must add permissions to the role to allow your core device to retrieve component artifacts\. For more information, see [Allow access to S3 buckets for component artifacts](device-service-role.md#device-service-role-access-s3-bucket)\.  
If you don't yet have an S3 bucket for component artifacts, you can add these permissions later after you create a bucket\.

   1. Create the IAM policy from the policy document\.
      + Replace *GreengrassV2TokenExchangeRoleAccess* with the name of the IAM policy to create\.

      ```
      aws iam create-policy --policy-name GreengrassV2TokenExchangeRoleAccess --policy-document file://device-role-access-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "Policy": {
          "PolicyName": "GreengrassV2TokenExchangeRoleAccess",
          "PolicyId": "ANPAZ2YMUHYHACI7C5Z66",
          "Arn": "arn:aws:iam::123456789012:policy/GreengrassV2TokenExchangeRoleAccess",
          "Path": "/",
          "DefaultVersionId": "v1",
          "AttachmentCount": 0,
          "PermissionsBoundaryUsageCount": 0,
          "IsAttachable": true,
          "CreateDate": "2021-02-06T00:37:17+00:00",
          "UpdateDate": "2021-02-06T00:37:17+00:00"
        }
      }
      ```

   1. Attach the IAM policy to the token exchange role\.
      + Replace *GreengrassV2TokenExchangeRole* with the name of the IAM role\.
      + Replace the policy ARN with the ARN of the IAM policy that you created in the previous step\.

      ```
      aws iam attach-role-policy --role-name GreengrassV2TokenExchangeRole --policy-arn arn:aws:iam::123456789012:policy/GreengrassV2TokenExchangeRoleAccess
      ```

      The command doesn't have any output if the request succeeds\.

1. <a name="create-token-exchange-role-create-iot-role-alias"></a>Create an AWS IoT role alias that points to the token exchange role\.
   + Replace *GreengrassCoreTokenExchangeRoleAlias* with the name of the role alias to create\.
   + Replace the role ARN with the ARN of the IAM role that you created in the previous step\.

   ```
   aws iot create-role-alias --role-alias GreengrassCoreTokenExchangeRoleAlias --role-arn arn:aws:iam::123456789012:role/GreengrassV2TokenExchangeRole
   ```

   The response looks similar to the following example, if the request succeeds\.

   ```
   {
     "roleAlias": "GreengrassCoreTokenExchangeRoleAlias",
     "roleAliasArn": "arn:aws:iot:us-west-2:123456789012:rolealias/GreengrassCoreTokenExchangeRoleAlias"
   }
   ```
**Note**  
To create a role alias, you must have permission to pass the token exchange IAM role to AWS IoT\. If you receive an error message when you try to create a role alias, check that your AWS user has this permission\. For more information, see [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *AWS Identity and Access Management User Guide*\.

1. Create and attach an AWS IoT policy that allows your Greengrass core device to use the role alias to assume the token exchange role\. If you have set up a Greengrass core device before, you can attach its role alias AWS IoT policy instead of creating a new one\. Do the following:

   1. \(Optional\) Create a file that contains the AWS IoT policy document that the role alias requires\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

      ```
      nano greengrass-v2-iot-role-alias-policy.json
      ```

      Copy the following JSON into the file\.
      + Replace the resource ARN with the ARN of your role alias\.

      ```
      {
        "Version":"2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": "iot:AssumeRoleWithCertificate",
            "Resource": "arn:aws:iot:us-west-2:123456789012:rolealias/GreengrassCoreTokenExchangeRoleAlias"
          }
        ]
      }
      ```

   1. Create an AWS IoT policy from the policy document\.
      + Replace *GreengrassCoreTokenExchangeRoleAliasPolicy* with the name of the AWS IoT policy to create\.

      ```
      aws iot create-policy --policy-name GreengrassCoreTokenExchangeRoleAliasPolicy --policy-document file://greengrass-v2-iot-role-alias-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "policyName": "GreengrassCoreTokenExchangeRoleAliasPolicy",
        "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/GreengrassCoreTokenExchangeRoleAliasPolicy",
        "policyDocument": "{
          \"Version\":\"2012-10-17\",
          \"Statement\": [
            {
              \"Effect\": \"Allow\",
              \"Action\": \"iot:AssumeRoleWithCertificate\",
              \"Resource\": \"arn:aws:iot:us-west-2:123456789012:rolealias/GreengrassCoreTokenExchangeRoleAlias\"
            }
          ]
        }",
        "policyVersionId": "1"
      }
      ```

   1. Attach the AWS IoT policy to the AWS IoT thing's certificate\.
      + Replace *GreengrassCoreTokenExchangeRoleAliasPolicy* with the name of the role alias AWS IoT policy\.
      + Replace the target ARN with the ARN of the certificate for your AWS IoT thing\.

      ```
      aws iot attach-policy --policy-name GreengrassCoreTokenExchangeRoleAliasPolicy --target arn:aws:iot:us-west-2:123456789012:cert/aa0b7958770878eabe251d8a7ddd547f4889c524c9b574ab9fbf65f32248b1d4
      ```

      The command doesn't have any output if the request succeeds\.

## Create a configuration file<a name="create-docker-install-configuration-file"></a>

1. Create a folder where you place your configuration file\.

   ```
   mkdir ./greengrass-v2-config
   ```

1. Use a text editor to create a configuration file named `config.yaml` in the `./greengrass-v2-config` folder\.

   For example, you can run the following command to use GNU nano to create the `config.yaml`\. 

   ```
   nano ./greengrass-v2-config/config.yaml
   ```

1. Copy the following YAML content into the file\. This partial configuration file specifies system parameters and Greengrass nucleus parameters\.

   ```
   ---
   system:
     certificateFilePath: "/tmp/certs/device.pem.crt"
     privateKeyPath: "/tmp/certs/private.pem.key"
     rootCaPath: "/tmp/certs/AmazonRootCA1.pem"
     rootpath: "/greengrass/v2"
     thingName: "MyGreengrassCore"
   services:
     aws.greengrass.Nucleus:
       componentType: "NUCLEUS"
       version: "nucleus-version"
       configuration:
         awsRegion: "region"
         iotRoleAlias: "GreengrassCoreTokenExchangeRoleAlias"
         iotDataEndpoint: "device-data-prefix-ats.iot.region.amazonaws.com"
         iotCredEndpoint: "device-credentials-prefix.credentials.region.amazonaws.com"
   ```

   Then, replace the following values:
   + */tmp/certs*\. The directory in the Docker container to which you mount the downloaded certificates when you start the container\.
   + */greengrass/v2*\. The Greengrass root folder that you want to use for installation\. You use the `GGC_ROOT` environment variable to set this value\.
   + *MyGreengrassCore*\. The name of the AWS IoT thing\.
   + *nucleus\-version*\. The version of the AWS IoT Greengrass Core software to install\. This value must match the version of the Docker image or Dockerfile that you downloaded\. If you downloaded the Greengrass Docker image with the `latest` tag, use ****docker inspect *image\-id***** to see the image version\.
   + *region*\. The AWS Region where you created your AWS IoT resources\. You must also specify the same value for the `AWS_REGION` environment variable in your [environment file](#create-env-file-manual-provisioning)\.
   + *GreengrassCoreTokenExchangeRoleAlias*\. The token exchange role alias\.
   + *device\-data\-prefix*\. The prefix for your AWS IoT data endpoint\.
   + *device\-credentials\-prefix*\. The prefix for your AWS IoT credentials endpoint\.

## Create an environment file<a name="create-env-file-manual-provisioning"></a>

This tutorial uses an environment file to set the environment variables that will be passed to the AWS IoT Greengrass Core software installer inside the Docker container\. You can also use [the `-e` or `--env` argument](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file) in your `docker run` command to set environment variables in the Docker container or you can set the variables in [an `environment` block](https://docs.docker.com/compose/compose-file/compose-file-v3/#environment) in the `docker-compose.yml` file\.

1. Use a text editor to create an environment file named `.env`\.

   For example, on a Linux\-based system, you can run the following command to use GNU nano to create the `.env` in the current directory\.

   ```
   nano .env
   ```

1. Copy the following content into the file\.

   ```
   GGC_ROOT_PATH=/greengrass/v2
   AWS_REGION=region
   PROVISION=false
   COMPONENT_DEFAULT_USER=ggc_user:ggc_group
   INIT_CONFIG=/tmp/config/config.yaml
   ```

   Then, replace the following values\.
   + */greengrass/v2*\. The path to the root folder to use to install the AWS IoT Greengrass Core software\.
   + *region*\. The AWS Region where you created your AWS IoT resources\. You must specify the same value for the `awsRegion` configuration parameter in your [configuration file](#create-docker-install-configuration-file)\.
   + */tmp/config/*\. The directory to which you mount the configuration file when you start the Docker container\.
**Note**  <a name="docker-local-dev-tools-production-environment-warning"></a>
You can set the `DEPLOY_DEV_TOOLS` environment variable to `true` to deploy the [Greengrass CLI component](greengrass-cli-component.md), which enables you to develop custom components inside of the Docker container\. <a name="local-dev-tools-production-environment-warning"></a>We recommend that you use this component in only development environments, not production environments\. This component provides access to information and operations that you typically won't need in a production environment\. Follow the principle of least privilege by deploying this component to only core devices where you need it\.

## Run the AWS IoT Greengrass Core software in a container<a name="run-greengrass-image-manual-provisioning"></a>

This tutorial shows you how to pull the latest AWS IoT Greengrass Docker image from Docker Hub and start the Docker container\. You can use the Docker CLI or the Docker Compose CLI to run the AWS IoT Greengrass Core software image in a Docker container\. 

------
#### [ Docker ]

1. Run the following command to pull the latest AWS IoT Greengrass Docker image from Docker Hub\.

   ```
   docker pull amazon/aws-iot-greengrass:latest
   ```

1. Run the following command to start the Docker container\. This command runs the Greengrass Docker image that you downloaded from Docker Hub\. If you use a Docker image from a different source, replace *amazon/aws\-iot\-greengrass:latest* with the name of your Docker image\. 

   ```
   docker run --rm --init -it --name aws-iot-greengrass \
    -v path/to/greengrass-v2-config:/tmp/config/:ro \
    -v path/to/greengrass-v2-certs:/tmp/certs:ro \ 
    --env-file .env \
    -p 8883 \
    amazon/aws-iot-greengrass:latest
   ```

   This example command uses the following arguments for [docker run](https://docs.docker.com/engine/reference/commandline/run/):
   + <a name="docker-run-rm"></a>[https://docs.docker.com/engine/reference/run/#clean-up---rm](https://docs.docker.com/engine/reference/run/#clean-up---rm)\. Cleans up the container when it exits\.
   + <a name="docker-run-init"></a>[https://docs.docker.com/engine/reference/run/#specify-an-init-process](https://docs.docker.com/engine/reference/run/#specify-an-init-process)\. Uses an init process in the container\. 
**Note**  
The `--init` argument is required to shut down AWS IoT Greengrass Core software when you stop the Docker container\.
   + <a name="docker-run-it"></a>[https://docs.docker.com/engine/reference/run/#foreground](https://docs.docker.com/engine/reference/run/#foreground)\. \(Optional\) Runs the Docker container in the foreground as an interactive process\. You can replace this with the `-d` argument to run the Docker container in detached mode instead\. For more information, see [Detached vs foreground](https://docs.docker.com/engine/reference/run/#detached-vs-foreground) in the Docker documentation\.
   + <a name="docker-run-name"></a>[https://docs.docker.com/engine/reference/run/#name---name](https://docs.docker.com/engine/reference/run/#name---name)\. Runs a container named `aws-iot-greengrass` 
   + <a name="docker-run-v"></a>[https://docs.docker.com/storage/volumes/](https://docs.docker.com/storage/volumes/)\. Mounts a volume into the Docker container to make the configuration file and the certificate files available to AWS IoT Greengrass running inside the container\.
   + <a name="docker-run-env-file"></a>[https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file)\. \(Optional\) Specifies the environment file to set the environment variables that will be passed to the AWS IoT Greengrass Core software installer inside the Docker container\. This argument is required only if you created an [environment file](#create-env-file-manual-provisioning) to set environment variables\. If you didn't create an environment file, you can use `--env` arguments to set environment variables directly in your Docker run command\.
   + <a name="docker-run-p"></a>[https://docs.docker.com/engine/reference/commandline/run/#publish-or-expose-port--p---expose](https://docs.docker.com/engine/reference/commandline/run/#publish-or-expose-port--p---expose)\. \(Optional\) Publishes the 8883 container port to the host machine\. This argument is required if you want to connect and communicate over MQTT because AWS IoT Greengrass uses port 8883 for MQTT traffic\. To open other ports, use additional `-p` arguments\.
**Note**  <a name="docker-run-cap-drop"></a>
To run your Docker container with increased security, you can use the `--cap-drop` and `--cap-add` arguments to selectively enable Linux capabilities for your container\. For more information, see [Runtime privilege and Linux capabilities](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) in the Docker documentation\.

------
#### [ Docker Compose ]

1. Use a text editor to create a Docker Compose file named `docker-compose.yml`\.

   For example, on a Linux\-based system, you can run the following command to use GNU nano to create the `docker-compose.yml` in the current directory\.

   ```
   nano docker-compose.yml
   ```
**Note**  
You can also download and use the latest version of the AWS\-provided Compose file from [GitHub](https://github.com/aws-greengrass/aws-greengrass-docker/releases/)\.

1. Add the following content to the Compose file\. Your file should look similar to the following example\. This example specifies the Greengrass Docker image that you downloaded from Docker Hub\. If you use a Docker image from a different source, replace *amazon/aws\-iot\-greengrass:latest* with the name of your Docker image\. 

   ```
   version: '3.7'
    
   services:
     greengrass:
       init: true
       build:
         context: .
       container_name: aws-iot-greengrass
       image: amazon/aws-iot-greengrass:latest
       volumes:
         - path/to/greengrass-v2-config:/tmp/config/:ro
         - path/to/greengrass-v2-certs:/tmp/certs:ro 
       env_file: .env
       ports:
         - "8883:8883"
   ```<a name="docker-compose-optional-params"></a>

   The following parameters in this example Compose file are optional:
   + `ports`—Publishes the 8883 container ports to the host machine\. This parameter is required if you want to connect and communicate over MQTT because AWS IoT Greengrass uses port 8883 for MQTT traffic\. 
   + `env_file`—Specifies the environment file to set the environment variables that will be passed to the AWS IoT Greengrass Core software installer inside the Docker container\. This parameter is required only if you created an [environment file](#create-env-file-manual-provisioning) to set environment variables\. If you didn't create an environment file, you can use the [ environment](https://docs.docker.com/compose/compose-file/compose-file-v3/#environment) parameter to set the variables directly in your Compose file\.
**Note**  <a name="docker-compose-cap-drop"></a>
To run your Docker container with increased security, you can use `cap_drop` and `cap_add` in your Compose file to selectively enable Linux capabilities for your container\. For more information, see [Runtime privilege and Linux capabilities](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) in the Docker documentation\.

1. Run the following command to start the container\.

   ```
   docker-compose -f docker-compose.yml up
   ```

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

For information about creating a simple component, see [Step 4: Develop and test a component on your device](getting-started.md#create-first-component) in [Getting started with AWS IoT Greengrass V2](getting-started.md)

**Note**  <a name="run-greengrass-commands-in-docker-note"></a>
When you use `docker exec` to run commands inside the Docker container, those commands are not logged in the Docker logs\. To log your commands in the Docker logs, attach an interactive shell to the Docker container\. For more information, see [Attach an interactive shell to the Docker container](docker-troubleshooting.md#debugging-docker-attach-shell)\.

The AWS IoT Greengrass Core log file is called `greengrass.log` and is located in `/greengrass/v2/logs`\. Component log files are also located in the same directory\. To copy Greengrass logs to a temporary directory on the host, run the following command:

```
docker cp container-id:/greengrass/v2/logs /tmp/logs
```

If you want to persist logs after a container exits or has been removed, we recommend that you bind\-mount only the `/greengrass/v2/logs` directory to the temporary logs directory on the host instead of mounting the entire Greengrass directory\. For more information, see [Persist Greengrass logs outside of the Docker container](docker-troubleshooting.md#debugging-docker-persist-logs)\.

<a name="greengrass-docker-stop"></a>To stop a running AWS IoT Greengrass Docker container, run `docker stop` or `docker-compose -f docker-compose.yml stop`\. This action sends `SIGTERM` to the Greengrass process and shuts down all associated processes that were started in the container\. The Docker container is initialized with the `docker-init` executable as process PID 1, which helps in removing any leftover zombie processes\. For more information, see the [Docker documentation](https://docs.docker.com/engine/reference/run/#specify-an-init-process)\.

<a name="see-docker-troubleshooting"></a>For information about troubleshooting issues with running AWS IoT Greengrass in a Docker container, see [Troubleshooting AWS IoT Greengrass in a Docker container](docker-troubleshooting.md)\.