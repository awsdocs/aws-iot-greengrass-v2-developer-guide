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

## Requirements<a name="run-docker-container-requirements"></a>

To run a Docker container in a component, you need the following:
+ A Greengrass core device\. If you don't have one, see [Getting started with AWS IoT Greengrass V2](getting-started.md)\.
+ [Docker Engine](https://docs.docker.com/engine/) 1\.9\.1 or later installed on your Greengrass core device\. Version 20\.10 is the latest version that is verified to work with the connector\. You must install Docker directly on the core device before you deploy custom components that run Docker containers\. 
+ Root user permissions or Docker configured for you to run it as a [non\-root user](https://docs.docker.com/engine/install/linux-postinstall/)\. Adding a user to the `docker` group enables you to call `docker` commands without `sudo`\. To add `ggc_user`, or the non\-root user that you use to run AWS IoT Greengrass, to the `docker` group that you configure, run **sudo usermod \-aG docker *user\-name***\.
+ Files accessed by the Docker container component [mounted as a volume](https://docs.docker.com/storage/volumes/) in the Docker container\.

In addition to these requirements, you must also meet the following requirements if they apply to your environment:
+ To use [Docker Compose](https://docs.docker.com/compose/) to create and start your Docker containers, install Docker Compose on your Greengrass core device, and upload your Docker Compose file to an S3 bucket\. You must store your Compose file in an S3 bucket in the same AWS account and AWS Region as the component\. For an example that uses the `docker-compose up` command in a custom component, see [Run a Docker container from a public image in Amazon ECR or Docker Hub](#run-docker-container-public-ecr-dockerhub)\.
+ If you run AWS IoT Greengrass behind a network proxy, configure the Docker daemon to use a [proxy server](https://docs.docker.com/network/proxy/)\. 
+ If your Docker images are stored in Amazon ECR or Docker Hub, include the [Docker component manager](docker-application-manager-component.md) component as a dependency in your Docker container component\. You must start the Docker daemon on the core device before you deploy your component\. 

  Also, include the image URIs as component artifacts\. Image URIs must be in the format `docker:registry/image[:tag|@digest]` as shown in the following examples:
+ <a name="docker-image-artifact-uri"></a>
  + Private Amazon ECR image: `docker:account-id.dkr.ecr.region.amazonaws.com/repository/image[:tag|@digest]`
  + Public Amazon ECR image: `docker:public.ecr.aws/repository/image[:tag|@digest]`
  + Public Docker Hub image: `docker:name[:tag|@digest]`
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

To use interprocess communication \(IPC\) to enable your Docker container component to communicate with other AWS IoT Greengrass components, you must also set AWS IoT Greengrass Core environment variables in the Docker container and mount the AWS IoT Greengrass Core root folder as a volume in the Docker container\. For more information, see [Use interprocess communication in Docker container components](#docker-container-ipc)\.

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
To use interprocess communication \(IPC\) in a Docker container component, you must set AWS IoT Greengrass Core environment variables in the Docker container\. For more information, see [Use interprocess communication in Docker container components](#docker-container-ipc)\.

1. [Test the component](test-components.md) to verify that it works as expected\.
**Important**  
You must install and start the Docker daemon before you deploy the component\.

   After you deploy the component locally, you can run the [docker container ls](https://docs.docker.com/engine/reference/commandline/container_ls/) command to verify that your container runs\.

   ```
   docker container ls
   ```

1. When the component is ready, upload the component to AWS IoT Greengrass to deploy to other core devices\. For more information, see [Upload components to deploy to your core devicesUpload components to deploy](upload-components.md)\.

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
To use interprocess communication \(IPC\) in a Docker container component, you must set AWS IoT Greengrass Core environment variables in the Docker container\. For more information, see [Use interprocess communication in Docker container components](#docker-container-ipc)\.

1. [Test the component](test-components.md) to verify that it works as expected\.
**Important**  
You must install and start the Docker daemon before you deploy the component\.

   After you deploy the component locally, you can run the [docker container ls](https://docs.docker.com/engine/reference/commandline/container_ls/) command to verify that your container runs\.

   ```
   docker container ls
   ```

1. Upload the component to AWS IoT Greengrass to deploy to other core devices\. For more information, see [Upload components to deploy to your core devicesUpload components to deploy](upload-components.md)\.

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
To use interprocess communication \(IPC\) in a Docker container component, you must set AWS IoT Greengrass Core environment variables in the Docker container\. For more information, see [Use interprocess communication in Docker container components](#docker-container-ipc)\.  
You can also configure AWS IoT Greengrass to install Docker Engine when the component installs\. For example, the following install script installs Docker Engine before it loads the Docker image\. This install script works on Debian\-based Linux distributions, such as Ubuntu\. If you configure the component to install Docker Engine with this command, you may need to add `sudo` to the `docker` commands to run them\.  

   ```
   sudo apt-get install docker-ce docker-ce-cli containerd.io && sudo docker load -i {artifacts:path}/hello-world.tar
   ```

1. [Test the component](test-components.md) to verify that it works as expected\.

   After you deploy the component locally, you can run the [docker container ls](https://docs.docker.com/engine/reference/commandline/container_ls/) command to verify that your container runs\.

   ```
   docker container ls
   ```

1. When the component is ready, upload the Docker image archive to an S3 bucket, and add its URI to the component recipe\. Then, you can upload the component to AWS IoT Greengrass to deploy to other core devices\. For more information, see [Upload components to deploy to your core devicesUpload components to deploy](upload-components.md)\.

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

[Interprocess communication](interprocess-communication.md) enables you to develop components that can communicate with AWS IoT Greengrass Core and other components\. To use interprocess communication in your Docker container components, you must set the following environment variables that AWS IoT Greengrass Core provides to components\.
+ `AWS_REGION`
+ `SVCUID`
+ `AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT`
+ `AWS_CONTAINER_AUTHORIZATION_TOKEN`
+ `AWS_CONTAINER_CREDENTIALS_FULL_URI`

You can use the `-e`, `--env`, or `--env-file` parameter to [set environment variables in the Docker container](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file) that you run\. 

When you start the Docker container, you must also mount as a volume any files that the Docker container component will need to access\. To use interprocess communication, mount the root folder for your AWS IoT Greengrass Core software\. You can use the `-v`, `--volume`, or `--mount` parameter to [ mount a volume in the Docker container](https://docs.docker.com/storage/volumes/) that you run\.

The following example component recipe has the following properties: 
+ The Docker application manager component as a dependency\. This component enables AWS IoT Greengrass to manage credentials to download images from private repositories\.
+ The token exchange service component as a dependency\. This component enables AWS IoT Greengrass to retrieve AWS credentials to interact with Amazon ECR\.
+ A component artifact that specifies a Docker image in a private Amazon ECR repository\.
+ A lifecycle run script that uses [docker run](https://docs.docker.com/engine/reference/commandline/run/) to create and start a container from the image\.
  + The `-e` parameter sets the required environment variables from AWS IoT Greengrass Core in the Docker container\.
  + The `-v` parameter mounts the */greengrass/v2* folder as a volume\. Replace */greengrass/v2* with the path to the root folder that you used to install the AWS IoT Greengrass Core software\. 
  + The `--rm` option cleans up the container when it exits\.

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.MyIPCDockerComponent",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "A component that runs a Docker container and uses interprocess communication.",
  "ComponentPublisher": "Amazon",
  "ComponentDependencies": {
    "aws.greengrass.DockerApplicationManager": {
      "VersionRequirement": "~2.0.0"
    },
    "aws.greengrass.TokenExchangeService": {
      "VersionRequirement": "~2.0.0"
    }
  },
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "accessControl": "{\"aws.greengrass.ipc.pubsub\":{\"com.example.MyDockerComponent:pubsub:1\":{\"policyDescription\":\"Allows access to publish and subscribe to all topics.\",\"operations\":[\"*\"],\"resources\":[\"*\"]}}}"
    }
  },
  "Manifests": [
    {
      "Platform": {
        "os": "all"
      },
      "Lifecycle": {
        "Run": "docker run --rm -v /greengrass/v2:/greengrass/v2 -e AWS_REGION=$AWS_REGION -e SVCUID=$SVCUID -e AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT=$AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT -e AWS_CONTAINER_AUTHORIZATION_TOKEN=$AWS_CONTAINER_AUTHORIZATION_TOKEN -e AWS_CONTAINER_CREDENTIALS_FULL_URI=$AWS_CONTAINER_CREDENTIALS_FULL_URI account-id.dkr.ecr.region.amazonaws.com/repository[:tag|@digest]"
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
ComponentName: com.example.MyIPCDockerComponent
ComponentVersion: 1.0.0
ComponentDescription: 'A component that runs a Docker container and uses interprocess communication.'
ComponentPublisher: Amazon
ComponentDependencies:
  aws.greengrass.DockerApplicationManager:
    VersionRequirement: ~2.0.0
  aws.greengrass.TokenExchangeService:
    VersionRequirement: ~2.0.0
ComponentConfiguration:
  DefaultConfiguration:
    accessControl: '{"aws.greengrass.ipc.pubsub":{"com.example.MyDockerComponent:pubsub:1":{"policyDescription":"Allows access to publish and subscribe to all topics.","operations":["*"],"resources":["*"]}}}'
Manifests:
  - Platform:
      os: all
    Lifecycle:
      Run: 'docker run --rm -v /greengrass/v2:/greengrass/v2 -e AWS_REGION=$AWS_REGION -e SVCUID=$SVCUID -e AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT=$AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT -e AWS_CONTAINER_AUTHORIZATION_TOKEN=$AWS_CONTAINER_AUTHORIZATION_TOKEN -e AWS_CONTAINER_CREDENTIALS_FULL_URI=$AWS_CONTAINER_CREDENTIALS_FULL_URI account-id.dkr.ecr.region.amazonaws.com/repository[:tag|@digest]'
    Artifacts:
      - URI: 'docker:account-id.dkr.ecr.region.amazonaws.com/repository[:tag|@digest]'
```

------