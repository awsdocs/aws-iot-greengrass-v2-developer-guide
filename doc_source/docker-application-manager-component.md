# Docker application manager<a name="docker-application-manager-component"></a>

The Docker application manager component \(`aws.greengrass.DockerApplicationManager`\) enables AWS IoT Greengrass to download Docker images from public image registries\. It also enables AWS IoT Greengrass to manage credentials to download images from private repositories in Amazon Elastic Container Registry \(Amazon ECR\)\. 

When you develop a custom component that runs a Docker container, include the Docker application manager as a dependency to download the Docker images that are specified as artifacts in your component\. For more information, see [Run a Docker container](run-docker-container.md)\.

**Topics**
+ [Versions](#docker-application-manager-component-versions)
+ [Type](#docker-application-manager-component-type)
+ [Operating system](#docker-application-manager-component-os-support)
+ [Requirements](#docker-application-manager-component-requirements)
+ [Dependencies](#docker-application-manager-component-dependencies)
+ [Configuration](#docker-application-manager-component-configuration)
+ [Local log file](#docker-application-manager-component-log-file)
+ [Changelog](#docker-application-manager-component-changelog)
+ [See also](#docker-application-manager-component-see-also)

## Versions<a name="docker-application-manager-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="docker-application-manager-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="docker-application-manager-component-os-support"></a>

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

## Requirements<a name="docker-application-manager-component-requirements"></a>

This component has the following requirements:
+ <a name="docker-engine-requirement"></a>[Docker Engine](https://docs.docker.com/engine/) 1\.9\.1 or later installed on your Greengrass core device\. Version 20\.10 is the latest version that is verified to work with the connector\. You must install Docker directly on the core device before you deploy custom components that run Docker containers\. 
+ <a name="docker-daemon-requirement"></a>The Docker daemon started and running on the core device before you deploy this component\. 
+ Docker images stored in one of the following supported image sources:
  + Public and private image repositories in Amazon Elastic Container Registry \(Amazon ECR\)
  + Public Docker Hub repository
  + Public Docker Trusted Registry
+ Docker images included as artifacts in your custom Docker container components\. Use the following URI formats to specify your Docker images:<a name="docker-image-artifact-uri"></a>
  + Private Amazon ECR image: `docker:account-id.dkr.ecr.region.amazonaws.com/repository/image[:tag|@digest]`
  + Public Amazon ECR image: `docker:public.ecr.aws/repository/image[:tag|@digest]`
  + Public Docker Hub image: `docker:name[:tag|@digest]`

  For more information, see [Run a Docker container](run-docker-container.md)\.
**Note**  
If you don't specify the image tag or image digest in the artifact URI for an image, then the Docker application manager pulls the latest available version of that image when you deploy your custom Docker container component\. To ensure that all of your core devices run the same version of an image, we recommend that you include the image tag or image digest in the artifact URI\.
+ <a name="docker-user-permissions-requirement"></a>The system user that runs a Docker container component must have root or administrator permissions, or you must configure Docker to run it as a non\-root or non\-admistrator user\. On Linux devices, you can add a user to the `docker` group to call `docker` commands without `sudo`\. On Windows devices, you can add a user to the `docker-users` group to call `docker` commands without adminstrator privileges\.

  On Linux, to add `ggc_user`, or the non\-root user that you use to run AWS IoT Greengrass, to the `docker` group that you configure, run the following command\.

  ```
  sudo usermod -aG docker user-name
  ```

  For more information, see the following Docker documentation:
  + Linux: [Manage Docker as a non\-root user](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)
  + Windows: [Install Docker Desktop on Windows](https://docs.docker.com/desktop/windows/install/#install-docker-desktop-on-windows)
+ <a name="docker-proxy-requirement"></a>If you [configure the AWS IoT Greengrass Core software to use a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy), you must [configure Docker to use the same proxy server](https://docs.docker.com/network/proxy/)\.
+ If your Docker images are stored in an Amazon ECR private registry, then you must include the token exchange service component as a dependency in the Docker container component\. Also, the [Greengrass device role](device-service-role.md) must allow the `ecr:GetAuthorizationToken`, `ecr:BatchGetImage`, and `ecr:GetDownloadUrlForLayer` actions, as shown in the following example IAM policy\.

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

### Endpoints and ports<a name="docker-application-manager-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `ecr.region.amazonaws.com`  | 443 | No |  Required if you download Docker images from Amazon ECR\.  | 
|  `hub.docker.com` `registry.hub.docker.com/v1`  | 443 | No |  Required if you download Docker images from Docker Hub\.  | 

## Dependencies<a name="docker-application-manager-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#docker-application-manager-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.0\.4 ]

The following table lists the dependencies for version 2\.0\.4 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.1\.0 <2\.6\.0 | Soft | 

------
#### [ 2\.0\.3 ]

The following table lists the dependencies for version 2\.0\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.1\.0 <2\.5\.0 | Soft | 

------
#### [ 2\.0\.2 ]

The following table lists the dependencies for version 2\.0\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.1\.0 <2\.4\.0 | Soft | 

------
#### [ 2\.0\.1 ]

The following table lists the dependencies for version 2\.0\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.1\.0 <2\.3\.0 | Soft | 

------
#### [ 2\.0\.0 ]

The following table lists the dependencies for version 2\.0\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.1\.0 <2\.2\.0 | Soft | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="docker-application-manager-component-configuration"></a>

This component doesn't have any configuration parameters\.

## Local log file<a name="docker-application-manager-component-log-file"></a>

This component uses the following log file\.

------
#### [ Linux ]

```
/greengrass/v2/logs/aws.greengrass.DockerApplicationManager.log
```

------
#### [ Windows ]

```
C:\greengrass\v2\logs\aws.greengrass.DockerApplicationManager.log
```

------

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux ]

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.DockerApplicationManager.log
  ```

------
#### [ Windows \(PowerShell\) ]

  ```
  Get-Content C:\greengrass\v2\logs\aws.greengrass.DockerApplicationManager.log -Tail 10 -Wait
  ```

------

## Changelog<a name="docker-application-manager-component-changelog"></a>

The following table describes the changes in each version of the component\.


|   **Version**   |   **Changes**   | 
| --- | --- | 
|  2\.0\.4  |  Version updated for Greengrass nucleus version 2\.5\.0 release\.  | 
|  2\.0\.3  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.2  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.1  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.0\.0  |  Initial version\.  | 

## See also<a name="docker-application-manager-component-see-also"></a>
+  [Run a Docker container](run-docker-container.md) 