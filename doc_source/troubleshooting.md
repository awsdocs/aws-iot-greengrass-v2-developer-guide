# Troubleshooting<a name="troubleshooting"></a>

Use the troubleshooting information and solutions in this section to help resolve issues with AWS IoT Greengrass Version 2\.

**Topics**
+ [View AWS IoT Greengrass Core logs](#view-greengrass-core-logs)
+ [View component logs](#view-component-logs)
+ [AWS IoT Greengrass Core software issues](#greengrass-core-issues)
+ [Core device deployment issues](#greengrass-core-deployment-issues)

## View AWS IoT Greengrass Core logs<a name="view-greengrass-core-logs"></a>

The AWS IoT Greengrass Core log file provides real\-time information about the AWS IoT Greengrass Core\. This can help you identify issues with components and deployments\.

**To view the AWS IoT Greengrass Core log file**
+ Run the following command to view the log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/greengrass.log
  ```

## View component logs<a name="view-component-logs"></a>

Component log files provide real\-time information about a component that runs on the Greengrass core device\. This can help you identify issues with components\.

**To view the log file for a component**
+ Run the following command to view the log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder, and replace *com\.example\.HelloWorld* with the name of the component\.

  ```
  sudo tail -f /greengrass/v2/logs/com.example.HelloWorld.log
  ```

## AWS IoT Greengrass Core software issues<a name="greengrass-core-issues"></a>

Use the following information to troubleshoot AWS IoT Greengrass Core software issues\. Each entry corresponds to a log message that you might see on your core device\.

**Topics**
+ [Unable to set up core device](#unable-to-set-up-core-device)
+ [Unable to install AWS IoT Greengrass Core software on NVIDIA Jetson device](#unable-to-install-on-jetson-device)
+ [Error: Unable to connect to AWS IoT Core](#core-error-unable-to-connect-to-aws-iot)
+ [Out of memory error](#java-out-of-memory)

### Unable to set up core device<a name="unable-to-set-up-core-device"></a>

If the AWS IoT Greengrass Core software installer fails and you aren't able to set up a core device, you might need to uninstall the software and try again\. For more information, see [Uninstall the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md#uninstall-greengrass-core-v2)\.

### Unable to install AWS IoT Greengrass Core software on NVIDIA Jetson device<a name="unable-to-install-on-jetson-device"></a>

You might see the following error and stack trace when you install the AWS IoT Greengrass Core software on an NVIDIA Jetson device, such as the Jetson Nano\.

```
Error while trying to setup Greengrass Nucleus
java.lang.RuntimeException: Error setting up component default user / group
at com.aws.greengrass.easysetup.GreengrassSetup.setComponentDefaultUserAndGroup(GreengrassSetup.java:492)
at com.aws.greengrass.easysetup.GreengrassSetup.performSetup(GreengrassSetup.java:283)
at com.aws.greengrass.easysetup.GreengrassSetup.main(GreengrassSetup.java:242)
Caused by: java.io.IOException: Failed to add user to group , command : sudo dscl . -append /Groups/ggc_group GroupMembership ggc_user
at com.aws.greengrass.util.platforms.unix.UnixPlatform.runCmd(UnixPlatform.java:433)
at com.aws.greengrass.util.platforms.unix.DarwinPlatform.addUserToGroup(DarwinPlatform.java:48)
at com.aws.greengrass.easysetup.GreengrassSetup.setComponentDefaultUserAndGroup(GreengrassSetup.java:484)
... 2 more
Caused by: java.io.IOException: Failed to add user to group - command: sudo dscl . -append /Groups/g
```

To resolve this issue, create and use a custom system user and group other than `ggc_user` and `ggc_group`\. The AWS IoT Greengrass Core software runs components as this user and group by default\. 

**To create and use a custom system user and group**

1. Run the following commands to create a user, create a group, and add the user to the group\. Replace *my\_ggc\_user* with a user name, and replace *my\_ggc\_group* with a group name\.

   ```
   sudo useradd -r -m my_ggc_user
   sudo groupadd -r my_ggc_group
   sudo usermod -a -G my_ggc_group my_ggc_user
   ```

1. Run the AWS IoT Greengrass Core software installer by using the following argument, so that you can use the custom user and group\.

   ```
   --component-default-user my_ggc_user:my_ggc_group
   ```

   For more information, see [Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\.

### Error: Unable to connect to AWS IoT Core<a name="core-error-unable-to-connect-to-aws-iot"></a>

You might see this error when the AWS IoT Greengrass Core software can't connect to AWS IoT Core to retrieve deployment jobs, for example\. Do the following:
+ Check that your core device can connect to the internet and AWS IoT Core\. For more information about the AWS IoT Core endpoint to which your device connects, see [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)\.
+ Check that your core device's AWS IoT thing uses a certificate that allows the `iot:Connect`, `iot:Publish`, `iot:Receive`, and `iot:Subscribe` permissions\.
+ If your core device uses a [network proxy](configure-greengrass-core-v2.md#configure-network-proxy), check that your core device has a [device role](device-service-role.md) and that its role allows the `iot:Connect`, `iot:Publish`, `iot:Receive`, and `iot:Subscribe` permissions\.

### Out of memory error<a name="java-out-of-memory"></a>

This error typically occurs if your device doesn't have sufficient memory to allocate an object in the Java heap\. On devices with limited memory, you might need to specify a maximum heap size to control memory allocation\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

## Core device deployment issues<a name="greengrass-core-deployment-issues"></a>

Use the following information to troubleshoot deployment issues on Greengrass core devices\. Each entry corresponds to a log message that you might see on your core device\.

**Topics**
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.PackageDownloadException: Failed to download artifact](#core-error-failed-to-download-artifact-package-download-exception)
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.ArtifactChecksumMismatchException: Failed to download artifact](#core-error-failed-to-download-artifact-checksum-mismatch-exception)

### Error: com\.aws\.greengrass\.componentmanager\.exceptions\.PackageDownloadException: Failed to download artifact<a name="core-error-failed-to-download-artifact-package-download-exception"></a>

You might see this error when the AWS IoT Greengrass Core software fails to download a component artifact when the core device applies a deployment\. The deployment fails as a result of this error\.

When you receive this error, the log also includes a stack trace that you can use to identify the specific issue\. Each of the following entries corresponds to a message that you might see in the stack trace of the `Failed to download artifact` error message\.

#### software\.amazon\.awssdk\.services\.s3\.model\.S3Exception: null \(Service: S3, Status Code: 403, Request ID: null, \.\.\.\)<a name="core-error-failed-to-download-artifact-s3-permissions"></a>

The [PackageDownloadException error](#core-error-failed-to-download-artifact-package-download-exception) might include this stack trace in the following cases:
+ The component artifact isn't available at the Amazon S3 URL that you specify in the component's recipe\. Check that you uploaded the artifact to the S3 bucket and that the artifact URL matches the Amazon S3 URL of the artifact in the bucket\.
+ The [core device role](device-service-role.md) doesn't allow the AWS IoT Greengrass Core software to download the component artifact from the Amazon S3 URL that you specify in the component's recipe\. Check that the device role allows `s3:GetObject` for the Amazon S3 URL where the artifact is available\.

### Error: com\.aws\.greengrass\.componentmanager\.exceptions\.ArtifactChecksumMismatchException: Failed to download artifact<a name="core-error-failed-to-download-artifact-checksum-mismatch-exception"></a>

You might see this error when the AWS IoT Greengrass Core software fails to download a component artifact when the core device applies a deployment\. The deployment fails as a result of this error\.

This error indicates that the downloaded artifact file's checksum doesn't match the checksum that AWS IoT Greengrass calculated when you created the component\. You might see this error in the following cases:
+ Check if the artifact file changed in the S3 bucket where you host it\. If the file changed since you created the component, restore it to the previous version that the core device expects\. If you can't restore the file to its previous version, create a new version of the component with the artifact file to deploy\.
+ Check your core device's internet connection\. This error can occur if the artifact file becomes corrupted while it downloads\. Create a new deployment to try again\.