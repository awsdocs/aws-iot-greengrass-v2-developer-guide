# Lookout for Vision Edge Agent<a name="lookout-for-vision-edge-agent-component"></a>

The Lookout for Vision Edge Agent component \(`aws.iot.lookoutvision.EdgeAgent`\) installs a local Amazon Lookout for Vision runtime server, which uses computer vision to find visual defects in industrial products\.

To use this component, create and deploy Lookout for Vision machine learning model components\. These machine learning models predict the presence of anomalies in images by finding patterns in images that you use to train the model\. Then, you can develop and deploy custom Greengrass components, called client application components, that provide images and video streams to this runtime component to detect anomalies using the machine learning models\.

You can use the Lookout for Vision Edge Agent API to interact with this component from other Greengrass components\. This API is implemented using [gRPC](https://grpc.io/), which is a protocol for making remote procedure calls\. For more information, see [Writing a client application component](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/client-application-overview.html) and [Lookout for Vision Edge Agent API reference](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/edge-agent-reference.html) in the *Amazon Lookout for Vision Developer Guide*\.

For more information about how to use this component, see the following:
+ [Use Amazon Lookout for Vision on Greengrass core devices](use-lookout-for-vision.md)
+ [What is Amazon Lookout for Vision?](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/what-is.html) in the *Amazon Lookout for Vision Developer Guide*
+ [Creating a Lookout for Vision model](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/model.html) in the *Amazon Lookout for Vision Developer Guide*\.
+ [Using a Lookout for Vision model on an edge device](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/models-devices.html) in the *Amazon Lookout for Vision Developer Guide*\.

**Note**  
The Lookout for Vision Edge Agent component is available only in the following AWS Regions:  
US East \(Ohio\)
US East \(N\. Virginia\)
US West \(Oregon\)
Europe \(Frankfurt\)
Europe \(Ireland\)
Asia Pacific \(Tokyo\)
Asia Pacific \(Seoul\)

**Topics**
+ [Versions](#lookout-for-vision-edge-agent-component-versions)
+ [Type](#lookout-for-vision-edge-agent-component-type)
+ [Operating system](#lookout-for-vision-edge-agent-component-os-support)
+ [Requirements](#lookout-for-vision-edge-agent-component-requirements)
+ [Dependencies](#lookout-for-vision-edge-agent-component-dependencies)
+ [Configuration](#lookout-for-vision-edge-agent-component-configuration)
+ [Local log file](#lookout-for-vision-edge-agent-component-log-file)
+ [Changelog](#lookout-for-vision-edge-agent-component-changelog)

## Versions<a name="lookout-for-vision-edge-agent-component-versions"></a>

This component has the following versions:
+ 1\.1\.x
+ 1\.0\.x
+ 0\.1\.x

## Type<a name="lookout-for-vision-edge-agent-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="lookout-for-vision-edge-agent-component-os-support"></a>

This component can be installed on Linux core devices only\.

## Requirements<a name="lookout-for-vision-edge-agent-component-requirements"></a>

This component has the following requirements:
+ The Greengrass core device must use an Armv8 \(AArch64\) or x86\_64 architecture\.
+ If you use version 1\.0\.0 or later of this component, [Python](https://www.python.org/downloads/) 3\.8 or [Python](https://www.python.org/downloads/) 3\.9, including `pip`, installed on the Greengrass core device\.

  If you use version 0\.1\.x of this component, [Python](https://www.python.org/downloads/) 3\.7, including `pip`, installed on the Greengrass core device\.
**Important**  
The device must have one of these exact versions of Python\. This component doesn't support later versions of Python\.
+ To use graphics processing unit \(GPU\) inference, the core device must meet the following requirements\. GPU inference is optional in version 1\.1\.0 and later of this component\.
  + A graphics processing unit \(GPU\) that supports CUDA\. For more information, see [Verify You Have a CUDA\-Capable GPU](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#verify-you-have-cuda-enabled-system) in the *CUDA Toolkit Documentation*\.
  + cuDNN, CUDA, and TensorRT installed on the Greengrass core device\.
    + On NVIDIA Jetson devices, such as the Jetson Nano or Jetson Xavier, cuDNN, CUDA, and TensorRT come installed with NVIDIA JetPack\. You don't need to make any changes\. This component supports [JetPack 4\.4](https://developer.nvidia.com/jetpack-sdk-44-archive), [JetPack 4\.5](https://developer.nvidia.com/jetpack-sdk-45-archive), and [JetPack 4\.5\.1](https://developer.nvidia.com/jetpack-sdk-451-archive)\.
**Important**  
You must install one of these versions of JetPack, and not another version such as JetPack 4\.6\.1\. The Lookout for Vision service compiles computer vision models for these JetPack platforms\.
    + On x86 devices with a GPU that has the NVIDIA Ampere microarchitecture \(or the GPU's compute capacity is 8\.0\), do the following:
      + Install cuDNN by following instructions in the [NVIDIA cuDNN Documentation](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html)\.
      + Install CUDA version 11\.2 by following instructions in the [NVIDIA CUDA Installation Guide for Linux](https://docs.nvidia.com/cuda/archive/11.2.0/cuda-installation-guide-linux/index.html)\.
      + Install TensorRT version 8\.2\.0 by following instructions in the [NVIDIA TensorRT Documentation](https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html)\.
    + On x86 devices with a GPU that has an NVIDIA architecture prior to Ampere \(or the GPU's compute capacity is less than 8\.0\), do the following:
      + Install cuDNN by following instructions in the [NVIDIA cuDNN Documentation](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html)\.
      + Install CUDA version 10\.2 by following instructions in the [NVIDIA CUDA Installation Guide for Linux](https://docs.nvidia.com/cuda/archive/10.2/cuda-installation-guide-linux/index.html)\.
      + Install TensorRT version 7\.1\.3 or later, but earlier than version 8\.0\.0, by following instructions in the [NVIDIA TensorRT Documentation](https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html)\.
  + The system user that runs this component must be a member of the system group that has access to the GPU on the device\. The name of this group differs by operating system\. Consult the documentation for your operating system and GPU to determine the name of this system group\.

    For example, on NVIDIA Jetson devices, the name of this group is `video`, and you can run the following command to add a system user to this group\. Replace *ggc\_user* with the name of the user to add\.

    ```
    sudo usermod -aG video ggc_user
    ```

## Dependencies<a name="lookout-for-vision-edge-agent-component-dependencies"></a>

This component doesn't have any dependencies\.

## Configuration<a name="lookout-for-vision-edge-agent-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`Socket`  
\(Optional\) The file socket where the Edge Agent operates\. Lookout for Vision model components use this file socket to communicate with the Edge Agent\. If you change this parameter, you must specify the same value when you deploy Lookout for Vision model components\.  
Default: `unix:///tmp/aws.iot.lookoutvision.EdgeAgent.sock`

## Local log file<a name="lookout-for-vision-edge-agent-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/aws.iot.lookoutvision.EdgeAgent.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.iot.lookoutvision.EdgeAgent.log
  ```

## Changelog<a name="lookout-for-vision-edge-agent-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  1\.1\.1  |  General bug fixes and improvements\.  | 
|  1\.1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/lookout-for-vision-edge-agent-component.html)  | 
|  1\.0\.0  |  This version of the Lookout for Vision Edge Agent component requires a different version of Python than version 0\.1\.x\. If you want to upgrade from v0\.1\.x to v1\.x, you must upgrade the Python installation on the core device\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/lookout-for-vision-edge-agent-component.html)  | 
|  0\.1\.37  |  General bug fixes and improvements\.  | 
|  0\.1\.36  |  Initial version\.  | 