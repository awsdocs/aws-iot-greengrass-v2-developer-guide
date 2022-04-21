# DLR image classification<a name="dlr-image-classification-component"></a>

The DLR image classification component \(`aws.greengrass.DLRImageClassification`\) contains sample inference code to perform image classification inference using [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) and resnet\-50 models\. This component uses the variant [DLR image classification model store](dlr-image-classification-model-store-component.md) and the [DLR runtime](dlr-component.md) components as dependencies to download DLR and the sample models\. 

To use this inference component with a custom\-trained DLR model, [create a custom version](ml-customization.md#override-public-model-store) of the dependent model store component\. To use your own custom inference code, you can use the recipe of this component as a template to [create a custom inference component](ml-customization.md#create-inference-component)\.

**Topics**
+ [Versions](#dlr-image-classification-component-versions)
+ [Type](#dlr-image-classification-component-type)
+ [Operating system](#dlr-image-classification-component-os-support)
+ [Requirements](#dlr-image-classification-component-requirements)
+ [Dependencies](#dlr-image-classification-component-dependencies)
+ [Configuration](#dlr-image-classification-component-configuration)
+ [Local log file](#dlr-image-classification-component-log-file)
+ [Changelog](#dlr-image-classification-component-changelog)

## Versions<a name="dlr-image-classification-component-versions"></a>

This component has the following versions:
+ 2\.1\.x
+ 2\.0\.x

## Type<a name="dlr-image-classification-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="dlr-image-classification-component-os-support"></a>

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

## Requirements<a name="dlr-image-classification-component-requirements"></a>

This component has the following requirements:<a name="ml-component-requirements"></a>
+ <a name="ml-req-glibc"></a>On Greengrass core devices running Amazon Linux 2 or Ubuntu 18\.04, [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later installed on the device\.
+ On Armv7l devices, such as Raspberry Pi, dependencies for OpenCV\-Python installed on the device\. Run the following command to install the dependencies\.

  ```
  sudo apt-get install libopenjp2-7 libilmbase23 libopenexr-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0 libwebp-dev
  ```
+ On Raspberry Pi devices, OpenCV\-Python installed on the device\. Run the following command to install OpenCV\-Python\.

  ```
  pip3 install opencv-python
  ```

## Dependencies<a name="dlr-image-classification-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#dlr-image-classification-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.1\.6 ]

The following table lists the dependencies for version 2\.1\.6 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.6\.0 | Soft | 
| [DLR image classification model store](dlr-image-classification-model-store-component.md) | \~2\.1\.0 | Hard | 
| [DLR](dlr-component.md) | \~1\.6\.0 | Hard | 

------
#### [ 2\.1\.4 \- 2\.1\.5 ]

The following table lists the dependencies for versions 2\.1\.4 to 2\.1\.5 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.5\.0 | Soft | 
| [DLR image classification model store](dlr-image-classification-model-store-component.md) | \~2\.1\.0 | Hard | 
| [DLR](dlr-component.md) | \~1\.6\.0 | Hard | 

------
#### [ 2\.1\.3 ]

The following table lists the dependencies for version 2\.1\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.4\.0 | Soft | 
| [DLR image classification model store](dlr-image-classification-model-store-component.md) | \~2\.1\.0 | Hard | 
| [DLR](dlr-component.md) | \~1\.6\.0 | Hard | 

------
#### [ 2\.1\.2 ]

The following table lists the dependencies for version 2\.1\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.3\.0 | Soft | 
| [DLR image classification model store](dlr-image-classification-model-store-component.md) | \~2\.1\.0 | Hard | 
| [DLR](dlr-component.md) | \~1\.6\.0 | Hard | 

------
#### [ 2\.1\.1 ]

The following table lists the dependencies for version 2\.1\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.2\.0 | Soft | 
| [DLR image classification model store](dlr-image-classification-model-store-component.md) | \~2\.1\.0 | Hard | 
| [DLR](dlr-component.md) | \~1\.6\.0 | Hard | 

------
#### [ 2\.0\.x ]

The following table lists the dependencies for version 2\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | \~2\.0\.0 | Soft | 
| DLR image classification model store | \~2\.0\.0 | Hard | 
| DLR | \~1\.3\.0 | Soft | 

------

## Configuration<a name="dlr-image-classification-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ 2\.1\.x ]

`accessControl`  
<a name="ml-config-accesscontrol-desc"></a>\(Optional\) The object that contains the [authorization policy](interprocess-communication.md#ipc-authorization-policies) that allows the component to publish messages to the default notifications topic\.   
Default:   

```
{
   "aws.greengrass.ipc.mqttproxy": {
      "aws.greengrass.DLRImageClassification:mqttproxy:1": {
         "policyDescription": "Allows access to publish via topic ml/dlr/image-classification.",
         "operations": [
            "aws.greengrass#PublishToIoTCore"
         ],
         "resources": [
            "ml/dlr/image-classification"
         ]
      }
   }
}
```

`PublishResultsOnTopic`  
<a name="ml-config-publishresultsontopic-desc"></a>\(Optional\) The topic on which you want to publish the inference results\. If you modify this value, then you must also modify the value of `resources` in the `accessControl` parameter to match your custom topic name\.  
Default: `ml/dlr/image-classification`

`Accelerator`  <a name="ml-config-accelerator"></a>
The accelerator that you want to use\. Supported values are `cpu` and `gpu`\.  
The sample models in the dependent model component support only CPU acceleration\. To use GPU acceleration with a different custom model, [create a custom model component](ml-customization.md#override-public-model-store) to override the public model component\.  
Default: `cpu`

`ImageDirectory`  
<a name="ml-config-imagedirectory-desc"></a>\(Optional\) The path of the folder on the device where inference components read images\. You can modify this value to any location on your device to which you have read/write access\.  
<a name="ml-config-imagedirectory-img-default"></a>Default: `/greengrass/v2/packages/artifacts-unarchived/component-name/image_classification/sample_images/`  
If you set the value of `UseCamera` to `true`, then this configuration parameter is ignored\. 

`ImageName`  
<a name="ml-config-imagename-desc"></a>\(Optional\) The name of the image that the inference component uses as an input to a make prediction\. The component looks for the image in the folder specified in `ImageDirectory`\. By default, the component uses the sample image in the default image directory\. AWS IoT Greengrass supports the following image formats: `jpeg`, `jpg`, `png`, and `npy`\.   
<a name="ml-config-imagename-img-default"></a>Default: `cat.jpeg`  
If you set the value of `UseCamera` to `true`, then this configuration parameter is ignored\. 

`InferenceInterval`  <a name="ml-config-inferenceinterval"></a>
\(Optional\) The time in seconds between each prediction made by the inference code\. The sample inference code runs indefinitely and repeats its predictions at the specified time interval\. For example, you can change this to a shorter interval if you want to use images taken by a camera for real\-time prediction\.  
Default: `3600`

`ModelResourceKey`  <a name="ml-config-modelresourcekey"></a>
<a name="ml-config-modelresourcekey-desc"></a>\(Optional\) The models that are used in the dependent public model component\. Modify this parameter only if you override the public model component with a custom component\.   
Default:  

```
{
    "armv7l": "DLR-resnet50-armv7l-cpu-ImageClassification",
    "aarch64": "DLR-resnet50-aarch64-cpu-ImageClassification",
    "x86_64": "DLR-resnet50-x86_64-cpu-ImageClassification",
    "windows": "DLR-resnet50-win-cpu-ImageClassification"
}
```

`UseCamera`  <a name="ml-config-usecamera"></a>
\(Optional\) String value that defines whether to use images from a camera connected to the Greengrass core device\. Supported values are `true` and `false`\.  
When you set this value to `true`, the sample inference code accesses the camera on your device and runs inference locally on the captured image\. The values of the `ImageName` and `ImageDirectory` parameters are ignored\. Make sure that the user running this component has read/write access to the location where the camera stores captured images\.  
Default: `false`  
When you view the recipe of this component, the `UseCamera` configuration parameter doesn't appear in the default configuration\. However, you can modify the value of this parameter in a [configuration merge update](update-component-configurations.md) when you deploy the component\.   
When you set `UseCamera` to `true`, you must also create a symlink to enable the inference component to access your camera from the virtual environment that is created by the runtime component\. For more information about using a camera with the sample inference components, see [Update component configurations](ml-tutorial-image-classification-camera.md)\.

------
#### [ 2\.0\.x ]

`MLRootPath`  <a name="ml-config-mlrootpath"></a>
<a name="ml-config-mlrootpath-desc"></a>\(Optional\) The path of the folder on Linux core devices where inference components read images and write inference results\. You can modify this value to any location on your device to which the user running this component has read/write access\.  
<a name="ml-config-mlrootpath-default-dlr"></a>Default: `/greengrass/v2/work/variant.DLR/greengrass_ml`  
<a name="ml-config-mlrootpath-default-tfl"></a>Default: `/greengrass/v2/work/variant.TensorFlowLite/greengrass_ml`

`Accelerator`  <a name="ml-config-accelerator"></a>
The accelerator that you want to use\. Supported values are `cpu` and `gpu`\.  
The sample models in the dependent model component support only CPU acceleration\. To use GPU acceleration with a different custom model, [create a custom model component](ml-customization.md#override-public-model-store) to override the public model component\.  
Default: `cpu`

`ImageName`  
<a name="ml-config-imagename-desc-dlr-1.3.0"></a>\(Optional\) The name of the image that the inference component uses as an input to a make prediction\. The component looks for the image in the folder specified in `ImageDirectory`\. The default location is `MLRootPath/images`\. AWS IoT Greengrass supports the following image formats: `jpeg`, `jpg`, `png`, and `npy`\.   
<a name="ml-config-imagename-img-default"></a>Default: `cat.jpeg`

`InferenceInterval`  <a name="ml-config-inferenceinterval"></a>
\(Optional\) The time in seconds between each prediction made by the inference code\. The sample inference code runs indefinitely and repeats its predictions at the specified time interval\. For example, you can change this to a shorter interval if you want to use images taken by a camera for real\-time prediction\.  
Default: `3600`

`ModelResourceKey`  <a name="ml-config-modelresourcekey"></a>
<a name="ml-config-modelresourcekey-desc"></a>\(Optional\) The models that are used in the dependent public model component\. Modify this parameter only if you override the public model component with a custom component\.   
Default:  

```
armv7l: "DLR-resnet50-armv7l-cpu-ImageClassification"
x86_64: "DLR-resnet50-x86_64-cpu-ImageClassification"
```

------

## Local log file<a name="dlr-image-classification-component-log-file"></a>

This component uses the following log file\.

------
#### [ Linux ]

```
/greengrass/v2/logs/aws.greengrass.DLRImageClassification.log
```

------
#### [ Windows ]

```
C:\greengrass\v2\logs\aws.greengrass.DLRImageClassification.log
```

------

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux ]

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.DLRImageClassification.log
  ```

------
#### [ Windows \(PowerShell\) ]

  ```
  Get-Content C:\greengrass\v2\logs\aws.greengrass.DLRImageClassification.log -Tail 10 -Wait
  ```

------

## Changelog<a name="dlr-image-classification-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.1\.6  |  Version updated for Greengrass nucleus version 2\.5\.0 release\.  | 
|  2\.1\.5  |  Component released in all AWS Regions\.  | 
|  2\.1\.4  |  Version updated for Greengrass nucleus version 2\.4\.0 release\. This version isn't available in Europe \(London\) \(`eu-west-2`\)\.  | 
|  2\.1\.3  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.1\.2  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.1\.1  |  <a name="changelog-dlr-image-classification-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/dlr-image-classification-component.html)  | 
|  2\.0\.4  |  Initial version\.  | 