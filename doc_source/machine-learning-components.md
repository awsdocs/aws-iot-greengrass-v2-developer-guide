# Machine learning components<a name="machine-learning-components"></a>

AWS IoT Greengrass provides machine learning components that you can select and deploy to supported devices to [perform machine learning inference](ml-inference)\. AWS\-provided machine learning components are broadly categorized as follows:<a name="ml-component-types"></a>
+ **Model component**—Contains machine learning models as Greengrass artifacts\.
+ **Runtime component**—Contains the script that installs the machine learning framework and its dependencies on the Greengrass core device\.
+ **Inference component**—Contains the inference code and includes component dependencies to install the machine learning framework and download pre\-trained machine learning models\.

The public machine learning components use sample inference code and pre\-trained models to perform image classification and object detection using [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) \(DLR\) and [TensorFlow Lite](https://www.tensorflow.org/lite)\. To perform custom machine learning inference with your own models, or to use a different machine learning framework, you can use the recipes of these public components as templates to create custom machine learning components\. For more information, see [Customize your machine learning components](ml-customization.md)\.

**Components**
+ [Machine learning components for DLR](#machine-learning-components-dlr)
+ [Machine learning components for TensorFlow Lite](#machine-learning-components-tensorflow-lite)

## Machine learning components for DLR<a name="machine-learning-components-dlr"></a>

AWS IoT Greengrass provides the following public components for performing sample machine learning inference using [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) \(DLR\)\. 


| Component | Description | Depends on nucleus | [Component type](manage-components.md#component-types) | Open source | 
| --- | --- | --- | --- | --- | 
| [DLR image classification](dlr-image-classification-component.md) | Inference component that uses the DLR image classification model store and the DLR runtime component as dependencies to install DLR, download sample image classification models, and perform image classification inference on supported devices\. | Yes | Generic | No | 
| [DLR object detection](dlr-object-detection-component.md) | Inference component that uses the DLR object detection model store and the DLR runtime component as dependencies to install DLR, download sample object detection models, and perform object detection inference on supported devices\. | Yes | Generic | No | 
| [DLR image classification model store](dlr-image-classification-model-store-component.md) | Model component that contains sample ResNet\-50 image classification models as Greengrass artifacts\. | Yes | Generic | No | 
| [DLR object detection model store](dlr-object-detection-model-store-component.md) | Model component that contains sample YOLOv3 object detection models as Greengrass artifacts\. | Yes | Generic | No | 
| [DLR](dlr-component.md) | Runtime component that contains an installation script that is used to install DLR and its dependencies on the Greengrass core device\. | Yes | Generic | No | 

## Machine learning components for TensorFlow Lite<a name="machine-learning-components-tensorflow-lite"></a>

AWS IoT Greengrass provides the following public components for performing sample machine learning inference using [TensorFlow Lite](https://www.tensorflow.org/lite)\.


| Component | Description | Depends on nucleus | [Component type](manage-components.md#component-types) | Open source | 
| --- | --- | --- | --- | --- | 
| [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) | Inference component that uses the TensorFlow Lite image classification model store and the TensorFlow Lite runtime component as dependencies to install TensorFlow Lite, download sample image classification models, and perform image classification inference on supported devices\. | Yes | Generic | No | 
| [TensorFlow Lite object detection](tensorflow-lite-object-detection-component.md) | Inference component that uses the TensorFlow Lite object detection model store and the TensorFlow Lite runtime component as dependencies to install TensorFlow Lite, download sample object detection models, and perform object detection inference on supported devices\. | Yes | Generic | No | 
| [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md) | Model component that contains a sample MobileNet v1 model as a Greengrass artifact\. | Yes | Generic | No | 
| [TensorFlow Lite object detection model store](tensorflow-lite-object-detection-model-store-component.md) | Model component that contains a sample Single Shot Detection \(SSD\) MobileNet model as a Greengrass artifact\. | Yes | Generic | No | 
| [TensorFlow Lite](tensorflow-lite-component.md) | Runtime component that contains an installation script that is used to install TensorFlow Lite and its dependencies on the Greengrass core device\. | Yes | Generic | No | 

**Topics**
+ [Machine learning components for DLR](#machine-learning-components-dlr)
+ [Machine learning components for TensorFlow Lite](#machine-learning-components-tensorflow-lite)
+ [DLR image classification](dlr-image-classification-component.md)
+ [DLR object detection](dlr-object-detection-component.md)
+ [DLR image classification model store](dlr-image-classification-model-store-component.md)
+ [DLR object detection model store](dlr-object-detection-model-store-component.md)
+ [DLR](dlr-component.md)
+ [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md)
+ [TensorFlow Lite object detection](tensorflow-lite-object-detection-component.md)
+ [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md)
+ [TensorFlow Lite object detection model store](tensorflow-lite-object-detection-model-store-component.md)
+ [TensorFlow Lite](tensorflow-lite-component.md)