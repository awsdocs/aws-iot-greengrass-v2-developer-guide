# Uninstall the AWS IoT Greengrass Core software<a name="uninstall-greengrass-core-v2"></a>

You can uninstall the AWS IoT Greengrass Core software to remove it from a device that you don't want to use as a Greengrass core device\. You can also use these steps to clean up an installation that fails\.

**To uninstall the AWS IoT Greengrass Core software**

1. If you run the software as a system service, you must stop, disable, and remove the service\. Run the following commands as appropriate for your operating system\.

------
#### [ Linux ]

   1. Stop the service\.

      ```
      sudo systemctl stop greengrass.service
      ```

   1. Disable the service\.

      ```
      sudo systemctl disable greengrass.service
      ```

   1. Remove the service\.

      ```
      sudo rm /etc/systemd/system/greengrass.service
      ```

   1. Verify that the service is deleted\.

      ```
      sudo systemctl daemon-reload && sudo systemctl reset-failed
      ```

------
#### [ Windows \(Command Prompt\) ]

**Note**  
You must run Command Prompt as an administrator to run these commands\.

   1. Stop the service\.

      ```
      sc stop "greengrass"
      ```

   1. Disable the service\.

      ```
      sc config "greengrass" start=disabled
      ```

   1. Remove the service\.

      ```
      sc delete "greengrass"
      ```

   1. Restart the device\.

------
#### [ Windows \(PowerShell\) ]

**Note**  
You must run PowerShell as an administrator to run these commands\.

   1. Stop the service\.

      ```
      Stop-Service -Name "greengrass"
      ```

   1. Disable the service\.

      ```
      Set-Service -Name "greengrass" -Status stopped -StartupType disabled
      ```

   1. Remove the service\.
      + For PowerShell 6\.0 and later:

        ```
        Remove-Service -Name "greengrass" -Confirm:$false -Verbose
        ```
      + For PowerShell versions earlier than 6\.0 :

        ```
        Get-Item HKLM:\SYSTEM\CurrentControlSet\Services\greengrass | Remove-Item -Force -Verbose
        ```

   1. Restart the device\.

------

1. Remove the root folder from the device\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the root folder\.

------
#### [ Linux ]

   ```
   sudo rm -rf /greengrass/v2
   ```

------
#### [ Windows \(Command Prompt\) ]

   ```
   rmdir /s /q C:\greengrass\v2
   ```

------
#### [ Windows \(PowerShell\) ]

   ```
   Remove-Item -Path C:\greengrass\v2 -Recurse -Force
   ```

------

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