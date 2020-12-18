# Run a Docker container<a name="run-docker-container"></a>

You can configure AWS IoT Greengrass components to run a [Docker](https://www.docker.com/) container\.

You can store Docker images in Amazon S3 to provide them as component artifacts\.

**Topics**
+ [Requirements](#run-docker-container-requirements)
+ [Run a Docker container from an image in Amazon S3](#s3-docker-image)
+ [Use interprocess communication in Docker container components](#docker-container-ipc)

## Requirements<a name="run-docker-container-requirements"></a>

To run a Docker container in a component, you need the following:
+ A Greengrass core device\. If you don't have one, see [Getting started with AWS IoT Greengrass V2](getting-started.md)\.
+ [Docker Engine](https://docs.docker.com/engine/) installed on your Greengrass core device\.
+ [Docker configured for you to run it as a non\-root user](https://docs.docker.com/engine/install/linux-postinstall/)\. This lets you call `docker` commands without `sudo`\.
+ To use interprocess communication \(IPC\) in a Docker container component, you must set AWS IoT Greengrass Core environment variables in the Docker container\. For more information, see [Use interprocess communication in Docker container components](#docker-container-ipc)\.

## Run a Docker container from an image in Amazon S3<a name="s3-docker-image"></a>

This section describes how you can store a Docker image in Amazon S3 to run a Docker container in a component\. In this procedure, you do the following:
+ Use component lifecycle commands to load a Docker image and run the image in a container\.
+ Store the image in an S3 bucket to provide it as an artifact that your component uses\.

**To run a Docker container in a component**

1. Run the [docker save](https://docs.docker.com/engine/reference/commandline/save/) command to create a backup of a Docker container\. You provide this backup as a component artifact to run the container on AWS IoT Greengrass\. Replace *hello\-world* with the name of the image, and replace *hello\-world\.tar* with the name of the archive file to create\.

   ```
   docker save hello-world > artifacts/com.example.MyDockerComponent/1.0.0/hello-world.tar
   ```

1. [Create a custom component](create-components.md#develop-component) on your AWS IoT Greengrass core device\. Use the following example recipe, which has the following notable properties:
   + A lifecycle install script that uses [docker load](https://docs.docker.com/engine/reference/commandline/load/) to load a Docker image from an archive\.
   + A lifecycle run script that uses [docker run](https://docs.docker.com/engine/reference/commandline/run/) to create and start a container from the image\. The `--rm` option cleans up the container when it exits\.

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.MyDockerComponent
   ComponentVersion: '1.0.0'
   ComponentDescription: A component that runs a Docker container.
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
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.MyDockerComponent",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "A component that runs a Docker container.",
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
**Note**  
To use interprocess communication \(IPC\) in a Docker container component, you must set AWS IoT Greengrass Core environment variables in the Docker container\. For more information, see [Use interprocess communication in Docker container components](#docker-container-ipc)\.  
You can also configure AWS IoT Greengrass to install Docker Engine when the component installs\. For example, the following install script installs Docker Engine before it loads the Docker image\. This install script works on Debian\-based Linux distributions, such as Ubuntu\. If you configure the component to install Docker Engine with this command, you may need to add `sudo` to the `docker` commands to run them\.  

   ```
   sudo apt-get install docker-ce docker-ce-cli containerd.io && sudo docker load -i {artifacts:path}/hello-world.tar
   ```

1. [Test the component](create-components.md#test-component) to verify that it works as expected\.

   After you deploy the component locally, you can run the [docker container ls](https://docs.docker.com/engine/reference/commandline/container_ls/) command to verify that your container runs\.

   ```
   docker container ls
   ```

1. When the component is ready, upload the Docker image archive to an S3 bucket, and add its URI to the component recipe\. Then, you can upload the component to AWS IoT Greengrass to deploy to other core devices\. For more information, see [Upload components to deploy to your core devicesUpload components to deploy](upload-components.md)\.

   When you're done, the component recipe should look like the following example\.

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.MyDockerComponent
   ComponentVersion: '1.0.0'
   ComponentDescription: A component that runs a Docker container.
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
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.MyDockerComponent",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "A component that runs a Docker container.",
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

## Use interprocess communication in Docker container components<a name="docker-container-ipc"></a>

[Interprocess communication](interprocess-communication.md) enables you to develop components that can communicate with AWS IoT Greengrass Core and other components\. This feature requires the following environment variables that AWS IoT Greengrass Core provides to components\. You must set these environment variables in the Docker container that you run\.
+ `AWS_REGION`
+ `SVCUID`
+ `AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT`
+ `AWS_CONTAINER_AUTHORIZATION_TOKEN`
+ `AWS_CONTAINER_CREDENTIALS_FULL_URI`

Use the `-e`, `--env`, or `--env-file` parameter to [set environment variables in a Docker container](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file)\. The following example component recipe uses the `-e` parameter to set the required environment variables from AWS IoT Greengrass Core in the Docker container\.

------
#### [ YAML ]

```
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.MyDockerComponent
ComponentVersion: '1.0.0'
ComponentDescription: A component that runs a Docker container.
ComponentPublisher: Amazon
ComponentConfiguration:
  DefaultConfiguration:
    accessControl: >-
      aws.greengrass.ipc.pubsub:
        "com.example.MyDockerComponent:pubsub:1":
          policyDescription: Allows access to publish and subscribe to all topics.
          operations:
            - "*"
          resources:
            - "*"
Manifests:
  - Platform:
      os: linux
    Lifecycle:
      Install:
        Script: docker load -i {artifacts:path}/hello-world.tar
      Run:
        Script: docker run -e AWS_REGION -e SVCUID -e AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT -e AWS_CONTAINER_AUTHORIZATION_TOKEN -e AWS_CONTAINER_CREDENTIALS_FULL_URI --rm hello-world
    Artifacts:
      - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.MyDockerComponent/1.0.0/hello-world.tar
```

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.MyDockerComponent",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "A component that runs a Docker container.",
  "ComponentPublisher": "Amazon",
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "accessControl": "{\"aws.greengrass.ipc.pubsub\":{\"com.example.MyDockerComponent:pubsub:1\":{\"policyDescription\":\"Allows access to publish and subscribe to all topics.\",\"operations\":[\"*\"],\"resources\":[\"*\"]}}}"
    }
  },
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
          "Script": "docker run -e AWS_REGION -e SVCUID -e AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT -e AWS_CONTAINER_AUTHORIZATION_TOKEN -e AWS_CONTAINER_CREDENTIALS_FULL_URI --rm hello-world"
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