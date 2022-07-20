# Run a Docker container<a name="run-docker-container"></a>

You can configure AWS IoT Greengrass components to run a [Docker](https://www.docker.com/) container from images stored in the following locations:
+ Public and private image repositories in Amazon Elastic Container Registry \(Amazon ECR\)
+ Public Docker Hub repository
+ Public Docker Trusted Registry
+ S3 bucket

In your custom component, include the Docker image URI as an artifact to retrieve the image and run it on the core device\. For Amazon ECR and Docker Hub images, you can use the [Docker application manager](docker-application-manager-component.md) component to download the images and manage credentials for private Amazon ECR repositories\.

**Topics**
+ [Requirements](#run-docker-container-requirements)
+ [Run a Docker container from a public image in Amazon ECR or Docker Hub](#run-docker-container-public-ecr-dockerhub)
+ [Run a Docker container from a private image in Amazon ECR](#run-docker-container-private-ecr)
+ [Run a Docker container from an image in Amazon S3](#run-docker-container-s3)
+ [Use interprocess communication in Docker container components](#docker-container-ipc)
+ [Use AWS credentials in Docker container components \(Linux\)](#docker-container-token-exchange-service)
+ [Use stream manager in Docker container components \(Linux\)](#docker-container-stream-manager)

## Requirements<a name="run-docker-container-requirements"></a>

To run a Docker container in a component, you need the following:
+ A Greengrass core device\. If you don't have one, see [Tutorial: Getting started with AWS IoT Greengrass V2](getting-started.md)\.
+ <a name="docker-engine-requirement"></a>[Docker Engine](https://docs.docker.com/engine/) 1\.9\.1 or later installed on the Greengrass core device\. Version 20\.10 is the latest version that is verified to work with the AWS IoT Greengrass Core software\. You must install Docker directly on the core device before you deploy components that run Docker containers\.
**Tip**  
You can also configure the core device to install Docker Engine when the component installs\. For example, the following install script installs Docker Engine before it loads the Docker image\. This install script works on Debian\-based Linux distributions, such as Ubuntu\. If you configure the component to install Docker Engine with this command, you may need to set `RequiresPrivilege` to `true` in the lifecycle script to run the installation and `docker` commands\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.  

  ```
  apt-get install docker-ce docker-ce-cli containerd.io && docker load -i {artifacts:path}/hello-world.tar
  ```
+ <a name="docker-user-permissions-requirement"></a>The system user that runs a Docker container component must have root or administrator permissions, or you must configure Docker to run it as a non\-root or non\-admistrator user\.
  + On Linux devices, you can add a user to the `docker` group to call `docker` commands without `sudo`\.
  + On Windows devices, you can add a user to the `docker-users` group to call `docker` commands without adminstrator privileges\.

------
#### [ Linux or Unix ]

  To add `ggc_user`, or the non\-root user that you use to run Docker container components, to the `docker` group, run the following command\.

  ```
  sudo usermod -aG docker ggc_user
  ```

  For more information, see [Manage Docker as a non\-root user](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)\.

------
#### [ Windows Command Prompt \(CMD\) ]

  To add `ggc_user`, or the user that you use to run Docker container components, to the `docker-users` group, run the following command as an administrator\.

  ```
  net localgroup docker-users ggc_user /add
  ```

------
#### [ Windows PowerShell ]

  To add `ggc_user`, or the user that you use to run Docker container components, to the `docker-users` group, run the following command as an administrator\.

  ```
  Add-LocalGroupMember -Group docker-users -Member ggc_user
  ```

------
+ Files accessed by the Docker container component [mounted as a volume](https://docs.docker.com/storage/volumes/) in the Docker container\.
+ <a name="docker-proxy-requirement"></a>If you [configure the AWS IoT Greengrass Core software to use a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy), you must [configure Docker to use the same proxy server](https://docs.docker.com/network/proxy/)\.

In addition to these requirements, you must also meet the following requirements if they apply to your environment:
+ To use [Docker Compose](https://docs.docker.com/compose/) to create and start your Docker containers, install Docker Compose on your Greengrass core device, and upload your Docker Compose file to an S3 bucket\. You must store your Compose file in an S3 bucket in the same AWS account and AWS Region as the component\. For an example that uses the `docker-compose up` command in a custom component, see [Run a Docker container from a public image in Amazon ECR or Docker Hub](#run-docker-container-public-ecr-dockerhub)\.
+ If you run AWS IoT Greengrass behind a network proxy, configure the Docker daemon to use a [proxy server](https://docs.docker.com/network/proxy/)\. 
+ If your Docker images are stored in Amazon ECR or Docker Hub, include the [Docker component manager](docker-application-manager-component.md) component as a dependency in your Docker container component\. You must start the Docker daemon on the core device before you deploy your component\. 

  Also, include the image URIs as component artifacts\. Image URIs must be in the format `docker:registry/image[:tag|@digest]` as shown in the following examples:<a name="docker-image-artifact-uri"></a>
  + Private Amazon ECR image: `docker:account-id.dkr.ecr.region.amazonaws.com/repository/image[:tag|@digest]`
  + Public Amazon ECR image: `docker:public.ecr.aws/repository/image[:tag|@digest]`
  + Public Docker Hub image: `docker:name[:tag|@digest]`

  For more information about running Docker containers from images stored in public repositories, see [Run a Docker container from a public image in Amazon ECR or Docker Hub](#run-docker-container-public-ecr-dockerhub)\.
+ If your Docker images are stored in an Amazon ECR private repository, then you must include the token exchange service component as a dependency in the Docker container component\. Also, the [Greengrass device role](device-service-role.md) must allow the `ecr:GetAuthorizationToken`, `ecr:BatchGetImage`, and `ecr:GetDownloadUrlForLayer` actions, as shown in the following example IAM policy\. 

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "ecr:GetAuthorizationToken",
          "ecr:BatchGetImage",
          "ecr:GetDownloadUrlForLayer"
        ],
        "Resource": [
          "*"
        ],
        "Effect": "Allow"
      }
    ]
  }
  ```

  For information about running Docker containers from images stored in an Amazon ECR private repository, see [Run a Docker container from a private image in Amazon ECR](#run-docker-container-private-ecr)\.
+ To use Docker images stored in an Amazon ECR private repository, the private repository must be in the same AWS Region as the core device\.
+ If your Docker images or Compose files are stored in an S3 bucket, the [Greengrass device role](device-service-role.md) must allow the `s3:GetObject` permission to allow core devices to download the images as component artifacts, as shown in the following example IAM policy\. 

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "s3:GetObject"
        ],
        "Resource": [
          "*"
        ],
        "Effect": "Allow"
      }
    ]
  }
  ```

  For information about running Docker containers from images stored in Amazon S3, see [Run a Docker container from an image in Amazon S3](#run-docker-container-s3)\.
+ <a name="docker-greengrass-features-requirements"></a>To use interprocess communication \(IPC\), AWS credentials, or stream manager in your Docker container component, you must specify additional options when you run the Docker container\. For more information, see the following:<a name="docker-greengrass-features-requirements-links"></a>
  + [Use interprocess communication in Docker container components](#docker-container-ipc)
  + [Use AWS credentials in Docker container components \(Linux\)](#docker-container-token-exchange-service)
  + [Use stream manager in Docker container components \(Linux\)](#docker-container-stream-manager)

## Run a Docker container from a public image in Amazon ECR or Docker Hub<a name="run-docker-container-public-ecr-dockerhub"></a>

This section describes how you can create a custom component that uses Docker Compose to run a Docker container from Docker images that are stored Amazon ECR and Docker Hub\.

**To run a Docker container using Docker Compose**

1. Create and upload a Docker Compose file to an Amazon S3 bucket\. Make sure that the [Greengrass device role](device-service-role.md) allows the `s3:GetObject` permission to enable the device to access the Compose file\. The example Compose file shown in the following example includes the Amazon CloudWatch Agent image from Amazon ECR and the MySQL image from Docker Hub\.

   ```
   version: "3"
   services:
     cloudwatchagent:
       image: "public.ecr.aws/cloudwatch-agent/cloudwatch-agent:latest"
     mysql:
       image: "mysql:8.0"
   ```

1. [Create a custom component](create-components.md) on your AWS IoT Greengrass core device\. The example recipe shown in the following example has the following properties:
   + The Docker application manager component as a dependency\. This component enables AWS IoT Greengrass to download images from public Amazon ECR and Docker Hub repositories\.
   + A component artifact that specifies a Docker image in a public Amazon ECR repository\.
   + A component artifact that specifies a Docker image in a public Docker Hub repository\.
   + A component artifact that specifies the Docker Compose file that includes containers for the Docker images that you want to run\. 
   + A lifecycle run script that uses [docker\-compose up](https://docs.docker.com/compose/reference/up/) to create and start a container from the specified images\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.MyDockerComposeComponent",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "A component that uses Docker Compose to run images from public Amazon ECR and Docker Hub.",
     "ComponentPublisher": "Amazon",
     "ComponentDependencies": {
       "aws.greengrass.DockerApplicationManager": {
         "VersionRequirement": "~2.0.0"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "all"
         },
         "Lifecycle": {
           "Run": "docker-compose -f {artifacts:path}/docker-compose.yaml up"
         },
         "Artifacts": [
           {
             "URI": "docker:public.ecr.aws/cloudwatch-agent/cloudwatch-agent:latest"
           },
           {
             "URI": "docker:mysql:8.0"
           },
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/folder/docker-compose.yaml"
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.MyDockerComposeComponent
   ComponentVersion: '1.0.0'
   ComponentDescription: 'A component that uses Docker Compose to run images from public Amazon ECR and Docker Hub.'
   ComponentPublisher: Amazon
   ComponentDependencies:
     aws.greengrass.DockerApplicationManager:
       VersionRequirement: ~2.0.0
   Manifests:
     - Platform:
         os: all
       Lifecycle:
           Run: docker-compose -f {artifacts:path}/docker-compose.yaml up
       Artifacts:
         - URI: "docker:public.ecr.aws/cloudwatch-agent/cloudwatch-agent:latest"
         - URI: "docker:mysql:8.0"
         - URI: "s3://DOC-EXAMPLE-BUCKET/folder/docker-compose.yaml"
   ```

------
**Note**  
<a name="docker-greengrass-features-requirements"></a>To use interprocess communication \(IPC\), AWS credentials, or stream manager in your Docker container component, you must specify additional options when you run the Docker container\. For more information, see the following:  
[Use interprocess communication in Docker container components](#docker-container-ipc)
[Use AWS credentials in Docker container components \(Linux\)](#docker-container-token-exchange-service)
[Use stream manager in Docker container components \(Linux\)](#docker-container-stream-manager)

1. [Test the component](test-components.md) to verify that it works as expected\.
**Important**  
You must install and start the Docker daemon before you deploy the component\.

   After you deploy the component locally, you can run the [docker container ls](https://docs.docker.com/engine/reference/commandline/container_ls/) command to verify that your container runs\.

   ```
   docker container ls
   ```

1. When the component is ready, upload the component to AWS IoT Greengrass to deploy to other core devices\. For more information, see [Publish components to deploy to your core devices](publish-components.md)\.

## Run a Docker container from a private image in Amazon ECR<a name="run-docker-container-private-ecr"></a>

This section describes how you can create a custom component that runs a Docker container from a Docker image that is stored in a private repository in Amazon ECR\.

**To run a Docker container**

1. [Create a custom component](create-components.md) on your AWS IoT Greengrass core device\. Use the following example recipe, which has the following properties:
   + The Docker application manager component as a dependency\. This component enables AWS IoT Greengrass to manage credentials to download images from private repositories\.
   + The token exchange service component as a dependency\. This component enables AWS IoT Greengrass to retrieve AWS credentials to interact with Amazon ECR\.
   + A component artifact that specifies a Docker image in a private Amazon ECR repository\.
   + A lifecycle run script that uses [docker run](https://docs.docker.com/engine/reference/commandline/run/) to create and start a container from the image\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.MyPrivateDockerComponent",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "A component that runs a Docker container from a private Amazon ECR image.",
     "ComponentPublisher": "Amazon",
     "ComponentDependencies": {
       "aws.greengrass.DockerApplicationManager": {
         "VersionRequirement": "~2.0.0"
       },
       "aws.greengrass.TokenExchangeService": {
         "VersionRequirement": "~2.0.0"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "all"
         },
         "Lifecycle": {
           "Run": "docker run account-id.dkr.ecr.region.amazonaws.com/repository[:tag|@digest]"
         },
         "Artifacts": [
           {
             "URI": "docker:account-id.dkr.ecr.region.amazonaws.com/repository[:tag|@digest]"
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.MyPrivateDockerComponent
   ComponentVersion: '1.0.0'
   ComponentDescription: 'A component that runs a Docker container from a private Amazon ECR image.'
   ComponentPublisher: Amazon
   ComponentDependencies:
     aws.greengrass.DockerApplicationManager:
       VersionRequirement: ~2.0.0
     aws.greengrass.TokenExchangeService:
       VersionRequirement: ~2.0.0
   Manifests:
     - Platform:
         os: all
       Lifecycle:
           Run: docker run account-id.dkr.ecr.region.amazonaws.com/repository[:tag|@digest]
       Artifacts:
         - URI: "docker:account-id.dkr.ecr.region.amazonaws.com/repository[:tag|@digest]"
   ```

------
**Note**  
<a name="docker-greengrass-features-requirements"></a>To use interprocess communication \(IPC\), AWS credentials, or stream manager in your Docker container component, you must specify additional options when you run the Docker container\. For more information, see the following:  
[Use interprocess communication in Docker container components](#docker-container-ipc)
[Use AWS credentials in Docker container components \(Linux\)](#docker-container-token-exchange-service)
[Use stream manager in Docker container components \(Linux\)](#docker-container-stream-manager)

1. [Test the component](test-components.md) to verify that it works as expected\.
**Important**  
You must install and start the Docker daemon before you deploy the component\.

   After you deploy the component locally, you can run the [docker container ls](https://docs.docker.com/engine/reference/commandline/container_ls/) command to verify that your container runs\.

   ```
   docker container ls
   ```

1. Upload the component to AWS IoT Greengrass to deploy to other core devices\. For more information, see [Publish components to deploy to your core devices](publish-components.md)\.

## Run a Docker container from an image in Amazon S3<a name="run-docker-container-s3"></a>

This section describes how you can run a Docker container in a component from a Docker image that is stored in Amazon S3\.

**To run a Docker container in a component from an image in Amazon S3**

1. Run the [docker save](https://docs.docker.com/engine/reference/commandline/save/) command to create a backup of a Docker container\. You provide this backup as a component artifact to run the container on AWS IoT Greengrass\. Replace *hello\-world* with the name of the image, and replace *hello\-world\.tar* with the name of the archive file to create\.

   ```
   docker save hello-world > artifacts/com.example.MyDockerComponent/1.0.0/hello-world.tar
   ```

1. [Create a custom component](create-components.md) on your AWS IoT Greengrass core device\. Use the following example recipe, which has the following properties:
   + A lifecycle install script that uses [docker load](https://docs.docker.com/engine/reference/commandline/load/) to load a Docker image from an archive\.
   + A lifecycle run script that uses [docker run](https://docs.docker.com/engine/reference/commandline/run/) to create and start a container from the image\. The `--rm` option cleans up the container when it exits\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.MyS3DockerComponent",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "A component that runs a Docker container from an image in an S3 bucket.",
     "ComponentPublisher": "Amazon",
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Install": {
             "Script": "docker load -i {artifacts:path}/hello-world.tar"
           },
           "Run": {
             "Script": "docker run --rm hello-world"
           }
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
   ComponentName: com.example.MyS3DockerComponent
   ComponentVersion: '1.0.0'
   ComponentDescription: 'A component that runs a Docker container from an image in an S3 bucket.'
   ComponentPublisher: Amazon
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Install:
           Script: docker load -i {artifacts:path}/hello-world.tar
         Run:
           Script: docker run --rm hello-world
   ```

------
**Note**  
<a name="docker-greengrass-features-requirements"></a>To use interprocess communication \(IPC\), AWS credentials, or stream manager in your Docker container component, you must specify additional options when you run the Docker container\. For more information, see the following:  
[Use interprocess communication in Docker container components](#docker-container-ipc)
[Use AWS credentials in Docker container components \(Linux\)](#docker-container-token-exchange-service)
[Use stream manager in Docker container components \(Linux\)](#docker-container-stream-manager)

1. [Test the component](test-components.md) to verify that it works as expected\.

   After you deploy the component locally, you can run the [docker container ls](https://docs.docker.com/engine/reference/commandline/container_ls/) command to verify that your container runs\.

   ```
   docker container ls
   ```

1. When the component is ready, upload the Docker image archive to an S3 bucket, and add its URI to the component recipe\. Then, you can upload the component to AWS IoT Greengrass to deploy to other core devices\. For more information, see [Publish components to deploy to your core devices](publish-components.md)\.

   When you're done, the component recipe should look like the following example\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.MyS3DockerComponent",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "A component that runs a Docker container from an image in an S3 bucket.",
     "ComponentPublisher": "Amazon",
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Install": {
             "Script": "docker load -i {artifacts:path}/hello-world.tar"
           },
           "Run": {
             "Script": "docker run --rm hello-world"
           }
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.MyDockerComponent/1.0.0/hello-world.tar"
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.MyS3DockerComponent
   ComponentVersion: '1.0.0'
   ComponentDescription: 'A component that runs a Docker container from an image in an S3 bucket.'
   ComponentPublisher: Amazon
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Install:
           Script: docker load -i {artifacts:path}/hello-world.tar
         Run:
           Script: docker run --rm hello-world
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.MyDockerComponent/1.0.0/hello-world.tar
   ```

------

## Use interprocess communication in Docker container components<a name="docker-container-ipc"></a>

You can use the Greengrass interprocess communication \(IPC\) library in the AWS IoT Device SDK to communicate with the Greengrass nucleus, other Greengrass components, and AWS IoT Core\. For more information, see [Use the AWS IoT Device SDK to communicate with the Greengrass nucleus, other components, and AWS IoT CoreCommunicate with the Greengrass nucleus, other components, and AWS IoT Core](interprocess-communication.md)\.

To use IPC in a Docker container component, you must run the Docker container with the following parameters:
+ Mount the IPC socket in the container\. The Greengrass nucleus provides the IPC socket file path in the `AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT` environment variable\.
+ Set the `SVCUID` and `AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT` environment variables to the values that the Greengrass nucleus provides to components\. Your component uses these environment variables to authenticate connections to the Greengrass nucleus\.

**Example recipe: Publish an MQTT message to AWS IoT Core \(Python\)**  
The following recipe defines an example Docker container component that publishes an MQTT message to AWS IoT Core\. This recipe has the following properties:  
+ An authorization policy \(`accessControl`\) that allows the component to publish MQTT messages to AWS IoT Core on all topics\. For more information, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies) and [AWS IoT Core MQTT IPC authorization](ipc-iot-core-mqtt.md#ipc-iot-core-mqtt-authorization)\.
+ A component artifact that specifies a Docker image as a TAR archive in Amazon S3\.
+ A lifecycle install script that loads the Docker image from the TAR archive\.
+ A lifecycle run script that runs a Docker container from the image\. The [Docker run](https://docs.docker.com/engine/reference/run/) command has the following arguments:
  + The `-v` argument mounts the Greengrass IPC socket in the container\.
  + The first two `-e` arguments set the required environment variables in the Docker container\.
  + The additional `-e` arguments set environment variables used by this example\.
  + The `--rm` argument cleans up the container when it exits\.

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.python.docker.PublishToIoTCore",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "Uses interprocess communication to publish an MQTT message to IoT Core.",
  "ComponentPublisher": "Amazon",
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "topic": "test/topic/java",
      "message": "Hello, World!",
      "qos": "1",
      "accessControl": {
        "aws.greengrass.ipc.mqttproxy": {
          "com.example.python.docker.PublishToIoTCore:pubsub:1": {
            "policyDescription": "Allows access to publish to IoT Core on all topics.",
            "operations": [
              "aws.greengrass#PublishToIoTCore"
            ],
            "resources": [
              "*"
            ]
          }
        }
      }
    }
  },
  "Manifests": [
    {
      "Platform": {
        "os": "all"
      },
      "Lifecycle": {
        "Install": "docker load -i {artifacts:path}/publish-to-iot-core.tar",
        "Run": "docker run -v $AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT:$AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT -e SVCUID -e AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT -e MQTT_TOPIC=\"{configuration:/topic}\" -e MQTT_MESSAGE=\"{configuration:/message}\" -e MQTT_QOS=\"{configuration:/qos}\" --rm publish-to-iot-core"
      },
      "Artifacts": [
        {
          "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.python.docker.PublishToIoTCore/1.0.0/publish-to-iot-core.tar"
        }
      ]
    }
  ]
}
```

```
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.python.docker.PublishToIoTCore
ComponentVersion: 1.0.0
ComponentDescription: Uses interprocess communication to publish an MQTT message to IoT Core.
ComponentPublisher: Amazon
ComponentConfiguration:
  DefaultConfiguration:
    topic: 'test/topic/java'
    message: 'Hello, World!'
    qos: '1'
    accessControl:
      aws.greengrass.ipc.mqttproxy:
        'com.example.python.docker.PublishToIoTCore:pubsub:1':
          policyDescription: Allows access to publish to IoT Core on all topics.
          operations:
            - 'aws.greengrass#PublishToIoTCore'
          resources:
            - '*'
Manifests:
  - Platform:
      os: all
    Lifecycle:
      Install: 'docker load -i {artifacts:path}/publish-to-iot-core.tar'
      Run: |
        docker run \
          -v $AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT:$AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT \
          -e SVCUID \
          -e AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT \
          -e MQTT_TOPIC="{configuration:/topic}" \
          -e MQTT_MESSAGE="{configuration:/message}" \
          -e MQTT_QOS="{configuration:/qos}" \
          --rm publish-to-iot-core
    Artifacts:
      - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.python.docker.PublishToIoTCore/1.0.0/publish-to-iot-core.tar
```

## Use AWS credentials in Docker container components \(Linux\)<a name="docker-container-token-exchange-service"></a>

You can use the [token exchange service component](token-exchange-service-component.md) to interact with AWS services in Greengrass components\. This component provides AWS credentials from the core device's [token exchange role](device-service-role.md) using a local container server\. For more information, see [Interact with AWS services](interact-with-aws-services.md)\.

**Note**  
The example in this section works only on Linux core devices\.

To use AWS credentials from the token exchange service in a Docker container component, you must run the Docker container with the following parameters:
+ Provide access to the host network using the `--network=host` argument\. This option enables the Docker container to connect to the local token exchange service to retrieve AWS credentials\. This argument works on only Docker for Linux\.
**Warning**  <a name="docker-network-host-security-warning"></a>
This option gives the container access to all local network interfaces on the host, so this option is less secure than if you run Docker containers without this access to the host network\. Consider this when you develop and run Docker container components that use this option\. For more information, see [Network: host](https://docs.docker.com/engine/reference/run/#network-host) in the *Docker Documentation*\.
+ Set the `AWS_CONTAINER_CREDENTIALS_FULL_URI` and `AWS_CONTAINER_AUTHORIZATION_TOKEN` environment variables to the values that the Greengrass nucleus provides to components\. AWS SDKs use these environment variables to retrieve AWS credentials\.

**Example recipe: List S3 buckets in a Docker container component \(Python\)**  
The following recipe defines an example Docker container component that lists the S3 buckets in your AWS account\. This recipe has the following properties:  
+ The token exchange service component as a dependency\. This dependency enables the component to retrieve AWS credentials to interact with other AWS services\.
+ A component artifact that specifies a Docker image as a tar archive in Amazon S3\.
+ A lifecycle install script that loads the Docker image from the TAR archive\.
+ A lifecycle run script that runs a Docker container from the image\. The [Docker run](https://docs.docker.com/engine/reference/run/) command has the following arguments:
  + The `--network=host` argument provides the container access to the host network, so the container can connect to the token exchange service\.
  + The `-e` argument sets the required environment variables in the Docker container\.
  + The `--rm` argument cleans up the container when it exits\.

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.python.docker.ListS3Buckets",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "Uses the token exchange service to lists your S3 buckets.",
  "ComponentPublisher": "Amazon",
  "ComponentDependencies": {
    "aws.greengrass.TokenExchangeService": {
      "VersionRequirement": "^2.0.0",
      "DependencyType": "HARD"
    }
  },
  "Manifests": [
    {
      "Platform": {
        "os": "linux"
      },
      "Lifecycle": {
        "Install": "docker load -i {artifacts:path}/list-s3-buckets.tar",
        "Run": "docker run --network=host -e AWS_CONTAINER_AUTHORIZATION_TOKEN -e AWS_CONTAINER_CREDENTIALS_FULL_URI --rm list-s3-buckets"
      },
      "Artifacts": [
        {
          "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.python.docker.ListS3Buckets/1.0.0/list-s3-buckets.tar"
        }
      ]
    }
  ]
}
```

```
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.python.docker.ListS3Buckets
ComponentVersion: 1.0.0
ComponentDescription: Uses the token exchange service to lists your S3 buckets.
ComponentPublisher: Amazon
ComponentDependencies:
  aws.greengrass.TokenExchangeService:
    VersionRequirement: ^2.0.0
    DependencyType: HARD
Manifests:
  - Platform:
      os: linux
    Lifecycle:
      Install: 'docker load -i {artifacts:path}/list-s3-buckets.tar'
      Run: |
        docker run \
          --network=host \
          -e AWS_CONTAINER_AUTHORIZATION_TOKEN \
          -e AWS_CONTAINER_CREDENTIALS_FULL_URI \
          --rm list-s3-buckets
    Artifacts:
      - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.python.docker.ListS3Buckets/1.0.0/list-s3-buckets.tar
```

## Use stream manager in Docker container components \(Linux\)<a name="docker-container-stream-manager"></a>

You can use the [stream manager component](stream-manager-component.md) to manage data streams in Greengrass components\. This component enables you to process data streams and transfer high\-volume IoT data to the AWS Cloud\. AWS IoT Greengrass provides a stream manager SDK that you use to interact with the stream manager component\. For more information, see [Manage data streams on Greengrass core devices](manage-data-streams.md)\.

**Note**  
The example in this section works only on Linux core devices\.

To use the stream manager SDK in a Docker container component, you must run the Docker container with the following parameters:
+ Provide access to the host network using the `--network=host` argument\. This option enables the Docker container to interact with the stream manager component over a local TLS connection\. This argument works on only Docker for Linux
**Warning**  <a name="docker-network-host-security-warning"></a>
This option gives the container access to all local network interfaces on the host, so this option is less secure than if you run Docker containers without this access to the host network\. Consider this when you develop and run Docker container components that use this option\. For more information, see [Network: host](https://docs.docker.com/engine/reference/run/#network-host) in the *Docker Documentation*\.
+ If you configure the stream manager component to require authentication, which is the default behavior, set the `AWS_CONTAINER_CREDENTIALS_FULL_URI` environment variable to the value that the Greengrass nucleus provides to components\. For more information, see [stream manager configuration](stream-manager-component.md#stream-manager-component-configuration)\.
+ If you configure the stream manager component to use a non\-default port, use [interprocess communication \(IPC\)](interprocess-communication.md) to get the port from the stream manager component configuration\. You must run the Docker container with additional options to use IPC\. For more information, see the following:
  + [Connect to stream manager in application code](use-stream-manager-in-custom-components.md#connect-to-stream-manager)
  + [Use interprocess communication in Docker container components](#docker-container-ipc)

**Example recipe: Stream a file to an S3 bucket in a Docker container component \(Python\)**  
The following recipe defines an example Docker container component that creates a file and streams it to an S3 bucket\. This recipe has the following properties:  
+ The stream manager component as a dependency\. This dependency enables the component to use the stream manager SDK to interact with the stream manager component\.
+ A component artifact that specifies a Docker image as a TAR archive in Amazon S3\.
+ A lifecycle install script that loads the Docker image from the TAR archive\.
+ A lifecycle run script that runs a Docker container from the image\. The [Docker run](https://docs.docker.com/engine/reference/run/) command has the following arguments:
  + The `--network=host` argument provides the container access to the host network, so the container can connect to the stream manager component\.
  + The first `-e` argument sets the required `AWS_CONTAINER_CREDENTIALS_FULL_URI` environment variable in the Docker container\.
  + The additional `-e` arguments set environment variables used by this example\.
  + The `-v` argument mounts the component's [work folder](component-recipe-reference.md#component-recipe-work-path) in the container\. This example creates a file in the work folder to upload that file to Amazon S3 using stream manager\.
  + The `--rm` argument cleans up the container when it exits\.

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.python.docker.StreamFileToS3",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "Creates a text file and uses stream manager to stream the file to S3.",
  "ComponentPublisher": "Amazon",
  "ComponentDependencies": {
    "aws.greengrass.StreamManager": {
      "VersionRequirement": "^2.0.0",
      "DependencyType": "HARD"
    }
  },
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "bucketName": ""
    }
  },
  "Manifests": [
    {
      "Platform": {
        "os": "linux"
      },
      "Lifecycle": {
        "Install": "docker load -i {artifacts:path}/stream-file-to-s3.tar",
        "Run": "docker run --network=host -e AWS_CONTAINER_AUTHORIZATION_TOKEN -e BUCKET_NAME=\"{configuration:/bucketName}\" -e WORK_PATH=\"{work:path}\" -v {work:path}:{work:path} --rm stream-file-to-s3"
      },
      "Artifacts": [
        {
          "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.python.docker.StreamFileToS3/1.0.0/stream-file-to-s3.tar"
        }
      ]
    }
  ]
}
```

```
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.python.docker.StreamFileToS3
ComponentVersion: 1.0.0
ComponentDescription: Creates a text file and uses stream manager to stream the file to S3.
ComponentPublisher: Amazon
ComponentDependencies:
  aws.greengrass.StreamManager:
    VersionRequirement: ^2.0.0
    DependencyType: HARD
ComponentConfiguration:
  DefaultConfiguration:
    bucketName: ''
Manifests:
  - Platform:
      os: linux
    Lifecycle:
      Install: 'docker load -i {artifacts:path}/stream-file-to-s3.tar'
      Run: |
        docker run \
          --network=host \
          -e AWS_CONTAINER_AUTHORIZATION_TOKEN \
          -e BUCKET_NAME="{configuration:/bucketName}" \
          -e WORK_PATH="{work:path}" \
          -v {work:path}:{work:path} \
          --rm stream-file-to-s3
    Artifacts:
      - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.python.docker.StreamFileToS3/1.0.0/stream-file-to-s3.tar
```