# Troubleshooting<a name="troubleshooting"></a>

Use the troubleshooting information and solutions in this section to help resolve issues with AWS IoT Greengrass Version 2\.

**Topics**
+ [View AWS IoT Greengrass Core logs](#view-greengrass-core-logs)
+ [View component logs](#view-component-logs)
+ [AWS IoT Greengrass Core software issues](#greengrass-core-issues)
+ [Core device deployment issues](#greengrass-core-deployment-issues)
+ [Core device component issues](#greengrass-core-component-issues)

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

Use the following information to troubleshoot AWS IoT Greengrass Core software issues\. 

**Topics**
+ [Unable to set up core device](#unable-to-set-up-core-device)
+ [Error: Unable to connect to AWS IoT Core](#core-error-unable-to-connect-to-aws-iot)
+ [Out of memory error](#java-out-of-memory)
+ [Unable to install Greengrass CLI](#unable-to-install-greengrass-cli)
+ [Failed to map segment from shared object: operation not permitted](#unable-to-start-greengrass)

### Unable to set up core device<a name="unable-to-set-up-core-device"></a>

If the AWS IoT Greengrass Core software installer fails and you aren't able to set up a core device, you might need to uninstall the software and try again\. For more information, see [Uninstall the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md#uninstall-greengrass-core-v2)\.

### Error: Unable to connect to AWS IoT Core<a name="core-error-unable-to-connect-to-aws-iot"></a>

You might see this error when the AWS IoT Greengrass Core software can't connect to AWS IoT Core to retrieve deployment jobs, for example\. Do the following:
+ Check that your core device can connect to the internet and AWS IoT Core\. For more information about the AWS IoT Core endpoint to which your device connects, see [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)\.
+ Check that your core device's AWS IoT thing uses a certificate that allows the `iot:Connect`, `iot:Publish`, `iot:Receive`, and `iot:Subscribe` permissions\.
+ If your core device uses a [network proxy](configure-greengrass-core-v2.md#configure-network-proxy), check that your core device has a [device role](device-service-role.md) and that its role allows the `iot:Connect`, `iot:Publish`, `iot:Receive`, and `iot:Subscribe` permissions\.

### Out of memory error<a name="java-out-of-memory"></a>

This error typically occurs if your device doesn't have sufficient memory to allocate an object in the Java heap\. On devices with limited memory, you might need to specify a maximum heap size to control memory allocation\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

### Unable to install Greengrass CLI<a name="unable-to-install-greengrass-cli"></a>

You might see the following console message when you use the `--deploy-dev-tools` argument in your installation command for AWS IoT Greengrass Core\.

```
Thing group exists, it could have existing deployment and devices, hence NOT creating deployment for Greengrass first party dev tools, please manually create a deployment if you wish to
```

This occurs when the Greengrass CLI component is not installed because your core device is a member of a thing group that has an existing deployment\. If you see this message, you can manually deploy the Greengrass CLI component \(`aws.greengrass.Cli`\) to the device to install the Greengrass CLI\. For more information, see [Install the Greengrass CLI](install-gg-cli.md)\.

### Failed to map segment from shared object: operation not permitted<a name="unable-to-start-greengrass"></a>

This error typically occurs when the AWS IoT Greengrass Core software fails to start because the `/tmp` directory is mounted with `noexec` permissions\.

Run the following command to remount the `/tmp` directory with `exec` permissions and try again\.

```
sudo mount -o remount,exec /tmp
```

## Core device deployment issues<a name="greengrass-core-deployment-issues"></a>

Use the following information to troubleshoot deployment issues on Greengrass core devices\. Each entry corresponds to a log message that you might see on your core device\.

**Topics**
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.PackageDownloadException: Failed to download artifact](#core-error-failed-to-download-artifact-package-download-exception)
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.ArtifactChecksumMismatchException: Failed to download artifact](#core-error-failed-to-download-artifact-checksum-mismatch-exception)
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.NoAvailableComponentVersionException: Failed to negotiate component <name> version with cloud and no local applicable version satisfying requirement <requirements>](#core-error-no-available-component-version)

### Error: com\.aws\.greengrass\.componentmanager\.exceptions\.PackageDownloadException: Failed to download artifact<a name="core-error-failed-to-download-artifact-package-download-exception"></a>

You might see this error when the AWS IoT Greengrass Core software fails to download a component artifact when the core device applies a deployment\. The deployment fails as a result of this error\.

When you receive this error, the log also includes a stack trace that you can use to identify the specific issue\. Each of the following entries corresponds to a message that you might see in the stack trace of the `Failed to download artifact` error message\.

#### software\.amazon\.awssdk\.services\.s3\.model\.S3Exception: null \(Service: S3, Status Code: 403, Request ID: null, \.\.\.\)<a name="core-error-failed-to-download-artifact-s3-permissions"></a>

The [PackageDownloadException error](#core-error-failed-to-download-artifact-package-download-exception) might include this stack trace in the following cases:
+ The component artifact isn't available at the Amazon S3 URL that you specify in the component's recipe\. Check that you uploaded the artifact to the S3 bucket and that the artifact URL matches the Amazon S3 URL of the artifact in the bucket\.
+ The [core device role](device-service-role.md) doesn't allow the AWS IoT Greengrass Core software to download the component artifact from the Amazon S3 URL that you specify in the component's recipe\. Check that the device role allows `s3:GetObject` for the Amazon S3 URL where the artifact is available\.

### Error: com\.aws\.greengrass\.componentmanager\.exceptions\.ArtifactChecksumMismatchException: Failed to download artifact<a name="core-error-failed-to-download-artifact-checksum-mismatch-exception"></a>

You might see this error when the AWS IoT Greengrass Core software fails to download a component artifact when the core device applies a deployment\. The deployment fails as a result of this error\.

This error indicates that the downloaded artifact file's checksum doesn't match the checksum that AWS IoT Greengrass calculated when you created the component\. Do the following:
+ Check if the artifact file changed in the S3 bucket where you host it\. If the file changed since you created the component, restore it to the previous version that the core device expects\. If you can't restore the file to its previous version, create a new version of the component with the artifact file to deploy\.
+ Check your core device's internet connection\. This error can occur if the artifact file becomes corrupted while it downloads\. Create a new deployment to try again\.

### Error: com\.aws\.greengrass\.componentmanager\.exceptions\.NoAvailableComponentVersionException: Failed to negotiate component <name> version with cloud and no local applicable version satisfying requirement <requirements><a name="core-error-no-available-component-version"></a>

You might see this error when a core device can't find a component version that meets the requirements of the deployments for that core device\. The core device checks for the component in the AWS IoT Greengrass service and on the local device\. The error message includes each deployment's target and that deployment's version requirements for the component\. The deployment target can be a thing, a thing group, or `LOCAL_DEPLOYMENT`, which represents the local deployment on the core device\.

This issue can occur in the following cases:
+ The core device is the target of multiple deployments that have conflicting component version requirements\. For example, the core device might be the target of multiple deployments that include a `com.example.HelloWorld` component, where one deployment requires version 1\.0\.0 and the other requires version 1\.0\.1\. It's impossible to have a component that meets both requirements, so the deployment fails\.
+ The component version doesn't exist in the AWS IoT Greengrass service or on the local device\. The component might have been deleted, for example\.
+ There exists component versions that meet the version requirements, but none are compatible with the core device's platform\.

To resolve this issue, revise the deployments to include compatible component versions or remove incompatible ones\. For more information about how to revise cloud deployments, see [Revise deployments](revise-deployments.md)\. For more information about how to revise local deployments, see the [AWS IoT Greengrass CLI deployment create](gg-cli-deployment.md#deployment-create) command\.

## Core device component issues<a name="greengrass-core-component-issues"></a>

Use the following information to troubleshoot Greengrass component issues on core devices\.

**Topics**
+ [Python script doesn't log messages](#python-component-no-log-output)

### Python script doesn't log messages<a name="python-component-no-log-output"></a>

Greengrass core devices collect logs that you can use to identify issues with components\. If your Python script's `stdout` and `stderr` messages don't appear in your component logs, you might need to flush the buffer or disable buffering for these standard output streams in Python\. Do any of the following:
+ Run Python with the [\-u](https://docs.python.org/3/using/cmdline.html#cmdoption-u) argument to disable buffering on `stdout` and `stderr`\.

  ```
  python3 -u hello_world.py
  ```
+ Use [Setenv](component-recipe-reference.md#lifecycle-setenv-definition) in your component's recipe to set the [PYTHONUNBUFFERED](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONUNBUFFERED) environment variable to a non\-empty string\. This environment variable disables buffering on `stdout` and `stderr`\.
+ Flush the buffer for the `stdout` or `stderr` streams\. Do one of the following:
  + Flush a message when you print\.

    ```
    import sys
    
    print('Hello, error!', file=sys.stderr, flush=True)
    ```
  + Flush a message after you print\. You can send multiple messages before you flush the stream\.

    ```
    import sys
    
    print('Hello, error!', file=sys.stderr)
    sys.stderr.flush()
    ```

For more information about how to verify that your Python script outputs log messages, see [View component logs](#view-component-logs)\.