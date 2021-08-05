# Tutorial: Perform sample image classification inference using TensorFlow Lite<a name="ml-tutorial-image-classification"></a>

This tutorial shows you how to use the [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) inference component to perform sample image classification inference on a Greengrass core device\. This component includes the following component dependencies: 
+ TensorFlow Lite image classification model store component
+ TensorFlow Lite component

When you deploy this component, it downloads a pre\-trained MobileNet v1 model and installs the [TensorFlow Lite](https://www.tensorflow.org/lite) runtime and its dependencies\. This component publishes inference results on the `ml/tflite/image-classification` topic\. To view these inference results, use the AWS IoT MQTT client in the AWS IoT console to subscribe to this topic\. 

In this tutorial you deploy the sample inference component to perform image classification on the sample image that is provided by AWS IoT Greengrass\. After you complete this tutorial, you can complete [Perform sample image classification inference on images from a camera using TensorFlow Lite](ml-tutorial-image-classification-camera.md), which shows you how to modify the sample inference component to perform image classification on images from a camera locally on a Greengrass core device\.

**Topics**
+ [Prerequisites](#ml-tutorial-prereqs)
+ [Step 1: Subscribe to the default notifications topic](#ml-image-classification-subscribe)
+ [Step 2: Deploy the TensorFlow Lite image classification component](#ml-image-classification-deploy)
+ [Step 3: View inference results](#ml-image-classification-results)
+ [Next steps](#ml-image-classification-next-steps)

## Prerequisites<a name="ml-tutorial-prereqs"></a>

To complete this tutorial, you need the following:
+ A Greengrass core device\. If you don't have one, see [Getting started with AWS IoT Greengrass V2](getting-started.md)\.
+ If you are using an Armv7l device such as Raspberry Pi, dependencies for OpenCV Python installed on the device\. Run the following command to install the dependencies: 

  ```
  sudo apt-get install libopenjp2-7 libilmbase23 libopenexr-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0 libwebp-dev
  ```

## Step 1: Subscribe to the default notifications topic<a name="ml-image-classification-subscribe"></a>

In this step, you configure the AWS IoT MQTT client in the AWS IoT console to watch MQTT messages published by the TensorFlow Lite image classification component\. By default, the component publishes inference results on the `ml/tflite/image-classification` topic\. Subscribe to this topic before you deploy the component to your Greengrass core device to see the inference results when the component runs for the first time\. 



**To subscribe to the default notifications topic**

1. In the [AWS IoT console](https://console.aws.amazon.com/iot/) navigation menu, choose **Test, MQTT test client**\.

1. Under **Subscribe to a topic**, in the **Topic name** box, enter **ml/tflite/image\-classification**\.

1. Choose **Subscribe**\.

## Step 2: Deploy the TensorFlow Lite image classification component<a name="ml-image-classification-deploy"></a>

In this step, you deploy the TensorFlow Lite image classification component to your core device:

### To deploy the TensorFlow Lite image classification component \(console\)<a name="ml-image-classification-deploy-console"></a>

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

1. On the **Configure components** page, keep the default configuration settings, and choose **Next**\.

1. On the **Configure advanced settings** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, choose **Deploy**

### To deploy the TensorFlow Lite image classification component \(AWS CLI\)<a name="ml-image-classification-deploy-cli"></a>

1. Create a `deployment.json` file to define the deployment configuration for the TensorFlow Lite image classification component\. This file should look like the following:

   ```
   {
     "targetArn":"targetArn",
     "components": {
       "aws.greengrass.TensorFlowLiteImageClassification": {
         "componentVersion": 2.1.0,
         "configurationUpdate": {
         }
       }
     }
   }
   ```
   + In the `targetArn` field, replace `targetArn` with the Amazon Resource Name \(ARN\) of the thing or thing group to target for the deployment, in the following format: 
     + Thing: `arn:aws:iot:region:account-id:thing/thingName`
     + Thing group: `arn:aws:iot:region:account-id:thinggroup/thingGroupName`
   + This tutorial uses component version 2\.1\.0\. In the `aws.greengrass.TensorFlowLiteObjectDetection` component object, replace *2\.1\.0* to use a different version of the TensorFlow Lite object detection component\.

1. Run the following command to deploy the TensorFlow Lite image classification component on the device:

   ```
   $ aws greengrassv2 create-deployment \
       --cli-input-json file://path/to/deployment.json
   ```

The deployment can take several minutes to complete\. In the next step, check the component log to verify that the deployment completed successfully and to view the inference results\.

## Step 3: View inference results<a name="ml-image-classification-results"></a>

After you deploy the component, you can view the inference results in the component log on your Greengrass core device and in the AWS IoT MQTT client in the AWS IoT console\. To subscribe to the topic on which the component publishes inference results, see [Step 1: Subscribe to the default notifications topic](#ml-image-classification-subscribe)\.
+ **AWS IoT MQTT client**—To view the results that the inference component publishes on the [default notifications topic](#ml-image-classification-subscribe), complete the following steps:

  1. In the [AWS IoT console](https://console.aws.amazon.com/iot/) navigation menu, choose **Test, MQTT test client**\.

  1. Under **Subscriptions**, choose **ml/tflite/image\-classification**\.

     You should see messages similar to the following example\.

     ```
     {
       "timestamp": "2021-01-01 00:00:00.000000",
       "inference-type": "image-classification",
       "inference-description": "Top 5 predictions with score 0.3 or above ",
       "inference-results": [
         {
           "Label": "cougar, puma, catamount, mountain lion, painter, panther, Felis concolor",
           "Score": "0.5882352941176471"
         },
         {
           "Label": "Persian cat",
           "Score": "0.5882352941176471"
         },
         {
           "Label": "tiger cat",
           "Score": "0.5882352941176471"
         },
         {
           "Label": "dalmatian, coach dog, carriage dog",
           "Score": "0.5607843137254902"
         },
         {
           "Label": "malamute, malemute, Alaskan malamute",
           "Score": "0.5450980392156862"
         }
       ]
     }
     ```

   
+ **Component log**—To view the inference results in the component log, run the following command on your Greengrass core device\. 

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.TensorFlowLiteImageClassification.log
  ```

  You should see results similar to the following example\.

  ```
  2021-01-01 00:00:00.000000 [INFO] (Copier) aws.greengrass.TensorFlowLiteImageClassification: stdout. Publishing results to the IoT core.... {scriptName=services.aws.greengrass.TensorFlowLiteImageClassification.lifecycle.Run.script, serviceName=aws.greengrass.TensorFlowLiteImageClassification, currentState=RUNNING}
          
  2021-01-01 00:00:00.000000 [INFO] (Copier) aws.greengrass.TensorFlowLiteImageClassification: stdout. {"timestamp": "2021-01-01 00:00:00.000000", "inference-type": "image-classification", "inference-description": "Top 5 predictions with score 0.3 or above ", "inference-results": [{"Label": "cougar, puma, catamount, mountain lion, painter, panther, Felis concolor", "Score": "0.5882352941176471"}, {"Label": "Persian cat", "Score": "0.5882352941176471"}, {"Label": "tiger cat", "Score": "0.5882352941176471"}, {"Label": "dalmatian, coach dog, carriage dog", "Score": "0.5607843137254902"}, {"Label": "malamute, malemute, Alaskan malamute", "Score": "0.5450980392156862"}]}. {scriptName=services.aws.greengrass.TensorFlowLiteImageClassification.lifecycle.Run.script, serviceName=aws.greengrass.TensorFlowLiteImageClassification, currentState=RUNNING}
  ```

If you can't see inference results in the component log or in the MQTT client, the deployment failed or didn't reach the core device\. This can occur if your core device isn't connected to the internet or doesn't have the right permissions to run the component\. Run the following command on your core device to view the AWS IoT Greengrass Core software log file\. This file includes logs from the Greengrass core device's deployment service\.

```
sudo tail -f /greengrass/v2/logs/greengrass.log
```

For more information, see [Troubleshooting machine learning inference](ml-troubleshooting.md)\.

## Next steps<a name="ml-image-classification-next-steps"></a>

If you have a Greengrass core device with a supported camera interface, you can complete [Perform sample image classification inference on images from a camera using TensorFlow Lite](ml-tutorial-image-classification-camera.md), which shows you how to modify the sample inference component to perform image classification on images from a camera\.

To further explore the configuration of the sample [TensorFlow Lite image classification](tensorflow-lite-image-classification-component.md) inference component, try the following:
+ Modify the `InferenceInterval` configuration parameter to change how often the inference code runs\.
+ Modify the `ImageName` and `ImageDirectory` configuration parameters in the inference component configuration to specify a custom image to use for inference\. 

For information about customizing the configuration of public components or creating custom machine learning components, see [Customize your machine learning components](ml-customization.md)\.