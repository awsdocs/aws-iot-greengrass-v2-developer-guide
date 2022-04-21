# Troubleshooting machine learning inference<a name="ml-troubleshooting"></a>

Use the troubleshooting information and solutions in this section to help resolve issues with your machine learning components\. For the public machine learning inference components, see the error messages in the following component logs:

------
#### [ Linux or Unix ]
+ `/greengrass/v2/logs/aws.greengrass.DLRImageClassification.log`
+ `/greengrass/v2/logs/aws.greengrass.DLRObjectDetection.log`
+ `/greengrass/v2/logs/aws.greengrass.TensorFlowLiteImageClassification.log`
+ `/greengrass/v2/logs/aws.greengrass.TensorFlowLiteObjectDetection.log`

------
#### [ Windows ]
+ `C:\greengrass\v2\logs\aws.greengrass.DLRImageClassification.log`
+ `C:\greengrass\v2\logs\aws.greengrass.DLRObjectDetection.log`
+ `C:\greengrass\v2\logs\aws.greengrass.TensorFlowLiteImageClassification.log`
+ `C:\greengrass\v2\logs\aws.greengrass.TensorFlowLiteObjectDetection.log`

------

If a component is installed correctly, then the component log contains the location of the library that it uses for inference\.

**Topics**
+ [Failed to fetch library](#rpi-update-error)
+ [Cannot open shared object file](#rpi-import-cv-error)
+ [Error: ModuleNotFoundError: No module named '<library>'](#troubleshooting-venv-errors-not-found)
+ [No CUDA\-capable device is detected](#troubleshooting-cuda-error)
+ [No such file or directory](#troubleshooting-venv-errors-no-such-file)
+ [Memory errors](#troubleshooting-memory-errors)
+ [Disk space errors](#troubleshooting-disk-space-errors)
+ [Timeout errors](#troubleshooting-timeout-errors)

## Failed to fetch library<a name="rpi-update-error"></a>

The following error occurs when the installer script fails to download a required library during deployment on a Raspberry Pi device\.

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

## Error: ModuleNotFoundError: No module named '<library>'<a name="troubleshooting-venv-errors-not-found"></a>

You might see this error in the ML runtime component logs \(`variant.DLR.log` or `variant.TensorFlowLite.log`\) when the ML runtime library or its dependencies aren't installed correctly\. This error can occur in the following cases:
+ If you use the `UseInstaller` option, which is enabled by default, this error indicates that the ML runtime component failed to install the runtime or its dependencies\. Do the following:

  1. Configure the ML runtime component to disable the `UseInstaller` option\.

  1. Install the ML runtime and its dependencies, and make them available to the system user that runs the ML components\. For more information, see the following:
     + [DLR runtime UseInstaller option](dlr-component.md#dlr-component-config-useinstaller-term)
     + [TensorFlow Lite runtime UseInstaller option](tensorflow-lite-component.md#tensorflow-lite-component-config-useinstaller-term)
+ If you don't use the `UseInstaller` option, this error indicates that the ML runtime or its dependencies aren't installed for the system user that runs the ML components\. Do the following:

  1. Check that the library is installed for the system user that runs the ML components\. Replace *ggc\_user* with the name of the system user, and replace *tflite\_runtime* with the name of the library to check\.

------
#### [ Linux or Unix ]

     ```
     sudo -H -u ggc_user bash -c "python3 -c 'import tflite_runtime'"
     ```

------
#### [ Windows ]

     ```
     runas /user:ggc_user "py -3 -c \"import tflite_runtime\""
     ```

------

  1. If the library isn't installed, install it for that user\. Replace *ggc\_user* with the name of the system user, and replace *tflite\_runtime* with the name of the library\.

------
#### [ Linux or Unix ]

     ```
     sudo -H -u ggc_user bash -c "python3 -m pip install --user tflite_runtime"
     ```

------
#### [ Windows ]

     ```
     runas /user:ggc_user "py -3 -m pip install --user tflite_runtime"
     ```

------

     For more information about the dependencies for each ML runtime, see the following:
     + [DLR runtime UseInstaller option](dlr-component.md#dlr-component-config-useinstaller-term)
     + [TensorFlow Lite runtime UseInstaller option](tensorflow-lite-component.md#tensorflow-lite-component-config-useinstaller-term)

  1. If the issue persists, install the library for another user to confirm whether this device can install the library\. The user could be, for example, your user, the root user, or an administrator user\. If you can't install the library successfully for any user, your device might not support the library\. Consult the library's documentation to review requirements and troubleshoot installation issues\.

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
+ [DLR runtime](dlr-component.md)
+ [TensorFlow Lite runtime](tensorflow-lite-component.md)

By default *MLRootPath* is set to `/greengrass/v2/work/component-name/greengrass_ml`\. To change this location, include the [DLR runtime](dlr-component.md) or [TensorFlow Lite runtime](tensorflow-lite-component.md) runtime component directly in your deployment, and specify a modified value for the `MLRootPath` parameter in a configuration merge update\. For more information about configuring component, see [Update component configurations](update-component-configurations.md)\.

**Note**  
For the DLR component v1\.3\.x, you set the `MLRootPath` parameter in the configuration of the inference component, and the default value is `$HOME/greengrass_ml`\.

## Memory errors<a name="troubleshooting-memory-errors"></a>

The following errors typically occur when the device does not have enough memory and the component process is interrupted\.
+ `stderr. Killed.`
+ `exitCode=137`

We recommend a minimum of 500 MB of memory to deploy a public machine learning inference component\.

## Disk space errors<a name="troubleshooting-disk-space-errors"></a>

The `no space left on device` error typically occurs when a device does not have enough storage\. Make sure that there is enough disk space available on your device before you deploy the component again\. We recommend a minimum of 500 MB of free disk space to deploy a public machine learning inference component\.

## Timeout errors<a name="troubleshooting-timeout-errors"></a>

The public machine learning components download large machine learning model files that are larger than 200 MB\. If the download times out during deployment, check your internet connection speed and retry the deployment\.