# TensorFlow Lite image classification model store<a name="tensorflow-lite-image-classification-model-store-component"></a>

The TensorFlow Lite image classification model store \(`variant.TensorFlowLite.ImageClassification.ModelStore`\) is a machine learning model component that contains a pre\-trained MobileNet v1 model as a Greengrass artifact\. The sample model used in this component is fetched from the [TensorFlow Hub](https://tfhub.dev/) and implemented using [TensorFlow Lite](https://www.tensorflow.org/lite/guide/python)\.

The [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) inference component uses this component as a dependency for the model source\. To use a custom\-trained TensorFlow Lite model, [create a custom version](ml-customization.md#override-public-model-store) of this model component, and include your custom model as a component artifact\. You can use the recipe of this component as a template to create custom model components\. 

**Topics**
+ [Versions](#tensorflow-lite-image-classification-model-store-component-versions)
+ [Type](#tensorflow-lite-image-classification-model-store-component-type)
+ [Requirements](#tensorflow-lite-image-classification-model-store-component-requirements)
+ [Dependencies](#tensorflow-lite-image-classification-model-store-component-dependencies)
+ [Configuration](#tensorflow-lite-image-classification-model-store-component-configuration)
+ [Local log file](#tensorflow-lite-image-classification-model-store-component-log-file)
+ [Changelog](#tensorflow-lite-image-classification-model-store-component-changelog)

## Versions<a name="tensorflow-lite-image-classification-model-store-component-versions"></a>

This component has the following versions:
+ 2\.1\.x

## Type<a name="tensorflow-lite-image-classification-model-store-component-type"></a>

<a name="public-component-type-generic"></a>This component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](manage-components.md#component-types)\.

## Requirements<a name="tensorflow-lite-image-classification-model-store-component-requirements"></a>

This component has the following requirements:<a name="ml-component-requirements"></a>
+ <a name="ml-req-glibc"></a>On Greengrass core devices running Amazon Linux 2 or Ubuntu 18\.04, [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later installed on the device\.
+ On Armv7l devices, such as Raspberry Pi, dependencies for OpenCV\-Python installed on the device\. Run the following command to install the dependencies: 

  ```
  sudo apt-get install libopenjp2-7 libilmbase23 libopenexr-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0 libwebp-dev
  ```

## Dependencies<a name="tensorflow-lite-image-classification-model-store-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#tensorflow-lite-image-classification-model-store-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.1\.3 ]

The following table lists the dependencies for version 2\.1\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.5\.0 | Soft | 

------
#### [ 2\.1\.2 ]

The following table lists the dependencies for version 2\.1\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.4\.0 | Soft | 

------
#### [ 2\.1\.1 ]

The following table lists the dependencies for version 2\.1\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.3\.0 | Soft | 

------
#### [ 2\.1\.0 ]

The following table lists the dependencies for version 2\.1\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.2\.0 | Soft | 

------

## Configuration<a name="tensorflow-lite-image-classification-model-store-component-configuration"></a>

This component doesn't have any configuration parameters\.

## Local log file<a name="tensorflow-lite-image-classification-model-store-component-log-file"></a>

This component doesn't output logs\.

## Changelog<a name="tensorflow-lite-image-classification-model-store-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  Version  |  Changes  | 
| --- | --- | 
|  2\.1\.3  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.1\.2  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.1\.1  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.1\.0  |  Initial version\.  | 