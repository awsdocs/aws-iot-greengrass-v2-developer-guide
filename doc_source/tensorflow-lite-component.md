# TensorFlow Lite<a name="tensorflow-lite-component"></a>

The TensorFlow Lite component \(`variant.TensorFlowLite`\) contains a script that installs [TensorFlow Lite](https://www.tensorflow.org/lite) version 2\.5\.0 and its dependencies in a virtual environment on your device\. The [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) and [TensorFlow Lite object detection](tensorflow-lite-object-detection-component.md) component use this runtime component as a dependency for installing TensorFlow Lite\. 

To use a different runtime, you can use the recipe of this component as a template to [create a custom runtime component](ml-customization.md)\. 

**Topics**
+ [Versions](#tensorflow-lite-component-versions)
+ [Type](#tensorflow-lite-component-type)
+ [Requirements](#tensorflow-lite-component-requirements)
+ [Dependencies](#tensorflow-lite-component-dependencies)
+ [Configuration](#tensorflow-lite-component-config)
+ [Usage](#tensorflow-lite-component-usage)
+ [Changelog](#tensorflow-lite-component-changelog)

## Versions<a name="tensorflow-lite-component-versions"></a>

This component has the following versions:
+ 2\.5\.x

## Type<a name="tensorflow-lite-component-type"></a>

<a name="public-component-type-generic"></a>This component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](manage-components.md#component-types)\.

## Requirements<a name="tensorflow-lite-component-requirements"></a>

This component has the following requirements:<a name="ml-component-requirements"></a>
+ <a name="ml-req-glibc"></a>On Greengrass core devices running Amazon Linux 2 or Ubuntu 18\.04, [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later installed on the device\.
+ On Armv7l devices, such as Raspberry Pi, dependencies for OpenCV Python installed on the device\. Run the following command to install the dependencies: 

  ```
  sudo apt-get install libopenjp2-7 libilmbase23 libopenexr-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0 libwebp-dev
  ```

## Dependencies<a name="tensorflow-lite-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#tensorflow-lite-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.5\.3 ]

The following table lists the dependencies for version 2\.5\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.5\.0 | Soft | 

------
#### [ 2\.5\.2 ]

The following table lists the dependencies for version 2\.5\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.4\.0 | Soft | 

------
#### [ 2\.5\.1 ]

The following table lists the dependencies for version 2\.5\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.3\.0 | Soft | 

------
#### [ 2\.5\.0 ]

The following table lists the dependencies for version 2\.5\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.2\.0 | Soft | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="tensorflow-lite-component-config"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`MLRootPath`  
<a name="ml-config-mlrootpath-desc"></a>\(Optional\) The path of the folder on the device where inference components read images and write inference results\. You can modify this value to any location on your device to which the user running this component has read/write access\.  
Default: `/greengrass/v2/work/variant.TensorFlowLite/greengrass_ml`

## Usage<a name="tensorflow-lite-component-usage"></a>

The TensorFlow Lite component sets up a virtual environment on your device that includes the OpenCV and NumPy libraries that are required for TensorFlow Lite\. 

**Note**  
The installer script in this component also installs the latest versions of additional system libraries that are required to configure the virtual environment and to run DLR on your device\. This might upgrade the existing system libraries on your device\. Review the following table for the list of libraries that this component installs for each supported operating system\.


| Operating system | Libraries installed on the device system | Libraries installed in the virtual environment | 
| --- | --- | --- | 
| Raspberry Pi OS | build\-essential, cmake, ca\-certificates, git | setuptools, wheel | 
| Amazon Linux 2 | mesa\-libGL | None | 
| Ubuntu | wget | None | 

When you deploy your inference component, this runtime component first verifies if your device already has TensorFlow Lite and its dependencies installed\. If not, then the runtime component installs them for you\. 

## Changelog<a name="tensorflow-lite-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  Version  |  Changes  | 
| --- | --- | 
|  2\.5\.3  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.5\.2  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.5\.1  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.5\.0  |  Initial version\.  | 