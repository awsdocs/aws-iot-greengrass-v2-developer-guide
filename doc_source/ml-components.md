# Public machine learning components<a name="ml-components"></a>

The machine learning components described in this section are published in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) as public components that you can select and deploy to supported devices\.

## Public inference components<a name="ml-inference-components"></a>

AWS IoT Greengrass includes the following public sample inference components\.


| Component | Description | Component name | 
| --- | --- | --- | 
| [DLR Image Classification](dlr-image-classification-component.md) | Inference component that uses the variant\.DLR and variant\.ImageClassification\.ModelStore components to install DLR and its dependencies, download sample image classification ML models, and perform image classification inference on supported devices\. | aws\.greengrass\.DLRImageClassification | 
| [DLR Object Detection](dlr-object-detection-component.md) | Inference component that uses the variant\.DLR and variant\.ObjectDetection\.ModelStore components to install DLR and its dependencies, download sample object detection ML models, and perform object detection inference on supported devices\. | aws\.greengrass\.DLRObjectDetection | 

## Public model components<a name="ml-model-components"></a>

AWS IoT Greengrass includes the following public sample model components, also called variant components\. You can create new private versions of variant components to use your own models instead of the sample models contained in the public components\.


| Component | Description | Component name | 
| --- | --- | --- | 
| [Image Classification Model Store](variant-image-classification-component.md) | Data component that contains [sample `resnet-50` models](https://cv.gluon.ai/model_zoo/classification.html#resnet) as Greengrass artifacts\. | variant\.ImageClassification\.ModelStore | 
| [Object Detection Model Store](variant-object-detection-component.md) | Data component that contains [sample `yolo3` models](https://cv.gluon.ai/model_zoo/detection.html#yolo-v3) as Greengrass artifacts\. | variant\.ObjectDetection\.ModelStore | 

## Public runtime components<a name="ml-runtime-components"></a>

AWS IoT Greengrass includes the following public sample runtime components, also called variant components\. You can create new private versions of variant components to use your own runtime installation scripts instead of the sample installation script contained in the public components\.


| Component | Description | Component name | 
| --- | --- | --- | 
| [DLR Installer](variant-dlr-component.md) | Data component that contains a DLR installation script as a Greengrass artifact\.  | variant\.DLR | 