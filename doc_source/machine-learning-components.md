# Machine learning components<a name="machine-learning-components"></a>

AWS IoT Greengrass provides the following machine learning components that you can deploy to supported devices to [perform machine learning inference](perform-machine-learning-inference.md) using models trained in Amazon SageMaker or with your own pre\-trained models that are stored in Amazon S3\. <a name="ml-component-types"></a>

AWS\-provided machine learning components are broadly categorized as follows:
+ **Model component**—Contains machine learning models as Greengrass artifacts\.
+ **Runtime component**—Contains the script that installs the machine learning framework and its dependencies on the Greengrass core device\.
+ **Inference component**—Contains the inference code and includes component dependencies to install the machine learning framework and download pre\-trained machine learning models\.

You can use the sample inference code and pre\-trained models in the AWS\-provided machine learning components to perform image classification and object detection using DLR and TensorFlow Lite\. To perform custom machine learning inference with your own models that are stored in Amazon S3, or to use a different machine learning framework, you can use the recipes of these public components as templates to create custom machine learning components\. For more information, see [Customize your machine learning components](ml-customization.md)\.

AWS IoT Greengrass also includes an AWS\-provided component to manage the installation and lifecycle of the SageMaker Edge Manager agent on Greengrass core devices\. With SageMaker Edge Manager, you can use Amazon SageMaker Neo\-compiled models directly on your core device\. For more information, see [Use Amazon SageMaker Edge Manager on Greengrass core devices](use-sagemaker-edge-manager.md)\.

The following table lists the machine learning components that are available in AWS IoT Greengrass\. 

**Note**  <a name="component-nucleus-dependency-update-note"></a>
Several AWS\-provided components depend on specific minor versions of the Greengrass nucleus\. Because of this dependency, you need to update these components when you update the Greengrass nucleus to a new minor version\. For information about the specific versions of the nucleus that each component depends on, see the corresponding component topic\. For more information about updating the nucleus, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| Component | Description | Depends on nucleus | [Component type](develop-greengrass-components.md#component-types) | [Open source](open-source.md) | 
| --- | --- | --- | --- | --- | 
| [SageMaker Edge Manager](sagemaker-edge-manager-component.md) | Deploys the Amazon SageMaker Edge Manager agent on the Greengrass core device\. | Yes | Generic | No | 
| [DLR image classification](dlr-image-classification-component.md) | Inference component that uses the DLR image classification model store and the DLR runtime component as dependencies to install DLR, download sample image classification models, and perform image classification inference on supported devices\. | Yes | Generic | No | 
| [DLR object detection](dlr-object-detection-component.md) | Inference component that uses the DLR object detection model store and the DLR runtime component as dependencies to install DLR, download sample object detection models, and perform object detection inference on supported devices\. | Yes | Generic | No | 
| [DLR image classification model store](dlr-image-classification-model-store-component.md) | Model component that contains sample ResNet\-50 image classification models as Greengrass artifacts\. | Yes | Generic | No | 
| [DLR object detection model store](dlr-object-detection-model-store-component.md) | Model component that contains sample YOLOv3 object detection models as Greengrass artifacts\. | Yes | Generic | No | 
| [DLR installer](dlr-component.md) | Runtime component that contains an installation script that is used to install DLR and its dependencies on the Greengrass core device\. | Yes | Generic | No | 
| [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) | Inference component that uses the TensorFlow Lite image classification model store and the TensorFlow Lite runtime component as dependencies to install TensorFlow Lite, download sample image classification models, and perform image classification inference on supported devices\. | Yes | Generic | No | 
| [TensorFlow Lite object detection](tensorflow-lite-object-detection-component.md) | Inference component that uses the TensorFlow Lite object detection model store and the TensorFlow Lite runtime component as dependencies to install TensorFlow Lite, download sample object detection models, and perform object detection inference on supported devices\. | Yes | Generic | No | 
| [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md) | Model component that contains a sample MobileNet v1 model as a Greengrass artifact\. | Yes | Generic | No | 
| [TensorFlow Lite object detection model store](tensorflow-lite-object-detection-model-store-component.md) | Model component that contains a sample Single Shot Detection \(SSD\) MobileNet model as a Greengrass artifact\. | Yes | Generic | No | 
| [TensorFlow Lite installer](tensorflow-lite-component.md) | Runtime component that contains an installation script that is used to install TensorFlow Lite and its dependencies on the Greengrass core device\. | Yes | Generic | No | 