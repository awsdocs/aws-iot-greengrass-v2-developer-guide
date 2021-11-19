# Perform sample image classification inference on images from a camera using TensorFlow Lite<a name="ml-tutorial-image-classification-camera"></a>

This tutorial shows you how to use the [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) inference component to perform sample image classification inference on images from a camera locally on a Greengrass core device\. This component includes the following component dependencies: 
+ TensorFlow Lite image classification model store component
+ TensorFlow Lite component

**Note**  
This tutorial accesses the camera module for [Raspberry Pi](https://www.raspberrypi.org/), [NVIDIA Jetson Nano](https://developer.nvidia.com/embedded/jetson-nano), or [AWS DeepLens](https://aws.amazon.com/deeplens/) devices, but AWS IoT Greengrass supports other devices on Armv7l, Armv8, or x86\_64 platforms\. To set up a camera for a different device, consult the relevant documentation for your device\.

**Topics**
+ [Prerequisites](#ml-tutorial-camera-prereqs)
+ [Step 1: Configure the camera module on your device](#ml-tutorial-image-classification-camera-install)
+ [Step 2: Verify your subscription to the default notifications topic](#ml-image-classification-camera-subscribe)
+ [Step 3: Modify the TensorFlow Lite image classification component configuration and deploy it](#ml-image-classification-camera-deploy)
+ [Step 4: View inference results](#ml-image-classification-camera-results)
+ [Next steps](#ml-image-classification-camera-next-steps)

## Prerequisites<a name="ml-tutorial-camera-prereqs"></a>

To complete this tutorial, you must first complete [Tutorial: Perform sample image classification inference using TensorFlow Lite](ml-tutorial-image-classification.md)\.

You also need the following:
+ A Linux Greengrass core device with a camera interface\. This tutorial accesses the camera module on one the following supported devices:
  + [Raspberry Pi](https://www.raspberrypi.org/) running [Raspberry Pi OS](https://www.raspberrypi.org/downloads/) \(previously called Raspbian\)
  + [NVIDIA Jetson Nano](https://developer.nvidia.com/embedded/jetson-nano)
  + [AWS DeepLens](https://aws.amazon.com/deeplens/)

  For information about setting up a Greengrass core device, see [Getting started with AWS IoT Greengrass V2](getting-started.md)\.
+ For Raspberry Pi or NVIDIA Jetson Nano devices, [Raspberry Pi Camera Module V2 \- 8 megapixel, 1080p](https://www.amazon.com/Raspberry-Pi-Camera-Module-Megapixel/dp/B01ER2SKFS)\. To learn how to set up the camera, see [Connecting the camera](https://www.raspberrypi.org/documentation/usage/camera/) in the Raspberry Pi documentation\. 
+ Dependencies for OpenCV Python installed on the device\. This requirement is applicable only for Armv7l devices such as the Raspberry Pi that is used in this tutorial\. You don't need to meet this requirement for other devices\. 

  Run the following command to install the dependencies: 

  ```
  sudo apt-get install libopenjp2-7 libilmbase23 libopenexr-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0 libwebp-dev
  ```

## Step 1: Configure the camera module on your device<a name="ml-tutorial-image-classification-camera-install"></a>

In this step, you install and enable the camera module for your device\. Run the following commands on the device\.

------
#### [ Raspberry Pi \(Armv7l\) ]

1. <a name="install-picamera-step"></a>Install the `picamera` interface for the camera module\. Run the following command to install the camera module and the other Python libraries that are required for this tutorial\.

   ```
   sudo apt-get install -y python3-picamera
   ```

1. Verify that Picamera installed successfully\.

   ```
   sudo -u ggc_user bash -c 'python3 -c "import picamera"'
   ```

   If the output doesn't contain errors, the validation is successful\.
**Note**  
If the Python executable file that is installed on your device is `python3.7`, use `python3.7` instead of `python3` for the commands in this tutorial\. Make sure that your pip installation maps to the correct `python3.7` or `python3` version to avoid dependency errors\.

1. Reboot the device\.

   ```
   sudo reboot
   ```

1. Open the Raspberry Pi configuration tool\.

   ```
   sudo raspi-config
   ```

1. Use the arrow keys to open **Interfacing Options** and enable the camera interface\. If prompted, allow the device to reboot\.

1. Run the following command to test the camera setup\.

   ```
   raspistill -v -o test.jpg
   ```

   This opens a preview window on the Raspberry Pi, saves a picture named `test.jpg` to your current directory, and displays information about the camera in the Raspberry Pi terminal\.

1. Run the following command to create a symlink to enable the inference component to access your camera from the virtual environment that is created by the runtime component\.

   ```
   sudo ln -s /usr/lib/python3/dist-packages/picamera "MLRootPath/greengrass_ml_tflite_venv/lib/python3.7/site-packages"
   ```

   The default value for *MLRootPath* for this tutorial is `/greengrass/v2/work/variant.TensorFlowLite/greengrass_ml`\. The `greengrass_ml_tflite_venv` folder in this location is created when you deploy the inference component for the first time in [Tutorial: Perform sample image classification inference using TensorFlow Lite](ml-tutorial-image-classification.md)\.

------
#### [ Jetson Nano \(Armv8\) ]

1. Run the following command to test the camera setup\.

   ```
   gst-launch-1.0 nvarguscamerasrc num-buffers=1 ! "video/x-raw(memory:NVMM), width=1920, height=1080, format=NV12, framerate=30/1" ! nvjpegenc ! filesink location=test.jpg
   ```

   This captures and saves an image named `test.jpg` to your current directory\.

1. \(Optional\) Reboot the device\. If you encounter issues when you run the `gst-launch` command in the previous step, rebooting your device might resolve those issues\.

   ```
   sudo reboot
   ```

**Note**  
For Armv8 \(AArch64\) devices, such as a Jetson Nano, you don't need to create a symlink to enable the inference component to access the camera from the virtual environment that is created by the runtime component\. 

------
#### [ AWS DeepLens \(x86\_64\) ]

1. <a name="install-awscam-step"></a>Update the `awscam` APT package\. Run the following command on the device\.

   ```
   sudo apt-get update && sudo apt-get install awscam
   ```

1. Reboot the device\.

   ```
   sudo reboot
   ```

1. Run each of the following commands to create a symlink to enable the inference component to access your camera from the virtual environment that is created by the runtime component\.

   ```
   sudo ln -s /usr/lib/python3/dist-packages/awscam "MLRootPath/greengrass_ml_tflite_conda/envs/greengrass_ml_tflite_conda/lib/python3.7/site-packages/"
   ```

   ```
   sudo ln -s /usr/lib/python3/dist-packages/awscamdldt.so "MLRootPath/greengrass_ml_tflite_conda/envs/greengrass_ml_tflite_conda/lib/python3.7/site-packages/"
   ```

   The default value for MLRootPath for this tutorial is `/greengrass/v2/work/variant.TensorFlowLite/greengrass_ml`\. The `greengrass_ml_tflite_conda` folder in this location is created when you deploy the inference component for the first time in [Tutorial: Perform sample image classification inference using TensorFlow Lite](ml-tutorial-image-classification.md)\.

For more information about using AWS DeepLens, see [AWS DeepLens Developer Guide](https://docs.aws.amazon.com/deeplens/latest/dg/)\.

------

## Step 2: Verify your subscription to the default notifications topic<a name="ml-image-classification-camera-subscribe"></a>

In [Tutorial: Perform sample image classification inference using TensorFlow Lite](ml-tutorial-image-classification.md), you configured the AWS IoT MQTT client is configured in the AWS IoT console to watch MQTT messages published by the TensorFlow Lite image classification component on the `ml/tflite/image-classification` topic\. In the AWS IoT console, verify that this subscription exists\. If it doesn't, follow the steps in [Step 1: Subscribe to the default notifications topic](ml-tutorial-image-classification.md#ml-image-classification-subscribe) to subscribe to this topic before you deploy the component to your Greengrass core device\.

## Step 3: Modify the TensorFlow Lite image classification component configuration and deploy it<a name="ml-image-classification-camera-deploy"></a>

In this step, you configure and deploy the TensorFlow Lite image classification component to your core device:

### To configure and deploy the TensorFlow Lite image classification component \(console\)<a name="ml-image-classification-camera-deploy-console"></a>

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

1. On the **Components** page, on the **Public components** tab, choose `aws.greengrass.TensorFlowLiteImageClassification`\.

1. On the **aws\.greengrass\.TensorFlowLiteImageClassification** page, choose **Deploy**\.

1. <a name="add-deployment"></a>From **Add to deployment**, choose one of the following:

   1. To merge this component to an existing deployment on your target device, choose **Add to existing deployment**, and then select the deployment that you want to revise\.

   1. To create a new deployment on your target device, choose **Create new deployment**\. If you have an existing deployment on your device, choosing this step replaces the existing deployment\. 

1. <a name="specify-deployment-target"></a>On the **Specify target** page, do the following: 

   1. Under **Deployment** information, enter or modify the friendly name for your deployment\.

   1. Under **Deployment targets**, select a target for your deployment, and choose **Next**\. You cannot change the deployment target if you are revising an existing deployment\.

1. On the **Select components** page, under **Public components**, verify that the `aws.greengrass.TensorFlowLiteImageClassification` component is selected, and choose **Next**\.

1. On the **Configure components** page, do the following: 

   1. Select the inference component, and choose **Configure component**\.

   1. Under **Configuration update**, enter the following configuration update in the **Configuration to merge** box\.

      ```
      {
        "InferenceInterval": "60",
        "UseCamera": "true"
      }
      ```

      With this configuration update, the component accesses the camera module on your device and performs inference on images taken by the camera\. The inference code runs every 60 seconds\.

   1. Choose **Confirm**, and then choose **Next**\.

1. On the **Configure advanced settings** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, choose **Deploy**

### To configure and deploy the TensorFlow Lite image classification component \(AWS CLI\)<a name="ml-image-classification-camera-deploy-cli"></a>

1. Create a `deployment.json` file to define the deployment configuration for the TensorFlow Lite image classification component\. This file should look like the following:

   ```
   {
     "targetArn":"targetArn",
     "components": {
       "aws.greengrass.TensorFlowLiteImageClassification": {
         "componentVersion": 2.1.0,
         "configurationUpdate": {
           "InferenceInterval": "60",
           "UseCamera": "true"
         }
       }
     }
   }
   ```
   + In the `targetArn` field, replace `targetArn` with the Amazon Resource Name \(ARN\) of the thing or thing group to target for the deployment, in the following format: 
     + Thing: `arn:aws:iot:region:account-id:thing/thingName`
     + Thing group: `arn:aws:iot:region:account-id:thinggroup/thingGroupName`
   + This tutorial uses component version 2\.1\.0\. In the `aws.greengrass.TensorFlowLiteImageClassification` component object, replace *2\.1\.0* to use a different version of the TensorFlow Lite image classification component\.

   With this configuration update, the component accesses the camera module on your device and performs inference on images taken by the camera\. The inference code runs every 60 seconds\. Replace the following values

1. Run the following command to deploy the TensorFlow Lite image classification component on the device:

   ```
   $ aws greengrassv2 create-deployment \
       --cli-input-json file://path/to/deployment.json
   ```

The deployment can take several minutes to complete\. In the next step, check the component log to verify that the deployment completed successfully and to view the inference results\.

## Step 4: View inference results<a name="ml-image-classification-camera-results"></a>

After you deploy the component, you can view the inference results in the component log on your Greengrass core device and in the AWS IoT MQTT client in the AWS IoT console\. To subscribe to the topic on which the component publishes inference results, see [Step 2: Verify your subscription to the default notifications topic](#ml-image-classification-camera-subscribe)\.

**Topics**
+ **AWS IoT MQTT client**—To view the results that the inference component publishes on the [default notifications topic](ml-tutorial-image-classification.md#ml-image-classification-subscribe), complete the following steps:

  1. In the [AWS IoT console](https://console.aws.amazon.com/iot/) navigation menu, choose **Test, MQTT test client**\.

  1. Under **Subscriptions**, choose **ml/tflite/image\-classification**\.

   
+ **Component log**—To view the inference results in the component log, run the following command on your Greengrass core device\. 

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.TensorFlowLiteImageClassification.log
  ```

If you can't see inference results in the component log or in the MQTT client, the deployment failed or didn't reach the core device\. This can occur if your core device isn't connected to the internet or doesn't have the required permissions to run the component\. Run the following command on your core device to view the AWS IoT Greengrass Core software log file\. This file includes logs from the Greengrass core device's deployment service\.

```
sudo tail -f /greengrass/v2/logs/greengrass.log
```

For more information, see [Troubleshooting machine learning inference](ml-troubleshooting.md)\.

## Next steps<a name="ml-image-classification-camera-next-steps"></a>

This tutorial shows you how to use the TensorFlow Lite image classification component, with custom configuration options to perform sample image classification on images taken by a camera\. 

For more information about customizing the configuration of public components or creating custom machine learning components, see [Customize your machine learning components](ml-customization.md)\.