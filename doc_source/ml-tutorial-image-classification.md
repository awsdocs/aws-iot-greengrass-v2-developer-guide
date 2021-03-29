# Tutorial: Perform image classification<a name="ml-tutorial-image-classification"></a>

This tutorial shows you how to perform image classification inference with a one\-click deployment using the public [DLR Image Classification](dlr-image-classification-component.md) machine learning inference component\.

## Prerequisites<a name="ml-tutorial-prereqs"></a>

To complete this tutorial, you will need a Greengrass core device\.<a name="dlr-supported-platforms"></a>

The public machine learning inference components require a Greengrass core device running one of the following supported platforms\. For more information see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.
+ **32\-bit Armv7l**

  [Raspberry Pi](https://www.raspberrypi.org) running Raspberry Pi OS, 2020\-08\-24\.
+ **64\-bit x86\_64**

  Device running Amazon Linux 2 or Ubuntu 18\.04, with [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later\.

## Deploy the DLR Image Classification component<a name="ml-image-classification-deploy"></a>

Complete the following steps to deploy the DLR Image Classification component to your core device:

### To deploy the DLR Image Classification component \(console\)<a name="ml-image-classification-deploy-console"></a>

1. In to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

1. On the **Components** page, on the **Public components** tab, choose `aws.greengrass.DLRImageClassification`\.

1. On the **aws\.greengrass\.DLRImageClassification** page, choose **Deploy**\.

1. <a name="add-deployment"></a>From **Add to deployment**, choose one of the following:

   1. To merge this component to an existing deployment on your target device, choose **Add to existing deployment** and then select the deployment that you want to revise\.

   1. To create a new deployment on your target device, then choose **Create new deployment**\. If you have an existing deployment on your device, then choosing this step will replace the existing deployment\. 

1. <a name="specify-deployment-target"></a>On the **Specify target** page, do the following: 

   1. Under **Deployment** information, enter or modify the friendly name for your deployment\.

   1. Under **Deployment targets**, select a target for your deployment, and choose **Next**\. You cannot change the deployment target if you are revising an existing deployment\.

1. On the **Select components** page, under **Public components**, verify that the `aws.greengrass.DLRImageClassification` component is selected, and choose **Next**\.

1. On the **Configure components** page, keep the default configuration settings, and choose **Next**\.

1. On the **Configure advanced settings** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, choose **Deploy**

### To deploy the DLR Image Classification component \(AWS CLI\)<a name="ml-image-classification-deploy-cli"></a>

1. Create a `deployment.json` file to define the deployment configuration for the DLR Image Classification component\. This file should look like the following:

   ```
   {
     "targetArn":"targetArn",
     "components": {
       "aws.greengrass.DLRImageClassification": {
         "componentVersion": <latest-component-version>,
         "configurationUpdate": {
         }
       }
     }
   }
   ```
   + In the `targetArn` field, replace `targetArn` with the Amazon Resource Name \(ARN\) of the thing or thing group to target for the deployment, in the following format: 
     + Thing: `arn:aws:iot:region:account-id:thing/thingName`
     + Thing group: `arn:aws:iot:region:account-id:thinggroup/thingGroupName`
   + In the `aws.greengrass.DLRImageClassification` component object, specify values as follows:  
`version`  
The version of the DLR Image Classification component\.

1. Run the following command to deploy the DLR Image Classification component on the device:

   ```
   $ aws greengrassv2 create-deployment \
       --cli-input-json file://path/to/deployment.json
   ```

<a name="view-inference-logs"></a>For a successful deployment, inference logs are located in the `/greengrass/v2/work/greengrass_ml/inference_log/<model-name>` folder\. 