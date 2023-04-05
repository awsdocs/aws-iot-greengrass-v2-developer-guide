# Test AWS IoT Greengrass components with local deployments<a name="test-components"></a>

If you develop a Greengrass component on a core device, you can create a local deployment to install and test it\. Follow the steps in this section to create a local deployment\.

If you develop the component on a different computer, such as a local development computer, you can't create a local deployment\. Instead, publish the component to the AWS IoT Greengrass service so that you can deploy it to Greengrass core devices to test it\. For more information, see [Publish components to deploy to your core devices](publish-components.md) and [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

**To test a component on an Greengrass core device**

1. The core device logs events such as component updates\. You can view this log file to discover and troubleshoot errors with your component, such as an invalid recipe\. This log file also displays messages that your component prints to standard out \(stdout\)\. We recommend that you open an additional terminal session on your core device to observe new log messages in real time\. Open a new terminal session, such as through SSH, and run the following command to view the logs\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/greengrass.log
   ```

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2\logs\greengrass.log -Tail 10 -Wait
   ```

------

   You can also view the log file for your component\.

------
#### [ Linux or Unix ]

   ```
   sudo tail -f /greengrass/v2/logs/com.example.HelloWorld.log
   ```

------
#### [ PowerShell ]

   ```
   gc C:\greengrass\v2\logs\com.example.HelloWorld.log -Tail 10 -Wait
   ```

------

1. In your original terminal session, run the following command to update the core device with your component\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder, and replace *\~/greengrassv2* with the path to your local development folder\.

------
#### [ Linux or Unix ]

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment create \
     --recipeDir ~/greengrassv2/recipes \
     --artifactDir ~/greengrassv2/artifacts \
     --merge "com.example.HelloWorld=1.0.0"
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment create ^
     --recipeDir %USERPROFILE%\greengrassv2\recipes ^
     --artifactDir %USERPROFILE%\greengrassv2\artifacts ^
     --merge "com.example.HelloWorld=1.0.0"
   ```

------
#### [ PowerShell ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment create `
     --recipeDir ~/greengrassv2/recipes `
     --artifactDir ~/greengrassv2/artifacts `
     --merge "com.example.HelloWorld=1.0.0"
   ```

------
**Note**  
You can also use the `greengrass-cli deployment create` command to set the value of your component's configuration parameters\. For more information, see [create](gg-cli-deployment.md#deployment-create)\.

1. Use the `greengrass-cli deployment status` command to monitor the progress of your component's deployment\. 

------
#### [ Unix or Linux ]

   ```
   sudo /greengrass/v2/bin/greengrass-cli deployment status \
     -i deployment-id
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment status ^
     -i deployment-id
   ```

------
#### [ PowerShell ]

   ```
   C:\greengrass\v2\bin\greengrass-cli deployment status `
     -i deployment-id
   ```

------

1. Test your component as it runs on the Greengrass core device\. When you finish this version of your component, you can upload it to the AWS IoT Greengrass service\. Then, you can deploy the component to other core devices\. For more information, see [Publish components to deploy to your core devices](publish-components.md)\.