# Troubleshooting machine learning inference<a name="ml-troubleshooting"></a>

Use the troubleshooting information and solutions in this section to help resolve issues with your machine learning components\. For the public machine learning inference components, you can see error messages in the following component logs:
+ `/greengrass/v2/logs/aws.greengrass.DLRImageClassification.log`
+ `/greengrass/v2/logs/aws.greengrass.DLRObjectDetection.log`
+ `/greengrass/v2/logs/aws.greengrass.TensorFlowLiteImageClassification.log`
+ `/greengrass/v2/logs/aws.greengrass.TensorFlowLiteObjectDetection.log`

If a component is installed correctly, then the component log contains the location of the library that it uses for inference\.

**Topics**
+ [Failed to fetch library](#rpi-update-error)
+ [Cannot open shared object file](#rpi-import-cv-error)
+ [<library> not found](#troubleshooting-venv-errors-not-found)
+ [No CUDA\-capable device is detected](#troubleshooting-cuda-error)
+ [No such file or directory](#troubleshooting-venv-errors-no-such-file)
+ [Memory errors](#troubleshooting-memory-errors)
+ [Disk space errors](#troubleshooting-disk-space-errors)
+ [Timeout errors](#troubleshooting-timeout-errors)

## Failed to fetch library<a name="rpi-update-error"></a>

Te following error occurs when the installer script fails to download a required library during deployment on a Raspberry Pi device\.

```
Err:2 http://raspbian.raspberrypi.org/raspbian buster/main armhf python3.7-dev armhf 3.7.3-2+deb10u1
404 Not Found [IP: 93.93.128.193 80] 
E: Failed to fetch http://raspbian.raspberrypi.org/raspbian/pool/main/p/python3.7/libpython3.7-dev_3.7.3-2+deb10u1_armhf.deb 404 Not Found [IP: 93.93.128.193 80]
```

Run `sudo apt-get update` and deploy your component again\.

## Cannot open shared object file<a name="rpi-import-cv-error"></a>

You might see errors similar to the following when the installer script fails to download a required dependency for `opencv-python` during deployment on a Raspberry Pi device\.

```
ImportError: libopenjp2.so.7: cannot open shared object file: No such file or directory
```

Run the following command to manually install the dependencies for `opencv-python`:

```
sudo apt-get install libopenjp2-7 libilmbase23 libopenexr-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0 libwebp-dev
```

## <library> not found<a name="troubleshooting-venv-errors-not-found"></a>

The following errors indicate that the runtime component was unable to set up the virtual environment correctly:
+ `cv2 not found `
+ `dlr not found `
+ `numpy not found `

Check the logs to make sure that all runtime dependencies were installed correctly\. For more information about the libraries installed by the installer script, see the following topics:
+ [DLR](dlr-component.md)
+ [TensorFlow Lite](tensorflow-lite-component.md)

## No CUDA\-capable device is detected<a name="troubleshooting-cuda-error"></a>

You might see the following error when you use GPU acceleration\. Run the following command to enable GPU access for the Greengrass user\.

```
sudo usermod -a -G video ggc_user
```

## No such file or directory<a name="troubleshooting-venv-errors-no-such-file"></a>

The following errors indicate that the runtime component was unable to set up the virtual environment correctly:
+ `MLRootPath/greengrass_ml_dlr_conda/bin/conda: No such file or directory `
+ `MLRootPath/greengrass_ml_dlr_venv/bin/activate: No such file or directory ` 
+ `MLRootPath/greengrass_ml_tflite_conda/bin/conda: No such file or directory ` 
+ `MLRootPath/greengrass_ml_tflite_venv/bin/activate: No such file or directory `

Check the logs to make sure that all runtime dependencies were installed correctly\. For more information about the libraries installed by the installer script, see the following topics:
+ [DLR](dlr-component.md)
+ [TensorFlow Lite](tensorflow-lite-component.md)

By default *MLRootPath* is set to `/greengrass/v2/work/component-name/greengrass_ml`\. To change this location, include the [DLR](dlr-component.md) or [TensorFlow Lite](tensorflow-lite-component.md) runtime component directly in your deployment, and specify a modified value for the `MLRootPath` parameter in a configuration merge update\. For more information about configuring component, see [Update component configurations](update-component-configurations.md)\.

**Note**  
For the DLR component v1\.3\.x, you set the `MLRootPath` parameter in the configuration of the inference component, and the default value is `$HOME/greengrass_ml`\.

## Memory errors<a name="troubleshooting-memory-errors"></a>

The following errors typically occur when the device does not have enough memory and the component process is interrupted\.
+ `stderr. Killed.`
+ `exitCode=137`

We recommend a minimum of 500 MB of memory to deploy a public machine learning inference component\.

## Disk space errors<a name="troubleshooting-disk-space-errors"></a>

The `no space left on device` error typically occurs when a device does not have enough storage\. Check the disk space that is available on your device and make sure you have enough space before you deploy the component again\. We recommend a minimum of 500 MB of free disk space to deploy a public machine learning inference component\.

## Timeout errors<a name="troubleshooting-timeout-errors"></a>

The public machine learning components download large machine learning model files that are larger than 200 MB\. If the download times out during deployment, check your internet connection speed and retry the deployment\.