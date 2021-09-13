# Uninstall the AWS IoT Greengrass Core software<a name="uninstall-greengrass-core-v2"></a>

You can uninstall the AWS IoT Greengrass Core software to remove it from a device that you don't want to use as a Greengrass core device\. You might also do this to clean up an installation that fails\.

**To uninstall the AWS IoT Greengrass Core software**

1. If you run the software as a system service, you must stop, disable, and remove the service\. Run the following commands\.

   ```
   sudo systemctl stop greengrass.service && sudo systemctl disable greengrass.service
   sudo rm /etc/systemd/system/greengrass.service
   sudo systemctl daemon-reload && sudo systemctl reset-failed
   ```

1. Remove the root folder from the device\. Replace */greengrass/v2* with the path to the root folder\.

   ```
   sudo rm -rf /greengrass/v2
   ```

1. Delete the core device from the AWS IoT Greengrass service\. This step removes the core device's status information from the AWS Cloud\. Be sure to complete this step if you plan to reinstall the AWS IoT Greengrass Core software to a core device with the same name\.
   + To delete a core device from the AWS IoT Greengrass console, do the following:

     1. <a name="navigate-greengrass-console"></a>Navigate to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\.

     1. Choose **Core devices**\.

     1. Choose the core device to delete\.

     1. Choose **Delete**\.

     1. In the confirmation modal, choose **Delete**\.
   + To delete a core device with the AWS Command Line Interface, use the [DeleteCoreDevice](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_DeleteCoreDevice.html) operation\. Run the following command, and replace *MyGreengrassCore* with the name of the core device\.

     ```
     aws greengrassv2 delete-core-device --core-device-thing-name MyGreengrassCore
     ```