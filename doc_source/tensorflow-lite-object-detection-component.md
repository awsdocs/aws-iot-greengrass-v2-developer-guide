# TensorFlow Lite object detection<a name="tensorflow-lite-object-detection-component"></a>

The TensorFlow Lite object detection component \(`aws.greengrass.TensorFlowLiteObjectDetection`\) contains sample inference code to perform object detection inference using [TensorFlow Lite](https://www.tensorflow.org/lite/guide/python) and a sample pre\-trained Single Shot Detection \(SSD\) MobileNet 1\.0 model\. This component uses the variant [TensorFlow Lite object detection model store](tensorflow-lite-object-detection-model-store-component.md) and the [TensorFlow Lite installer](tensorflow-lite-component.md) components as dependencies to download TensorFlow Lite and the sample model\. 

To use this inference component with a custom\-trained TensorFlow Lite model, you can [create a custom version](ml-customization.md#override-public-model-store) of the dependent model store component\. To use your own custom inference code, use the recipe of this component as a template to [create a custom inference component](ml-customization.md#create-inference-component)\.

**Topics**
+ [Versions](#tensorflow-lite-object-detection-component-versions)
+ [Type](#tensorflow-lite-object-detection-component-type)
+ [Requirements](#tensorflow-lite-object-detection-component-requirements)
+ [Dependencies](#tensorflow-lite-object-detection-component-dependencies)
+ [Configuration](#tensorflow-lite-object-detection-component-configuration)
+ [Local log file](#tensorflow-lite-object-detection-component-log-file)
+ [Changelog](#tensorflow-lite-object-detection-component-changelog)

## Versions<a name="tensorflow-lite-object-detection-component-versions"></a>

This component has the following versions:
+ 2\.1\.x

## Type<a name="tensorflow-lite-object-detection-component-type"></a>

<a name="public-component-type-generic"></a>This component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Requirements<a name="tensorflow-lite-object-detection-component-requirements"></a>

This component has the following requirements:<a name="ml-component-requirements"></a>
+ <a name="ml-req-glibc"></a>On Greengrass core devices running Amazon Linux 2 or Ubuntu 18\.04, [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later installed on the device\.
+ On Armv7l devices, such as Raspberry Pi, dependencies for OpenCV\-Python installed on the device\. Run the following command to install the dependencies: 

  ```
  sudo apt-get install libopenjp2-7 libilmbase23 libopenexr-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0 libwebp-dev
  ```

## Dependencies<a name="tensorflow-lite-object-detection-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#tensorflow-lite-object-detection-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.1\.3 ]

The following table lists the dependencies for version 2\.1\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.5\.0 | Soft | 
| [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md) | >=2\.1\.0 <2\.2\.0 | Hard | 
| [TensorFlow Lite](tensorflow-lite-component.md) | >=2\.5\.0 <2\.6\.0 | Hard | 

------
#### [ 2\.1\.2 ]

The following table lists the dependencies for version 2\.1\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.4\.0 | Soft | 
| [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md) | >=2\.1\.0 <2\.2\.0 | Hard | 
| [TensorFlow Lite](tensorflow-lite-component.md) | >=2\.5\.0 <2\.6\.0 | Hard | 

------
#### [ 2\.1\.1 ]

The following table lists the dependencies for version 2\.1\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.3\.0 | Soft | 
| [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md) | >=2\.1\.0 <2\.2\.0 | Hard | 
| [TensorFlow Lite](tensorflow-lite-component.md) | >=2\.5\.0 <2\.6\.0 | Hard | 

------
#### [ 2\.1\.0 ]

The following table lists the dependencies for version 2\.1\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.2\.0 | Soft | 
| [TensorFlow Lite image classification model store](tensorflow-lite-image-classification-model-store-component.md) | >=2\.1\.0 <2\.2\.0 | Hard | 
| [TensorFlow Lite](tensorflow-lite-component.md) | >=2\.5\.0 <2\.6\.0 | Hard | 

------

## Configuration<a name="tensorflow-lite-object-detection-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`accessControl`  
<a name="ml-config-accesscontrol-desc"></a>\(Optional\) The object that contains the [authorization policy](interprocess-communication.md#ipc-authorization-policies) that allows the component to publish messages to the default notifications topic\.   
Default:   

```
{
   "aws.greengrass.ipc.mqttproxy": {
      "aws.greengrass.TensorFlowLiteObjectDetection:mqttproxy:1": {
         "policyDescription": "Allows access to publish via topic ml/tflite/object-detection.",
         "operations": [
            "aws.greengrass#PublishToIoTCore"
         ],
         "resources": [
            "ml/tflite/object-detection"
         ]
      }
   }
}
```

`PublishResultsOnTopic`  
<a name="ml-config-publishresultsontopic-desc"></a>\(Optional\) The topic on which you want to publish the inference results\. If you modify this value, then you must also modify the value of `resources` in the `accessControl` parameter to match your custom topic name\.  
Default: `ml/tflite/object-detection`

`Accelerator`  <a name="ml-config-accelerator"></a>
The accelerator that you want to use\. Supported values are `cpu` and `gpu`\.  
The sample models in the dependent model component support only CPU acceleration\. To use GPU acceleration with a different custom model, [create a custom model component](ml-customization.md#override-public-model-store) to override the public model component\.  
Default: `cpu`

`ImageDirectory`  
<a name="ml-config-imagedirectory-desc"></a>\(Optional\) The path of the folder on the device where inference components read images\. You can modify this value to any location on your device to which you have read/write access\.  
<a name="ml-config-imagedirectory-obj-default"></a>Default: `/greengrass/v2/packages/artifacts-unarchived/component-name/object_detection/sample_images/`  
If you set the value of `UseCamera` to `true`, then this configuration parameter is ignored\. 

`ImageName`  
<a name="ml-config-imagename-desc"></a>\(Optional\) The name of the image that the inference component uses as an input to a make prediction\. The component looks for the image in the folder specified in `ImageDirectory`\. By default, the component uses the sample image in the default image directory\. AWS IoT Greengrass supports the following image formats: `jpeg`, `jpg`, `png`, and `npy`\.   
<a name="ml-config-imagename-obj-default"></a>Default: `objects.jpg`  
If you set the value of `UseCamera` to `true`, then this configuration parameter is ignored\. 

`InferenceInterval`  <a name="ml-config-inferenceinterval"></a>
\(Optional\) The time in seconds between each prediction made by the inference code\. The sample inference code runs indefinitely and repeats its predictions at the specified time interval\. For example, you can change this to a shorter interval if you want to use images taken by a camera for real\-time prediction\.  
Default: `3600`

`ModelResourceKey`  <a name="ml-config-modelresourcekey"></a>
<a name="ml-config-modelresourcekey-desc"></a>\(Optional\) The models that are used in the dependent public model component\. Modify this parameter only if you override the public model component with a custom component\.   
Default:  

```
{
    model: "TensorFlowLite-SSD"
}
```

`UseCamera`  <a name="ml-config-usecamera"></a>
\(Optional\) String value that defines whether to use images from a camera connected to the Greengrass core device\. Supported values are `true` and `false`\.  
When you set this value to `true`, the sample inference code accesses the camera on your device and runs inference locally on the captured image\. The values of the `ImageName` and `ImageDirectory` parameters are ignored\. Make sure that the user running this component has read/write access to the location where the camera stores captured images\.  
Default: `false`  
When you view the recipe of this component, the `UseCamera` configuration parameter doesn't appear in the default configuration\. However, you can modify the value of this parameter in a [configuration merge update](update-component-configurations.md) when you deploy the component\.   
When you set `UseCamera` to `true`, you must also create a symlink to enable the inference component to access your camera from the virtual environment that is created by the runtime component\. For more information about using a camera with the sample inference components, see [Update component configurations](ml-tutorial-image-classification-camera.md)\.

**Note**  <a name="ml-config-not-visible-note"></a>
When you view the recipe of this component, the `UseCamera` configuration parameter doesn't appear in the default configuration\. However, you can modify the value of this parameter in a [configuration merge update](update-component-configurations.md) when you deploy the component\.   
When you set `UseCamera` to `true`, you must also create a symlink to enable the inference component to access your camera from the virtual environment that is created by the runtime component\. For more information about using a camera with the sample inference components, see [Update component configurations](ml-tutorial-image-classification-camera.md)\.

## Local log file<a name="tensorflow-lite-object-detection-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/aws.greengrass.TensorFlowLiteObjectDetection.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.TensorFlowLiteObjectDetection.log
  ```

## Changelog<a name="tensorflow-lite-object-detection-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  Version  |  Changes  | 
| --- | --- | 
|  2\.1\.4  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.1\.3  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.1\.2  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.1\.1  |  <a name="changelog-tensorflow-lite-object-detection-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/tensorflow-lite-object-detection-component.html)  | 
|  2\.1\.0  |  Initial version\.  | 