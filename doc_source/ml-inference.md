# Perform machine learning inference<a name="ml-inference"></a>

With AWS IoT Greengrass, you can perform machine learning \(ML\) inference on your edge devices on locally generated data using cloud\-trained models\. You benefit from the low latency and cost savings of running local inference, yet still take advantage of cloud computing power for training models and complex processing\.

AWS IoT Greengrass simplifies the steps required to perform machine learning inference\. You can train your inference models anywhere and deploy them locally as *machine learning components*\. For example, you can build and train deep\-learning models in [SageMaker](https://console.aws.amazon.com/sagemaker) and store them in an Amazon S3 bucket\. You can then use these models as artifacts in your ML components to perform inference on your core devices\.

## How AWS IoT Greengrass ML inference works<a name="how-ml-inference-works"></a>

AWS provides public [machine learning components](ml-components.md) that you can use as\-is to create one\-click deployments to perform machine learning inference on your device\. You can also use these components as templates to create customized private components to meet your specific requirements\. ML components are broadly categorized as follows:
+ Inference components
+ Model components
+ Runtime components

Each deployment that you create to perform machine learning inference consists of at least one inference component that uses a model component and optionally, a runtime component, as dependencies\. To use these components as they are provided, you deploy an AWS\-provided inference component to your core device, which automatically includes model and runtime dependencies as needed\. To customize your deployments, you can plug in or swap out public components as needed, or you can use the component recipes for the public components as templates to create your own private components\. At a high level, you complete the following basic steps to perform machine learning inference using custom components:

1. Configure a model component\. This component contains the machine learning models that you want to use to perform inference\. You can use the AWS\-provided sample model components, or create your own private model component\.

1. Configure an inference component\. This component contains your inference code\. You can use the AWS\-provided sample inference components for image classification or object detection, or you can create your own private inference component\.

1. Create a runtime component, if needed\. This component contains any scripts required to install your ML runtimes\. AWS provides a sample runtime DLR Installer component that installs Neo\-AI\-DLR 1\.3\.0 runtime and its dependencies that are required for the public inference components\. To use other runtimes with your customized private inference components, you must create your own private runtime components\.

1. Deploy the inference component\.

To get started with AWS\-provided components, see the following tutorials:
+ [Tutorial: Perform image classification](ml-tutorial-image-classification.md)
+ [Tutorial: Perform object detection](ml-tutorial-object-detection.md)

For more information about creating custom machine learning components, see [Customize your machine learning components](ml-customization.md)\.

## What's different in Version 2?<a name="ml-differences"></a>

AWS IoT Greengrass takes advantage of components to consolidate multiple functional units for machine learning—such as models, runtimes, and inference code—and provide you with the flexibility to use public components\. You can also plug in your customized private components to use your own inference code to perform machine learning inference\. You can use a one\-click process to install runtimes, deploy your inference code with provided ML models, and perform inference on your device\. You can also select the runtime version, model version, inference type, and even use the public components as templates for private components that use your own inference code or model\. For more information about components, see [Manage AWS IoT Greengrass components](manage-components.md)\.

## Supported model sources<a name="ml-model-sources"></a>

AWS IoT Greengrass supports using Amazon S3 model sources in machine learning components\.

The following requirements apply to model sources:
+ S3 buckets that store your model sources must not be encrypted using SSE\-C\. For buckets that use server\-side encryption, AWS IoT Greengrass ML inference currently supports the SSE\-S3 or SSE\-KMS encryption options only\. For more information about server\-side encryption options, see [Protecting data using server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) in the *Amazon Simple Storage Service Developer Guide*\.
+ The names of S3 buckets that store your Amazon S3 model sources must not include periods \(`.`\)\. For more information, see the rule about using virtual hosted\-style buckets with SSL in [Rules for bucket naming](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) in the *Amazon Simple Storage Service Developer Guide*\.
+ AWS IoT Greengrass must have `read` permission to the model source\.

## Supported runtimes<a name="ml-runtime-libraries"></a>

To perform inference on a Greengrass core device, you must install the machine learning runtime or library for your ML model type\. The machine learning components provided in AWS IoT Greengrass use the [Deep Learning Runtime \(DLR\) 1\.3\.0](https://neo-ai-dlr.readthedocs.io/en/latest/install.html) runtime included in the DLR Installer component\. <a name="dlr-supported-platforms"></a>

The public machine learning inference components require a Greengrass core device running one of the following supported platforms\. For more information see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.
+ **32\-bit Armv7l**

  [Raspberry Pi](https://www.raspberrypi.org) running Raspberry Pi OS, 2020\-08\-24\.
+ **64\-bit x86\_64**

  Device running Amazon Linux 2 or Ubuntu 18\.04, with [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later\.

To use other runtimes and libraries, you must create customized components to install and use those runtimes\. 