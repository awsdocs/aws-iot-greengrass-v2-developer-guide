# Get started with SageMaker Edge Manager<a name="get-started-with-edge-manager-on-greengrass"></a>

To set up and use the SageMaker Edge Manager agent on an existing Greengrass core device, AWS provides example code that you can use to create the following sample inference and model components\. 
+ **Image classification**
  +  `com.greengrass.SageMakerEdgeManager.ImageClassification` 
  +  `com.greengrass.SageMakerEdgeManager.ImageClassification.Model` 
+ **Object detection**
  + `com.greengrass.SageMakerEdgeManager.ObjectDetection `
  + `com.greengrass.SageMakerEdgeManager.ObjectDetection.Model`

This tutorial shows you how to deploy the sample components and the SageMaker Edge Manager agent\.

**Topics**
+ [Prerequisites](#edge-manager-getting-started-prereqs)
+ [Set up your Greengrass core device in SageMaker Edge Manager](#greengrass-edge-manager-set-up)
+ [Create the sample components](#create-sample-sme-ml-components)
+ [Run sample image classification inference](#run-sample-sme-image-classification-inference)

## Prerequisites<a name="edge-manager-getting-started-prereqs"></a>

To complete this tutorial, you must meet the following prerequisites:
+ <a name="sm-req-core-device"></a>A Greengrass core device running on a Debian\-based Linux platform \(x86\_64 or Armv8\)\. If you don't have one, see [Getting started with AWS IoT Greengrass V2](getting-started.md)\.
+ <a name="sm-req-python"></a>[Python](https://www.python.org/downloads/) 3\.6 or later, including `pip` for your version of Python, installed on your core device\.
+ The OpenGL API GLX runtime \(`libgl1-mesa-glx`\) installed on your core device\.
+ An AWS Identity and Access Management \(IAM\) user with administrator permissions\.
+ An internet\-enabled Windows, Mac, or Unix\-like development computer that meets the following requirements:
  + [Python](https://www.python.org/downloads/) 3\.6 or later installed\.
  + AWS CLI installed and configured with your IAM administrator user credentials\. For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)\. 
+ The following S3 buckets created in the same AWS account and AWS Region as your Greengrass core device\. : 
  + An S3 bucket to store the artifacts that are included in the sample inference and model components\. This tutorial uses *DOC\-EXAMPLE\-BUCKET1* to refer to this bucket\. 
  + An S3 bucket that you associate with your SageMaker edge device fleet\. SageMaker Edge Manager requires an S3 bucket to create the edge device fleet, and to store sample data from running inference on your device\. This tutorial uses *DOC\-EXAMPLE\-BUCKET2* to refer to this bucket\. 

  For information about creating S3 buckets, see [Getting started with Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/GetStartedWithS3.html)\.
+ The [Greengrass device role](device-service-role.md) configured with the following: 
  + <a name="sm-req-iam-trust-relationship"></a>A trust relationship that allows `credentials.iot.amazonaws.com` and `sagemaker.amazonaws.com` to assume the role, as shown in the following IAM policy example\.

    ```
    { 
      "Version": "2012-10-17",
      "Statement": [ 
      { 
        "Effect": "Allow", 
        "Principal": {
          "Service": "credentials.iot.amazonaws.com"
         }, 
        "Action": "sts:AssumeRole" 
      }, 
      { 
        "Effect": "Allow", 
        "Principal": {
          "Service": "sagemaker.amazonaws.com"
        }, 
        "Action": "sts:AssumeRole" 
      } 
      ] 
    }
    ```
  + <a name="sm-req-iam-sagemanakeredgedevicefleetpolicy"></a>The [AmazonSageMakerEdgeDeviceFleetPolicy](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AmazonSageMakerEdgeDeviceFleetPolicy) IAM managed policy\.
  + The [AmazonSageMakerFullAccess](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AmazonSageMakerFullAccess) IAM managed policy\.
  + The `s3:GetObject` action for the S3 bucket that contains your component artifacts, as shown in the following IAM policy example\.

    ```
    {
      "Version": "2012-10-17",
      "Statement": [
      {
        "Action": [
          "s3:GetObject"
        ],
        "Resource": [
          "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
        ],
        "Effect": "Allow"
      }
      ]
    }
    ```

## Set up your Greengrass core device in SageMaker Edge Manager<a name="greengrass-edge-manager-set-up"></a>

Edge device fleets in SageMaker Edge Manager are collections of logically grouped devices\. To use SageMaker Edge Manager with AWS IoT Greengrass, you must create an edge device fleet that uses the same AWS IoT role alias as the Greengrass core device to which you deploy the SageMaker Edge Manager agent\. Then, you must register the core device as part of that fleet\.

**Topics**
+ [Create an edge device fleet](#create-edge-device-fleet-for-greengrass)
+ [Register your Greengrass core device](#register-greengrass-core-device-in-sme)

### Create an edge device fleet<a name="create-edge-device-fleet-for-greengrass"></a>

**To create an edge device fleet \(console\)**

1. In the [Amazon SageMaker console](https://console.aws.amazon.com/sagemaker), choose **Edge Manager**, and then choose **Edge device fleets**\.

1. On the **Device fleets** page, choose **Create device fleet**\.

1. Under **Device fleet properties**, do the following:
   + For **Device fleet name**, enter a name for your device fleet\.
   + For **IAM role**, enter the Amazon Resource Name \(ARN\) of the AWS IoT role alias that you specified when setting up your Greengrass core device\. 
   + Disable the **Create IAM role alias** toggle\. 

1. Choose **Next**\.

1. Under **Output configuration**, for **S3 bucket URI**, enter the URI of the S3 bucket that you want to associate with the device fleet\.

1. Choose **Submit**\.

### Register your Greengrass core device<a name="register-greengrass-core-device-in-sme"></a>

**To register your Greengrass core device as an edge device \(console\)**

1. In the [Amazon SageMaker console](https://console.aws.amazon.com/sagemaker), choose **Edge Manager**, and then choose **Edge devices**\.

1. On the **Devices** page, choose **Register devices**\.

1. Under **Device properties**, for **Device fleet name**, enter the name of the device fleet that you created, and then choose **Next**\.

1. Choose **Next**\.

1. Under **Device source**, for **Device name**, enter the AWS IoT thing name of your Greengrass core device\.

1. Choose **Submit**\.

## Create the sample components<a name="create-sample-sme-ml-components"></a>

To help you get started using the SageMaker Edge Manager component, AWS provides a Python script on GitHub that creates the sample inference and model components and uploads them to the AWS Cloud for you\. Complete the following steps on a development computer\.

**To create the sample components**

1. Download the [AWS IoT Greengrass component examples](https://github.com/aws-greengrass/aws-greengrass-component-examples/) repository on GitHub to your development computer\. 

1. Navigate to the downloaded `/machine-learning/sagemaker-edge-manager` folder\.

   ```
   cd download-directory/machine-learning/sagemaker-edge-manager
   ```

1. Run the following command to create and upload the sample components to the AWS Cloud\.

   ```
   python3 create_components.py -r region -b DOC-EXAMPLE-BUCKET
   ```

   Replace *region* with the AWS Region where you created your Greengrass core device, and replace *DOC\-EXAMPLE\-BUCKET1* with the name of the S3 bucket to store your component artifacts\.
**Note**  
By default, the script creates sample components for both image classification and object detection inference\. To create components for only a specific type of inference, specify the `-i ImageClassification | ObjectDetection` argument\.

Sample inference and model components for use with SageMaker Edge Manager are now created in your AWS account\. To see the sample components in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass), choose **Components**, and then under **My components**, search for the following components:
+  `com.greengrass.SageMakerEdgeManager.ImageClassification` 
+  `com.greengrass.SageMakerEdgeManager.ImageClassification.Model` 
+ `com.greengrass.SageMakerEdgeManager.ObjectDetection `
+ `com.greengrass.SageMakerEdgeManager.ObjectDetection.Model`

## Run sample image classification inference<a name="run-sample-sme-image-classification-inference"></a>

To run image classification inference using the AWS\-provided sample components and the SageMaker Edge Manager agent, you must deploy these components to your core device\. Deploying these components downloads a SageMaker Neo\-compiled pre\-trained Resnet\-50 model and installs the SageMaker Edge Manager agent on your device\. The SageMaker Edge Manager agent loads the model and publishes inference results on the `gg/sageMakerEdgeManager/image-classification` topic\. To view these inference results, use the AWS IoT MQTT client in the AWS IoT console to subscribe to this topic\. 

**Topics**
+ [Subscribe to the notifications topic](#sme-image-classification-subscribe)
+ [Deploy the sample components](#sme-image-classification-deploy)
+ [View inference results](#sme-image-classification-inference-results)

### Subscribe to the notifications topic<a name="sme-image-classification-subscribe"></a>

In this step, you configure the AWS IoT MQTT client in the AWS IoT console to watch MQTT messages published by the sample inference component\. By default, the component publishes inference results on the `gg/sageMakerEdgeManager/image-classification` topic\. Subscribe to this topic before you deploy the component to your Greengrass core device to see the inference results when the component runs for the first time\. 



**To subscribe to the default notifications topic**

1. In the [AWS IoT console](https://console.aws.amazon.com/iot/) navigation menu, choose **Test, MQTT test client**\.

1. Under **Subscribe to a topic**, in the **Topic name** box, enter **gg/sageMakerEdgeManager/image\-classification**\.

1. Choose **Subscribe**\.

### Deploy the sample components<a name="sme-image-classification-deploy"></a>

In this step, you configure and deploy the following components to your core device:
+  `aws.greengrass.SageMakerEdgeManager` 
+  `com.greengrass.SageMakerEdgeManager.ImageClassification` 
+  `com.greengrass.SageMakerEdgeManager.ImageClassification.Model` 

#### To deploy your components \(console\)<a name="sme-image-classification-deploy-console"></a>

1. In the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Deployments**, and then choose the deployment for your target device that you want to revise\.

1. On the deployment page, choose **Revise**, and then choose **Revise deployment**\.

1. On the **Specify target** page, choose **Next**\.

1. On the **Select components** page, do the following: 

   1. Under **My components**, select the following components:
      +  `com.greengrass.SageMakerEdgeManager.ImageClassification`
      + `com.greengrass.SageMakerEdgeManager.ImageClassification.Model` 

   1. Under **Public components**, turn off the **Show only selected components** toggle, and then select the `aws.greengrass.SageMakerEdgeManager` component\. 

   1. Choose **Next**\.

1. On the **Configure components** page, select the `aws.greengrass.SageMakerEdgeManager` component and do the following\.

   1. Choose **Configure component**\.

   1. Under **Configuration update**, in **Configuration to merge**, enter the following configuration\.

      ```
      {
          "DeviceFleetName": "device-fleet-name",
          "BucketName": "DOC-EXAMPLE-BUCKET"
      }
      ```

      Replace *device\-fleet\-name* with the name of the edge device fleet that you created, and replace **DOC\-EXAMPLE\-BUCKET** with the name of the S3 bucket that is associated with your device fleet\.

   1. Choose **Confirm**, and then choose **Next**\.

1. On the **Configure advanced settings** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, choose **Deploy**

#### To deploy your components \(AWS CLI\)<a name="sme-image-classification-deploy-cli"></a>

1. On your development computer, create a `deployment.json` file to define the deployment configuration for your SageMaker Edge Manager components\. This file should look like the following example\.

   ```
   {
     "targetArn":"targetArn",
     "components": {
       "aws.greengrass.SageMakerEdgeManager": {
         "componentVersion": 1.0.x,
         "configurationUpdate": {
           "merge": {
             "DeviceFleetName": "device-fleet-name",
             "BucketName": "DOC-EXAMPLE-BUCKET2"
           }
         }
       },
       "com.greengrass.SageMakerEdgeManager.ImageClassification": {
         "componentVersion": 1.0.x,
         "configurationUpdate": {
         }
       }, 
       "com.greengrass.SageMakerEdgeManager.ImageClassification.Model": {
         "componentVersion": 1.0.x,
         "configurationUpdate": {
         }
       }, 
     }
   }
   ```
   + In the `targetArn` field, replace `targetArn` with the Amazon Resource Name \(ARN\) of the thing or thing group to target for the deployment, in the following format: 
     + Thing: `arn:aws:iot:region:account-id:thing/thingName`
     + Thing group: `arn:aws:iot:region:account-id:thinggroup/thingGroupName`
   + In the `merge` field, replace *device\-fleet\-name* with the name of the edge device fleet that you created\. Then, replace **DOC\-EXAMPLE\-BUCKET2** with the name of the S3 bucket that is associated with your device fleet\.
   + Replace the component versions for each component with the latest available version\.

1. Run the following command to deploy the components on the device:

   ```
   $ aws greengrassv2 create-deployment \
       --cli-input-json file://path/to/deployment.json
   ```

The deployment can take several minutes to complete\. In the next step, check the component log to verify that the deployment completed successfully and to view the inference results\.

### View inference results<a name="sme-image-classification-inference-results"></a>

After you deploy the components, you can view the inference results in the component log on your Greengrass core device and in the AWS IoT MQTT client in the AWS IoT console\. To subscribe to the topic on which the component publishes inference results, see [Subscribe to the notifications topic](#sme-image-classification-subscribe)\.
+ **AWS IoT MQTT client**—To view the results that the inference component publishes on the [default notifications topic](#sme-image-classification-subscribe), complete the following steps:

  1. In the [AWS IoT console](https://console.aws.amazon.com/iot/) navigation menu, choose **Test, MQTT test client**\.

  1. Under **Subscriptions**, choose **gg/sageMakerEdgeManager/image\-classification**\.

   
+ **Component log**—To view the inference results in the component log, run the following command on your Greengrass core device\. 

  ```
  sudo tail -f /greengrass/v2/logs/com.greengrass.SageMakerEdgeManager.ImageClassification.log
  ```

If you can't see inference results in the component log or in the MQTT client, the deployment failed or didn't reach the core device\. This can occur if your core device isn't connected to the internet or doesn't have the right permissions to run the component\. Run the following command on your core device to view the AWS IoT Greengrass Core software log file\. This file includes logs from the Greengrass core device's deployment service\.

```
sudo tail -f /greengrass/v2/logs/greengrass.log
```

For more information, see [Troubleshooting machine learning inference](ml-troubleshooting.md)\.