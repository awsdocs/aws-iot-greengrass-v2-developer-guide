# Perform machine learning inference<a name="perform-machine-learning-inference"></a>

With AWS IoT Greengrass, you can perform machine learning \(ML\) inference on your edge devices on locally generated data using cloud\-trained models\. You benefit from the low latency and cost savings of running local inference, yet still take advantage of cloud computing power for training models and complex processing\.

AWS IoT Greengrass makes the steps required to perform inference more efficient\. You can train your inference models anywhere and deploy them locally as *machine learning components*\. For example, you can build and train deep\-learning models in [Amazon SageMaker](https://console.aws.amazon.com/sagemaker) or computer vision models in [Amazon Lookout for Vision](https://console.aws.amazon.com/lookoutvision)\. Then, you can store these models in an [Amazon S3](https://console.aws.amazon.com/s3) bucket, so you can use these models as artifacts in your components to perform inference on your core devices\.

**Topics**
+ [How AWS IoT Greengrass ML inference works](#how-ml-inference-works)
+ [What's different in AWS IoT Greengrass Version 2?](#ml-differences)
+ [Requirements](#ml-requirements)
+ [Supported model sources](#ml-model-sources)
+ [Supported machine learning runtimes](#ml-runtime-libraries)
+ [AWS\-provided machine learning components](#ml-components)
+ [Tutorial: Perform sample image classification inference using TensorFlow Lite](ml-tutorial-image-classification.md)
+ [Perform sample image classification inference on images from a camera using TensorFlow Lite](ml-tutorial-image-classification-camera.md)
+ [Use Amazon SageMaker Edge Manager on Greengrass core devices](use-sagemaker-edge-manager.md)
+ [Use Amazon Lookout for Vision on Greengrass core devices](use-lookout-for-vision.md)
+ [Customize your machine learning components](ml-customization.md)
+ [Troubleshooting machine learning inference](ml-troubleshooting.md)

## How AWS IoT Greengrass ML inference works<a name="how-ml-inference-works"></a>

AWS provides [machine learning components](#ml-components) that you can use to create one\-step deployments to perform machine learning inference on your device\. You can also use these components as templates to create custom components to meet your specific requirements\.<a name="ml-component-types"></a>

AWS provides the following categories of machine learning components:
+ **Model component**—Contains machine learning models as Greengrass artifacts\.
+ **Runtime component**—Contains the script that installs the machine learning framework and its dependencies on the Greengrass core device\.
+ **Inference component**—Contains the inference code and includes component dependencies to install the machine learning framework and download pre\-trained machine learning models\.

Each deployment that you create to perform machine learning inference consists of at least one component that runs your inference application, installs the machine learning framework, and downloads your machine learning models\. To perform sample inference with AWS\-provided components, you deploy an inference component to your core device, which automatically includes the corresponding model and runtime components as dependencies\. To customize your deployments, you can plug in or swap out the sample model components with custom model components, or you can use the component recipes for the AWS\-provided components as templates to create your own custom inference, model, and runtime components\. 

To perform machine learning inference by using custom components:

1. Create a model component\. This component contains the machine learning models that you want to use to perform inference\. AWS provides sample pre\-trained DLR and TensorFlow Lite models\. To use a custom model, create your own model component\.

1. Create a runtime component\. This component contains the scripts required to install the machine learning runtime for your models\. AWS provides sample runtime components for [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) \(DLR\) and [TensorFlow Lite](https://www.tensorflow.org/lite/guide/python)\. To use other runtimes with your custom models and inference code, create your own runtime components\.

1. Create an inference component\. This component contains your inference code, and includes your model and runtime components as dependencies\. AWS provides sample inference components for image classification and object detection using DLR and TensorFlow Lite\. To perform other types of inference, or to use custom models and runtimes, create your own inference component\.

1. Deploy the inference component\. When you deploy this component, AWS IoT Greengrass also automatically deploys the model and runtime component dependencies\.

To get started with AWS\-provided components, see [Tutorial: Perform sample image classification inference using TensorFlow Lite](ml-tutorial-image-classification.md)\.

For information about creating custom machine learning components, see [Customize your machine learning components](ml-customization.md)\.

## What's different in AWS IoT Greengrass Version 2?<a name="ml-differences"></a>

AWS IoT Greengrass consolidates functional units for machine learning—such as models, runtimes, and inference code— into components that enable you to use a one\-step process to install the machine learning runtime, download your trained models, and perform inference on your device\. 

By using the AWS\-provided machine learning components, you have the flexibility to start performing machine learning inference with sample inference code and pre\-trained models\. You can plug in custom model components to use your own custom\-trained models with the inference and runtime components that AWS provides\. For a completely customized machine learning solution, you can use the public components as templates to create custom components and use any runtime, model, or inference type that you want\.

## Requirements<a name="ml-requirements"></a>

To create and use machine learning components, you must have the following:
+ A Greengrass core device\. If you don't have one, see [Tutorial: Getting started with AWS IoT Greengrass V2](getting-started.md)\.
+ Minimum 500 MB local storage space to use AWS\-provided sample machine learning components\.

## Supported model sources<a name="ml-model-sources"></a>

AWS IoT Greengrass supports using custom\-trained machine learning models that are stored in Amazon S3\. You can also use Amazon SageMaker edge packaging jobs to directly create model components for your SageMaker Neo\-compiled models\. For information about using SageMaker Edge Manager with AWS IoT Greengrass, see [Use Amazon SageMaker Edge Manager on Greengrass core devices](use-sagemaker-edge-manager.md)\. You can also use Amazon Lookout for Vision model packaging jobs to create model components for your Lookout for Vision models\. For more information about using Lookout for Vision with AWS IoT Greengrass, see [Use Amazon Lookout for Vision on Greengrass core devices](use-lookout-for-vision.md)\.

The S3 buckets that contain your models must meet the following requirements:
+ They must not be encrypted using SSE\-C\. For buckets that use server\-side encryption, AWS IoT Greengrass machine learning inference currently supports the SSE\-S3 or SSE\-KMS encryption options only\. For more information about server\-side encryption options, see [Protecting data using server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) in the *Amazon Simple Storage Service User Guide*\.
+ Their names must not include periods \(`.`\)\. For more information, see the rule about using virtual hosted\-style buckets with SSL in [Rules for bucket naming](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) in the *Amazon Simple Storage Service User Guide*\.
+ <a name="sr-artifacts-req"></a>The S3 buckets that store your model sources must be in the same AWS account and AWS Region as your machine learning components\.
+ AWS IoT Greengrass must have `read` permission to the model source\. To enable AWS IoT Greengrass to access the S3 buckets, the [Greengrass device role](device-service-role.md) must allow the `s3:GetObject` action\. For more information about the device role, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

## Supported machine learning runtimes<a name="ml-runtime-libraries"></a>

AWS IoT Greengrass enables you to create custom components to use any machine learning runtime of your choice to perform machine learning inference with your custom\-trained models\. For information about creating custom machine learning components, see [Customize your machine learning components](ml-customization.md)\.

To make the process of getting started with machine learning more efficient, AWS IoT Greengrass provides sample inference, model, and runtime components that use the following machine learning runtimes: 
+  [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) \(DLR\) v1\.6\.0 and v1\.3\.0
+  [TensorFlow Lite](https://www.tensorflow.org/lite/guide/python) v2\.5\.0 

## AWS\-provided machine learning components<a name="ml-components"></a>

The following table lists the AWS\-provided components used for machine learning\. 

**Note**  <a name="component-nucleus-dependency-update-note"></a>
Several AWS\-provided components depend on specific minor versions of the Greengrass nucleus\. Because of this dependency, you need to update these components when you update the Greengrass nucleus to a new minor version\. For information about the specific versions of the nucleus that each component depends on, see the corresponding component topic\. For more information about updating the nucleus, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| Component | Description | Depends on nucleus | [Component type](develop-greengrass-components.md#component-types) | Supported OS | [Open source](open-source.md) | 
| --- | --- | --- | --- | --- | --- | 
| [Lookout for Vision Edge Agent](lookout-for-vision-edge-agent-component.md) | Deploys the Amazon Lookout for Vision runtime on the Greengrass core device, so you can use computer vision to find defects in industrial products\. | No | Generic | Linux | No | 
| [SageMaker Edge Manager](sagemaker-edge-manager-component.md) | Deploys the Amazon SageMaker Edge Manager agent on the Greengrass core device\. | Yes | Generic | Linux, Windows | No | 
| [DLR image classification](dlr-image-classification-component.md) | Inference component that uses the DLR image classification model store and the DLR runtime component as dependencies to install DLR, download sample image classification models, and perform image classification inference on supported devices\. | Yes | Generic | Linux, Windows | No | 
| [DLR object detection](dlr-object-detection-component.md) | Inference component that uses the DLR object detection model store and the DLR runtime component as dependencies to install DLR, download sample object detection models, and perform object detection inference on supported devices\. | Yes | Generic | Linux, Windows | No | 
| [DLR image classification model store](dlr-image-classification-model-store-component.md) | Model component that contains sample ResNet\-50 image classification models as Greengrass artifacts\. | Yes | Generic | Linux, Windows | No | 
| [DLR object detection model store](dlr-object-detection-model-store-component.md) | Model component that contains sample YOLOv3 object detection models as Greengrass artifacts\. | Yes | Generic | Linux, Windows | No | 
| [DLR runtime](dlr-component.md) | Runtime component that contains an installation script that is used to install DLR and its dependencies on the Greengrass core device\. | Yes | Generic | Linux, Windows | No | 
| [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) | Inference component that uses the TensorFlow Lite image classification model store and the TensorFlow Lite runtime component as dependencies to install TensorFlow Lite, download sample image classification models, and perform image classification inference on supported devices\. | Yes | Generic | Linux, Windows | No | 
| [TensorFlow Lite object detection](tensorflow-lite-object-detection-component.md) | Inference component that uses the TensorFlow Lite object detection model store and the TensorFlow Lite runtime component as dependencies to install TensorFlow Lite, download sample object detection models, and perform object detection inference on supported devices\. | Yes | Generic | Linux, Windows | No | 
| [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md) | Model component that contains a sample MobileNet v1 model as a Greengrass artifact\. | Yes | Generic | Linux, Windows | No | 
| [TensorFlow Lite object detection model store](tensorflow-lite-object-detection-model-store-component.md) | Model component that contains a sample Single Shot Detection \(SSD\) MobileNet model as a Greengrass artifact\. | Yes | Generic | Linux, Windows | No | 
| [TensorFlow Lite runtime](tensorflow-lite-component.md) | Runtime component that contains an installation script that is used to install TensorFlow Lite and its dependencies on the Greengrass core device\. | Yes | Generic | Linux, Windows | No | 