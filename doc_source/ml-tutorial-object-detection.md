# Tutorial: Perform object detection<a name="ml-tutorial-object-detection"></a>

This tutorial shows you how to perform object detection using the public DLR Object Detection machine learning inference component\.

## Prerequisites<a name="ml-tutorial-object-detection-prereqs"></a>

To complete this tutorial, you will need the following:
+ One of the following devices, set up and configured for use with AWS IoT Greengrass V2:<a name="dlr-supported-platforms"></a>

  This component requires one of the following devices configured for use with AWS IoT Greengrass\. For more information see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.
  + **32\-bit Armv7l**

    [Raspberry Pi](https://www.raspberrypi.org) running Raspberry Pi OS, 2020\-08\-24\.
  + **64\-bit x86\_64**

    Device running Amazon Linux 2 or Ubuntu 18\.04, with [GNU C Library](https://www.gnu.org/software/libc/) \(glibc\) version 2\.27 or later\.

   For more information, see [Setting up AWS IoT Greengrass core devices](setting-up.md)\.
+ A Greengrass core device and a Greengrass group\. For more information, see [Install the AWS IoT Greengrass Core software](getting-started.md#install-greengrass-v2)\.

## Deploy the DLR Object Detection component<a name="ml-object-detection-deploy"></a>

Complete the following steps to deploy the DLR Object Detection component to your core device:

### To deploy the DLR Object Detection component \(console\)<a name="ml-object-detection-deploy-console"></a>

1. In to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass) navigation menu, choose **Components**\.

1. On the **Components** page, on the **Public components** tab, choose `aws.greengrass.DLRObjectDetection`\.

1. On the **aws\.greengrass\.DLRObjectDetection** page, choose **Deploy**\.

1. <a name="add-deployment"></a>From **Add to deployment**, choose one of the following:

   1. To merge this component to an existing deployment on your target device, choose **Add to existing deployment** and then select the deployment that you want to revise\.

   1. To create a new deployment on your target device, then choose **Create new deployment**\. If you have an existing deployment on your device, then choosing this step will replace the existing deployment\. 

1. <a name="specify-deployment-target"></a>On the **Specify target** page, do the following: 

   1. Under **Deployment** information, enter or modify the friendly name for your deployment\.

   1. Under **Deployment targets**, select a target for your deployment, and choose **Next**\. You cannot change the deployment target if you are revising an existing deployment\.

1. On the **Select components** page, under **Public components**, verify that the `aws.greengrass.DLRObjectDetection` component is selected, and choose **Next**\.

1. On the **Configure components** page, keep the default configuration settings, and choose **Next**\.

1. On the **Configure advanced setting** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, choose **Deploy**

### To deploy the DLR Object Detection component \(AWS CLI\)<a name="ml-object-detection-deploy-cli"></a>

1. Create a `deployment.json` file to define the deployment configuration for the DLR Object Detection component\. This file should look like the following:

   ```
   {
     "targetArn":"targetArn",
     "components": {
       "aws.greengrass.DLRObjectDetection": {
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
   + In the `aws.greengrass.DLRObjectDetection` component object, specify values as follows:  
`version`  
The version of the DLR Object Detection component\.

1. Run the following command to deploy the DLR Object Detection component on the device:

   ```
   $ aws greengrassv2 create-deployment \
       --cli-input-json file://path/to/deployment.json
   ```

<a name="view-inference-logs"></a>For a successful deployment, inference logs are located in the `/greengrass/v2/work/greengrass_ml/inference_log/<model-name>` folder\. 