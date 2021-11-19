# Run AWS IoT Greengrass Core software in a Docker container<a name="run-greengrass-docker"></a>

AWS IoT Greengrass can be configured to run in a Docker container\. Docker is a platform that provides the tools for you to build, run, test, and deploy applications that are based on Linux containers\. When you run an AWS IoT Greengrass Docker image, you can choose whether to provide your AWS credentials to the Docker container and allow the AWS IoT Greengrass Core software installer to automatically provision the AWS resources that a Greengrass core device requires to operate\. If you don't want to provide AWS credentials, then you can manually provision AWS resources and run AWS IoT Greengrass Core software in the Docker container\.

**Topics**
+ [Supported platforms and requirements](#greengrass-docker-supported-platforms)
+ [Software downloads](#greengrass-docker-downloads)
+ [Choose how to provision AWS resources](#greengrass-docker-resource-provisioning)
+ [Build the AWS IoT Greengrass image from a Dockerfile](build-greengrass-dockerfile.md)
+ [Run AWS IoT Greengrass in Docker with automatic provisioning](run-greengrass-docker-automatic-provisioning.md)
+ [Run AWS IoT Greengrass in Docker with manual provisioning](run-greengrass-docker-manual-provisioning.md)
+ [Troubleshooting AWS IoT Greengrass in a Docker container](docker-troubleshooting.md)

## Supported platforms and requirements<a name="greengrass-docker-supported-platforms"></a>

Host computers must meet the following minimum requirements to install and run the AWS IoT Greengrass Core software in a Docker container:
+ <a name="docker-host-reqs"></a>A Linux\-based operating system with an internet connection\.
+ <a name="docker-engine-reqs"></a>[Docker Engine](https://docs.docker.com/engine/install/) version 18\.09 or later\.
+ <a name="docker-compose-reqs"></a>\(Optional\) [Docker Compose](https://docs.docker.com/compose/install/) version 1\.22 or later\. Docker Compose is required only if you want to use the Docker Compose CLI to run your Docker images\.

To run Lambda function components inside of the Docker container, you must configure the container to meet additional requirements\. For more information, see [Lambda function requirements](setting-up.md#greengrass-v2-lambda-requirements)\.

### Run components in process mode<a name="docker-container-mode-limitation"></a>

AWS IoT Greengrass doesn't support running Lambda functions or AWS\-provided components in an isolated runtime environment inside the AWS IoT Greengrass Docker container\. You must run these components in process mode without any isolation\.

When you configure a Lambda function component, set the isolation mode to **No container**\. For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\. 

When you deploy any of the following AWS\-provided components, update the configuration for each component to set the `containerMode` parameter to `NoContainer`\. For more information about configuration updates, see [Update component configurations](update-component-configurations.md)\.
+ [CloudWatch metrics](cloudwatch-metrics-component.md)
+ [Device Defender](device-defender-component.md)
+ [Kinesis Data Firehose](kinesis-firehose-component.md)
+ [Modbus\-RTU protocol adapter](modbus-rtu-protocol-adapter-component.md)
+ [Amazon SNS](sns-component.md)

## AWS IoT Greengrass Docker software downloads<a name="greengrass-docker-downloads"></a>

AWS IoT Greengrass provides the following Dockerfile and Docker images that make it easier for you to run AWS IoT Greengrass Core software in a Docker container\. 

**Dockerfile**  
AWS IoT Greengrass provides a Dockerfile to build a container image that has AWS IoT Greengrass Core software and dependencies installed on an Amazon Linux 2 \(x86\_64\) base image\. You can modify the base image in the Dockerfile to run AWS IoT Greengrass on a different platform architecture\.  
Download the Dockerfile package from [GitHub](https://github.com/aws-greengrass/aws-greengrass-docker/releases/)\.  
For information about building the AWS IoT Greengrass container image from the Dockerfile, see [Build the AWS IoT Greengrass container image from a Dockerfile](build-greengrass-dockerfile.md)\.

**Docker images**  
AWS IoT Greengrass provides a Docker image that has AWS IoT Greengrass Core software and dependencies installed on an Amazon Linux 2 \(x86\_64\) base image\. To run AWS IoT Greengrass in Docker on a different platform architecture, use the Dockerfile to build a container image for that platform\.   
Download a prebuilt image from [Docker Hub](https://hub.docker.com/r/amazon/aws-iot-greengrass) or [Amazon Elastic Container Registry \(Amazon ECR\)](https://gallery.ecr.aws/q3k3q7c1/aws-iot-greengrass-v2)\. <a name="pull-greengrass-docker-image"></a>

To pull the AWS IoT Greengrass Docker image from Docker Hub or Amazon ECR, run the following command\.

------
#### [ Docker Hub ]

```
docker pull amazon/aws-iot-greengrass:tag
```

------
#### [ Amazon ECR ]

```
docker pull public.ecr.aws/q3k3q7c1/aws-iot-greengrass-v2:tag
```

------

The version in your *tag* represents the version of the AWS IoT Greengrass Core software and dependencies installed on the Amazon Linux 2 base image\. To find tags for all available images, check the **Tags** page on Docker Hub or the **Image tags** page on Amazon ECR\.

## Choose how to provision AWS resources<a name="greengrass-docker-resource-provisioning"></a>

When you install the AWS IoT Greengrass Core software in a Docker container, you can choose whether to automatically provision the AWS resources that a Greengrass core device requires to operate, or to use resources that you manually provision\.
+ **Automatic resource provisioning**—The installer provisions the AWS IoT thing, AWS IoT thing group, IAM role, and AWS IoT role alias when you run the AWS IoT Greengrass container image for the first time\. The installer can also deploy the local development tools to the core device, so you can use the device to develop and test custom software components\. To automatically provision these resources, you must provide AWS credentials as environment variables to the Docker image\.

  To use automatic provisioning, you must set the Docker environment variable `PROVISION=true` and mount a credential file to provide your AWS credentials to the container\.
+ **Manual resource provisioning**—If you don't want to provide AWS credentials to the container, then you can manually provision the AWS resources before you run the AWS IoT Greengrass container image\. You must create a configuration file to provide information about these resources to the AWS IoT Greengrass Core software installer within the Docker container\.

  To use manual provisioning, you must set the Docker environment variable `PROVISION=false`\. Manual provisioning is the default option\.

For more information, see [Build the AWS IoT Greengrass container image from a Dockerfile](build-greengrass-dockerfile.md)\.