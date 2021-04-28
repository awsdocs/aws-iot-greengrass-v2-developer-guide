# What's new in AWS IoT Greengrass Version 2<a name="greengrass-v2-whats-new"></a>

AWS IoT Greengrass Version 2 is a new major version release of AWS IoT Greengrass\. The Version 2 release introduces the following features:

**Open source edge runtime**  
The edge runtime is now open source and distributed under the Apache 2\.0 license and available on GitHub\. You can now view the AWS IoT Greengrass edge runtime code, which allows you to troubleshoot interactions with your application and helps you build more reliable and performant applications running on AWS IoT Greengrass\. You can also customize and extend the AWS IoT Greengrass edge runtime to meet your specific hardware and software needs\. For more information, see [Open source AWS IoT Greengrass client software](open-source.md)\.

**Improved modularity**  
You can add or remove pre\-built software components based on your use cases, and your device CPU and memory resources\. For example, you can choose to include only pre\-built AWS IoT Greengrass components, such as stream manager, when you need to process data streams with your application\. Or, you can include only machine learning components when you want to perform machine learning inference locally on your devices\. For more information, see [Manage AWS IoT Greengrass components](manage-components.md) and [AWS\-provided components](public-components.md)\.

**New local development tools**  
AWS IoT Greengrass includes a new command line interface \(CLI\) that enables you to locally develop and debug applications on your device\. In addition, the new local debug console helps you visually debug applications on your device\. With these new capabilities, you can develop and debug code on a test device before using the cloud to deploy to your production devices\. For more information, see [Greengrass CLI](greengrass-cli-component.md) and [Local debug console](local-debug-console-component.md)\.

**Improved fleet deployment features**  
AWS IoT Greengrass is now integrated with AWS IoT thing groups\. This enables you to organize your devices in groups and manage application deployments across your devices with features that control rollout rates, timeouts, and rollbacks\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

## AWS IoT Greengrass V2 release notes<a name="greengrass-release-notes"></a>

AWS IoT Greengrass release notes provide details about AWS IoT Greengrass releases—new features, updates and improvements, and general fixes\. AWS IoT Greengrass has the following types of releases:
+ New feature releases for AWS IoT Greengrass
+ AWS IoT Greengrass Core software updates

This section contains all of the AWS IoT Greengrass V2 release notes, latest first, and includes major feature changes and significant bug fixes\. For information about additional minor fixes, see the [aws\-greengrass](https://github.com/aws-greengrass) organization on GitHub\.

**Topics**
+ [Release: AWS IoT Greengrass Core software update on April 26, 2021](#greengrass-release-2021-04-26)
+ [Release: AWS IoT Greengrass Core software update on March 09, 2021](#greengrass-release-2021-03-09)
+ [Release: AWS IoT Greengrass Core software update on February 04, 2021](#greengrass-release-2021-02-04)

### Release: AWS IoT Greengrass Core software update on April 26, 2021<a name="greengrass-release-2021-04-26"></a>

This release provides version 2\.1\.0 of the Greengrass nucleus component and updates AWS\-provided components\. It includes a new component that you can use to download Docker images from private Amazon ECR repositories, and new sample components to perform machine learning inference using TensorFlow Lite\.

**Release date:** April 26, 2021



#### Public component changes<a name="greengrass-2021-04-26-components"></a>

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all component dependencies for that component\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus | Version 2\.1\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| Lambda launcher |  Version 2\.0\.4 of the [Lambda launcher component](lambda-launcher-component.md) is available\. <a name="changelog-lambda-launcher-2.0.4"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| Legacy subscription router |  Version 2\.1\.0 of the [legacy subscription router component](legacy-subscription-router-component.md) is available\. <a name="changelog-legacy-subscription-router-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| Local debug console |  Version 2\.1\.0 of the [local debug console component](local-debug-console-component.md) is available\. <a name="changelog-local-debug-console-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| Log manager |  Version 2\.1\.0 of the [log manager component](log-manager-component.md) is available\. <a name="changelog-log-manager-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| DLR image classification | Version 2\.1\.1 of the [DLR image classification](dlr-image-classification-component.md) component is available\. <a name="changelog-dlr-image-classification-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| DLR object detection | Version 2\.1\.1 of the [DLR object detection](dlr-object-detection-component.md) component is available\. <a name="changelog-dlr-object-detection-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| DLR image classification model store | Version 2\.1\.1 of the [DLR image classification model store](dlr-image-classification-model-store-component.md) component is available\. <a name="changelog-dlr-image-classification-model-store-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| DLR object detection model store | Version 2\.1\.1 of the [DLR object detection model store](dlr-object-detection-model-store-component.md) component is available\. <a name="changelog-dlr-object-detection-model-store-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| DLR | Version 1\.6\.1 of the [DLR](dlr-component.md) component is available\. <a name="changelog-dlr-1.6.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 
| TensorFlow Lite image classification | Version 2\.1\.0 of the [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) component is available\. This component adds support for sample image classification inference using [TensorFlow Lite](https://www.tensorflow.org/lite)\.  | 
| TensorFlow Lite object detection | Version 2\.1\.0 of the [TensorFlow Lite object detection](tensorflow-lite-object-detection-component.md) component is available\. This component adds support for sample object detection inference using [TensorFlow Lite](https://www.tensorflow.org/lite)\.  | 
| TensorFlow Lite image classification model store | Version 2\.1\.0 of the [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md) component is available\. This component includes a pre\-trained MobileNet v1 quantized model for sample image classification inference\.  | 
| TensorFlow Lite object detection model store | Version 2\.1\.0 of the [TensorFlow Lite object detection model store](tensorflow-lite-object-detection-model-store-component.md) component is available\. This component includes a pre\-trained Single Shot Detection \(SSD\) MobileNet model trained on the COCO dataset for sample object detection inference\.  | 
| TensorFlow Lite | Version 2\.5\.0 of the [TensorFlow Lite](tensorflow-lite-component.md) component is available\. This component installs [TensorFlow Lite](https://www.tensorflow.org/lite) v2\.5\.0 and its dependencies in a virtual environment on the Greengrass core device\.  | 
| Docker application manager |  Version 2\.0\.0 of the new [Docker application manager component](docker-application-manager-component.md) is available\. This component enables AWS IoT Greengrass to download Docker images from public image registries\. It also enables AWS IoT Greengrass to manage credentials to download images from private repositories in Amazon Elastic Container Registry \(Amazon ECR\)\.   | 

### Release: AWS IoT Greengrass Core software update on March 09, 2021<a name="greengrass-release-2021-03-09"></a>

This release provides version 2\.0\.5 of the Greengrass nucleus component\. It fixes an issue with network proxy support and an issue with the Greengrass data plane endpoint in AWS China Regions\.

**Release date:** March 09, 2021



#### Public component changes<a name="greengrass-2021-03-09-components"></a>

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all component dependencies for that component\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus | Version 2\.0\.5 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.0.5"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 

### Release: AWS IoT Greengrass Core software update on February 04, 2021<a name="greengrass-release-2021-02-04"></a>

This release provides version 2\.0\.4 of the Greengrass nucleus component\. It includes the new `greengrassDataPlanePort` parameter to configure HTTPS communication over port 443 and fixes bugs\. The minimal IAM policy now requires the `iam:GetPolicy` and `sts:GetCallerIdentity` when the AWS IoT Greengrass Core software installer is run with `--provision true`\.

**Release date:** February 04, 2021



#### Public component changes<a name="greengrass-2021-02-04-components"></a>

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all component dependencies for that component\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus | Version 2\.0\.4 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.0.4"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-v2-whats-new.html)  | 