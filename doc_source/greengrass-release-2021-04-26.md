# Release: AWS IoT Greengrass Core v2\.1\.0 software update on April 26, 2021<a name="greengrass-release-2021-04-26"></a>

This release provides version 2\.1\.0 of the Greengrass nucleus component and updates AWS\-provided components\.

**Release date:** April 26, 2021

**Release highlights**
+ **Docker Hub and Amazon Elastic Container Registry \(Amazon ECR\) integration**—The new Docker application manager component enables you to download public or private images from Amazon ECR\. You can also use this component to download public images from Docker Hub and AWS Marketplace\. For more information, see [Run a Docker container](run-docker-container.md)\.
+ **Dockerfile and Docker images for AWS IoT Greengrass Core software**—You can use the Greengrass Docker image to run AWS IoT Greengrass in a Docker container that uses Amazon Linux 2 as the base operating system\. You can also use the AWS IoT Greengrass Dockerfile to build your own Greengrass image\. For more information, see [Run AWS IoT Greengrass Core software in a Docker container](run-greengrass-docker.md)\.
+ **Support for additional machine learning frameworks and platforms**—You can deploy sample machine learning inference components that use pre\-trained models to perform sample image classification and object detection using TensorFlow Lite 2\.5\.0 and DLR 1\.6\.0\. This release also extends sample machine learning support for Armv8 \(AArch64\) devices\. For more information, see [Perform machine learning inference](perform-machine-learning-inference.md)\.

**Topics**
+ [Platform support updates](#greengrass-2021-04-26-platforms)
+ [Public component updates](#greengrass-2021-04-26-components)

## Platform support updates<a name="greengrass-2021-04-26-platforms"></a>


| **Platform** | **Details** | 
| --- | --- | 
| Docker |  A Dockerfile and Docker image for AWS IoT Greengrass are now available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html) For more information, see [Run AWS IoT Greengrass Core software in a Docker container](run-greengrass-docker.md)\. | 

## Public component updates<a name="greengrass-2021-04-26-components"></a>

The following table lists AWS\-provided components that include new and updated features\. 

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus | Version 2\.1\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| Docker application manager |  Version 2\.0\.0 of the new [Docker application manager component](docker-application-manager-component.md) is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| Lambda launcher |  Version 2\.0\.4 of the [Lambda launcher component](lambda-launcher-component.md) is available\. <a name="changelog-lambda-launcher-2.0.4"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| Legacy subscription router |  Version 2\.1\.0 of the [legacy subscription router component](legacy-subscription-router-component.md) is available\. <a name="changelog-legacy-subscription-router-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| Local debug console |  Version 2\.1\.0 of the [local debug console component](local-debug-console-component.md) is available\. <a name="changelog-local-debug-console-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| Log manager |  Version 2\.1\.0 of the [log manager component](log-manager-component.md) is available\. <a name="changelog-log-manager-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| DLR image classification | Version 2\.1\.1 of the [DLR image classification](dlr-image-classification-component.md) component is available\. <a name="changelog-dlr-image-classification-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| DLR object detection | Version 2\.1\.1 of the [DLR object detection](dlr-object-detection-component.md) component is available\. <a name="changelog-dlr-object-detection-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| DLR image classification model store | Version 2\.1\.1 of the [DLR image classification model store](dlr-image-classification-model-store-component.md) component is available\. <a name="changelog-dlr-image-classification-model-store-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| DLR object detection model store | Version 2\.1\.1 of the [DLR object detection model store](dlr-object-detection-model-store-component.md) component is available\. <a name="changelog-dlr-object-detection-model-store-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| DLR installer | Version 1\.6\.1 of the [DLR](dlr-component.md) component is available\. <a name="changelog-dlr-1.6.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| TensorFlow Lite image classification | Version 2\.1\.0 of the new [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) component is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| TensorFlow Lite object detection | Version 2\.1\.0 of the new [TensorFlow Lite object detection](tensorflow-lite-object-detection-component.md) component is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| TensorFlow Lite image classification model store | Version 2\.1\.0 of the new [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md) component is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| TensorFlow Lite object detection model store | Version 2\.1\.0 of the new [TensorFlow Lite object detection model store](tensorflow-lite-object-detection-model-store-component.md) component is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 
| TensorFlow Lite | Version 2\.5\.0 of the new [TensorFlow Lite](tensorflow-lite-component.md) component is available\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2021-04-26.html)  | 