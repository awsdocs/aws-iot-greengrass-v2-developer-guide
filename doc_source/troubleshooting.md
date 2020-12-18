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
+ [Error: Unable to connect to AWS IoT Core](#core-error-unable-to-connect-to-aws-iot)

### Unable to set up core device<a name="unable-to-set-up-core-device"></a>

If the AWS IoT Greengrass Core software installer fails and you aren't able to set up a core device, you might need to uninstall the software and try again\. For more information, see [Uninstall the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md#uninstall-greengrass-core-v2)\.

### Error: Unable to connect to AWS IoT Core<a name="core-error-unable-to-connect-to-aws-iot"></a>

You might see this error when the AWS IoT Greengrass Core software can't connect to AWS IoT Core to retrieve deployment jobs, for example\. Do the following:
+ Check that your core device can connect to the internet and AWS IoT Core\. For more information about the AWS IoT Core endpoint to which your device connects, see [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)\.
+ Check that your core device's AWS IoT thing uses a certificate that allows the `iot:Connect`, `iot:Publish`, `iot:Receive`, and `iot:Subscribe` permissions\.
+ If your core device uses a [network proxy](configure-greengrass-core-v2.md#configure-network-proxy), check that your core device has a [device role](device-service-role.md) and that its role allows the `iot:Connect`, `iot:Publish`, `iot:Receive`, and `iot:Subscribe` permissions\.

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