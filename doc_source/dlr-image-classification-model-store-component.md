# DLR image classification model store<a name="dlr-image-classification-model-store-component"></a>

The DLR image classification model store is a machine learning model component that contains pre\-trained ResNet\-50 models as Greengrass artifacts\. The pre\-trained models used in this component are fetched from the [GluonCV Model Zoo](https://cv.gluon.ai/model_zoo/index.html) and are compiled using SageMaker Neo [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr)\.

The [DLR image classification](dlr-image-classification-component.md) inference component uses this component as a dependency for the model source\. To use a custom\-trained DLR model, [create a custom version](ml-customization.md#override-public-model-store) of this model component, and include your custom model as a component artifact\. You can use the recipe of this component as a template to create custom model components\. 

**Note**  
The name of the DLR image classification model store component varies depending on its version\. The component name for version 2\.1\.x and later versions is `variant.DLR.ImageClassification.ModelStore`\. The component name for version 2\.0\.x is `variant.ImageClassification.ModelStore`\.

**Topics**
+ [Versions](#dlr-image-classification-model-store-component-versions)
+ [Type](#dlr-image-classification-model-store-component-type)
+ [Operating system](#dlr-image-classification-model-store-component-os-support)
+ [Requirements](#dlr-image-classification-model-store-component-requirements)
+ [Dependencies](#dlr-image-classification-model-store-component-dependencies)
+ [Configuration](#dlr-image-classification-model-store-component-configuration)
+ [Local log file](#dlr-image-classification-model-store-component-log-file)
+ [Changelog](#dlr-image-classification-model-store-component-changelog)

## Versions<a name="dlr-image-classification-model-store-component-versions"></a>

This component has the following versions:
+ 2\.1\.x \(`variant.DLR.ImageClassification.ModelStore`\) 
+ 2\.0\.x \(`variant.ImageClassification.ModelStore`\) 

## Type<a name="dlr-image-classification-model-store-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="dlr-image-classification-model-store-component-os-support"></a>

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

## Requirements<a name="dlr-image-classification-model-store-component-requirements"></a>

This component has the following requirements:<a name="ml-component-requirements"></a>
+ On Greengrass core devices running Amazon Linux 2 or Ubuntu 18\.04, [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later installed on the device\.
+ On Armv7l devices, such as Raspberry Pi, dependencies for OpenCV\-Python installed on the device\. Run the following command to install the dependencies\.

  ```
  sudo apt-get install libopenjp2-7 libilmbase23 libopenexr-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0 libwebp-dev
  ```
+ Raspberry Pi devices that run Raspberry Pi OS Bullseye must meet the following requirements:
  + NumPy 1\.22\.4 or later installed on the device\. Raspberry Pi OS Bullseye includes an earlier version of NumPy, so you can run the following command to upgrade NumPy on the device\.

    ```
    pip3 install --upgrade numpy
    ```
  + The legacy camera stack enabled on the device\. Raspberry Pi OS Bullseye includes a new camera stack that is enabled by default and isn't compatible, so you must enable the legacy camera stack\.<a name="raspberry-pi-bullseye-enable-legacy-camera-stack"></a>

**To enable the legacy camera stack**

    1. Run the following command to open the Raspberry Pi configuration tool\.

       ```
       sudo raspi-config
       ```

    1. Select **Interface Options**\.

    1. Select **Legacy camera** to enable the legacy camera stack\.

    1. Reboot the Raspberry Pi\.

## Dependencies<a name="dlr-image-classification-model-store-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#dlr-image-classification-model-store-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.1\.9 ]

The following table lists the dependencies for version 2\.1\.9 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <3\.0\.0 | Soft | 

------
#### [ 2\.1\.8 ]

The following table lists the dependencies for version 2\.1\.8 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.9\.0 | Soft | 

------
#### [ 2\.1\.7 ]

The following table lists the dependencies for version 2\.1\.7 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.8\.0 | Soft | 

------
#### [ 2\.1\.6 ]

The following table lists the dependencies for version 2\.1\.6 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.7\.0 | Soft | 

------
#### [ 2\.1\.5 ]

The following table lists the dependencies for version 2\.1\.5 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.6\.0 | Soft | 

------
#### [ 2\.1\.4 ]

The following table lists the dependencies for version 2\.1\.4 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.5\.0 | Soft | 

------
#### [ 2\.1\.3 ]

The following table lists the dependencies for version 2\.1\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.4\.0 | Soft | 

------
#### [ 2\.1\.2 ]

The following table lists the dependencies for version 2\.1\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.3\.0 | Soft | 

------
#### [ 2\.1\.1 ]

The following table lists the dependencies for version 2\.1\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.2\.0 | Soft | 

------
#### [ 2\.0\.x ]

The following table lists the dependencies for version 2\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | \~2\.0\.0 | Soft | 

------

## Configuration<a name="dlr-image-classification-model-store-component-configuration"></a>

This component doesn't have any configuration parameters\.

## Local log file<a name="dlr-image-classification-model-store-component-log-file"></a>

This component doesn't output logs\.

## Changelog<a name="dlr-image-classification-model-store-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.1\.9  | Version updated for Greengrass nucleus version 2\.9\.0 release\. | 
|  2\.1\.8  | Version updated for Greengrass nucleus version 2\.8\.0 release\. | 
|  2\.1\.7  |  Version updated for Greengrass nucleus version 2\.7\.0 release\.  | 
|  2\.1\.6  |  Version updated for Greengrass nucleus version 2\.6\.0 release\.  | 
|  2\.1\.5  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/dlr-image-classification-model-store-component.html)  | 
|  2\.1\.4  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.1\.3  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.1\.2  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.1\.1  |  <a name="changelog-dlr-image-classification-model-store-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/dlr-image-classification-model-store-component.html)  | 
|  2\.0\.4  |  Initial version\.  | 