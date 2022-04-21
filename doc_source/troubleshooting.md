# Troubleshooting AWS IoT Greengrass V2<a name="troubleshooting"></a>

Use the troubleshooting information and solutions in this section to help resolve issues with AWS IoT Greengrass Version 2\.

**Topics**
+ [View AWS IoT Greengrass Core software and component logs](#troubleshoot-with-logs)
+ [AWS IoT Greengrass Core software issues](#greengrass-core-issues)
+ [AWS IoT Greengrass cloud issues](#greengrass-cloud-issues)
+ [Core device deployment issues](#greengrass-core-deployment-issues)
+ [Core device component issues](#greengrass-core-component-issues)
+ [AWS Command Line Interface issues](#aws-cli-issues)

## View AWS IoT Greengrass Core software and component logs<a name="troubleshoot-with-logs"></a>

The AWS IoT Greengrass Core software writes logs to the local file system that you can use to view real\-time information about the core device\. You can also configure core devices to write logs to CloudWatch Logs, so you can remotely troubleshoot core devices\. These logs can help you identify issues with components, deployments, and core devices\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

## AWS IoT Greengrass Core software issues<a name="greengrass-core-issues"></a>

Troubleshoot AWS IoT Greengrass Core software issues\. 

**Topics**
+ [Unable to set up core device](#unable-to-set-up-core-device)
+ [Unable to set up nucleus as a system service](#unable-to-set-up-system-service)
+ [Unable to connect to AWS IoT Core](#core-error-unable-to-connect-to-aws-iot)
+ [Out of memory error](#java-out-of-memory)
+ [Unable to install Greengrass CLI](#unable-to-install-greengrass-cli)
+ [User root is not allowed to execute](#user-not-allowed-to-execute)
+ [Failed to map segment from shared object: operation not permitted](#unable-to-start-greengrass)
+ [software\.amazon\.awssdk\.services\.iam\.model\.IamException: The security token included in the request is invalid](#error-invalid-security-token)
+ [Error: com\.aws\.greengrass\.security\.provider\.pkcs11\.PKCS11CryptoKeyService: Private key or certificate with label <label> does not exist](#pkcs11-provider-error-private-key-or-certificate-does-not-exist)

### Unable to set up core device<a name="unable-to-set-up-core-device"></a>

If the AWS IoT Greengrass Core software installer fails and you aren't able to set up a core device, you might need to uninstall the software and try again\. For more information, see [Uninstall the AWS IoT Greengrass Core software](uninstall-greengrass-core-v2.md)\.

### Unable to set up nucleus as a system service<a name="unable-to-set-up-system-service"></a>

You might see this error when the AWS IoT Greengrass Core software installer fails to set up AWS IoT Greengrass as a system service\. On Linux devices, this error typically occurs if the core device doesn't have the [systemd](https://en.wikipedia.org/wiki/Systemd) init system\. The installer can successfully set up the AWS IoT Greengrass Core software even if it fails to set up the system service\.

Do one of the following:
+ Configure and run the AWS IoT Greengrass Core software as a system service\. You must configure the software as a system service to use all of the features of AWS IoT Greengrass\. You can install [systemd](https://en.wikipedia.org/wiki/Systemd) or use a different init system\. For more information, see [Configure the Greengrass nucleus as a system service](configure-greengrass-core-v2.md#configure-system-service)\.
+ Run the AWS IoT Greengrass Core software without a system service\. You can run the software using a loader script that the installer sets up in the Greengrass root folder\. For more information, see [Run the AWS IoT Greengrass Core software without a system service](run-greengrass-core-v2.md#run-greengrass-core-no-system-service)\.

### Unable to connect to AWS IoT Core<a name="core-error-unable-to-connect-to-aws-iot"></a>

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

### User root is not allowed to execute<a name="user-not-allowed-to-execute"></a>

You might see this error when the user that runs the AWS IoT Greengrass Core software \(typically `root`\) doesn't have permission to run `sudo` with any user and any group\. For the default `ggc_user` system user, this error looks like the following:

```
Sorry, user root is not allowed to execute <command> as ggc_user:ggc_group.
```

Check that your `/etc/sudoers` file gives the user permission to run `sudo` as other groups\. The permission for the user in `/etc/sudoers` should look like the following example\.

```
root    ALL=(ALL:ALL) ALL
```

### Failed to map segment from shared object: operation not permitted<a name="unable-to-start-greengrass"></a>

This error typically occurs when the AWS IoT Greengrass Core software fails to start because the `/tmp` directory is mounted with `noexec` permissions\.

Run the following command to remount the `/tmp` directory with `exec` permissions and try again\.

```
sudo mount -o remount,exec /tmp
```

### software\.amazon\.awssdk\.services\.iam\.model\.IamException: The security token included in the request is invalid<a name="error-invalid-security-token"></a>

You might see this error when you [install the AWS IoT Greengrass Core software with automatic provisioning](quick-installation.md), and the installer uses an AWS session token that isn't valid\. Do the following:
+ If you use temporary security credentials, check that the session token is correct and that you copy and paste the complete session token\.
+ If you use long\-term security credentials, check that the device doesn't have a session token from a time where you previously used temporary credentials\. Do the following:

  1. Run the following command to unset the session token environment variable\.

------
#### [ Linux or Unix ]

     ```
     unset AWS_SESSION_TOKEN
     ```

------
#### [ Windows Command Prompt \(CMD\) ]

     ```
     set AWS_SESSION_TOKEN=
     ```

------
#### [ PowerShell ]

     ```
     Remove-Item Env:\AWS_SESSION_TOKEN
     ```

------

  1. Check if the AWS credentials file, `~/.aws/credentials`, contains a session token, `aws_session_token`\. If so, remove that line from the file\.

     ```
     aws_session_token = AQoEXAMPLEH4aoAH0gNCAPyJxz4BlCFFxWNE1OPTgk5TthT+FvwqnKwRcOIfrRh3c/LTo6UDdyJwOOvEVPvLXCrrrUtdnniCEXAMPLE/IvU1dYUg2RVAJBanLiHb4IgRmpRV3zrkuWJOgQs8IZZaIv2BXIa2R4Olgk
     ```

You can also install the AWS IoT Greengrass Core software without providing AWS credentials\. For more information, see [Install AWS IoT Greengrass Core software with manual resource provisioning](manual-installation.md) or [Install AWS IoT Greengrass Core software with AWS IoT fleet provisioning](fleet-provisioning.md)\.

### Error: com\.aws\.greengrass\.security\.provider\.pkcs11\.PKCS11CryptoKeyService: Private key or certificate with label <label> does not exist<a name="pkcs11-provider-error-private-key-or-certificate-does-not-exist"></a>

This error occurs when the [PKCS\#11 provider component](pkcs11-provider-component.md) can't find or load the private key or certificate that you specify when you configure the AWS IoT Greengrass Core software to use a [hardware security module \(HSM\)](hardware-security.md)\. Do the following:
+ Check that the private key and certificate are stored in the HSM using the slot, user PIN, and object label that you configure the AWS IoT Greengrass Core software to use\.
+ Check that the private key and certificate use the same object label in the HSM\.
+ If your HSM supports object IDs, check that the private key and certificate use the same object ID in the HSM\.

Check the documentation for your HSM to learn how to query details about the security tokens in the HSM\. If you need to change the slot, object label, or object ID for a security token, check the documentation for your HSM to learn how to do so\.

## AWS IoT Greengrass cloud issues<a name="greengrass-cloud-issues"></a>

Use the following information to troubleshoot issues with the AWS IoT Greengrass console and API\. Each entry corresponds to an error message that you might see when you perform an action\.

### An error occurred \(AccessDeniedException\) when calling the CreateComponentVersion operation: User: arn:aws:iam::123456789012:user/<username> is not authorized to perform: null<a name="cloud-error-create-component-version-not-authorized-to-perform-null"></a>

You might see this error when you create a component version from the AWS IoT Greengrass console or with the [CreateComponentVersion](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateComponentVersion.html) operation\.

This error indicates that your recipe isn't valid JSON or YAML\. Check the syntax of your recipe, fix any syntax issues, and try again\. You can use an online JSON or YAML syntax checker to identify syntax issues in your recipe\.

## Core device deployment issues<a name="greengrass-core-deployment-issues"></a>

Troubleshoot deployment issues on Greengrass core devices\. Each entry corresponds to a log message that you might see on your core device\.

**Topics**
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.PackageDownloadException: Failed to download artifact](#core-error-failed-to-download-artifact-package-download-exception)
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.ArtifactChecksumMismatchException: Integrity check for downloaded artifact failed\. Probably due to file corruption\.](#core-error-failed-to-download-artifact-checksum-mismatch-exception)
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.NoAvailableComponentVersionException: Failed to negotiate component <name> version with cloud and no local applicable version satisfying requirement <requirements>](#core-error-no-available-component-version)
+ [software\.amazon\.awssdk\.services\.secretsmanager\.model\.SecretsManagerException: User: <user> is not authorized to perform: secretsmanager:GetSecretValue on resource: <arn>](#secret-manager-error-not-authorized-to-perform-get-secret-value)
+ [Info: com\.aws\.greengrass\.deployment\.exceptions\.RetryableDeploymentDocumentDownloadException: Greengrass Cloud Service returned an error when getting full deployment configuration](#core-error-getting-full-deployment-configuration)
+ [Warn: com\.aws\.greengrass\.deployment\.DeploymentService: Failed to get thing group hierarchy](#core-warning-failed-to-get-thing-group-hierarchy)
+ [Info: com\.aws\.greengrass\.deployment\.DeploymentDocumentDownloader: Calling Greengrass cloud to get full deployment configuration](#core-info-repetitive-get-full-deployment-configuration)

### Error: com\.aws\.greengrass\.componentmanager\.exceptions\.PackageDownloadException: Failed to download artifact<a name="core-error-failed-to-download-artifact-package-download-exception"></a>

You might see this error when the AWS IoT Greengrass Core software fails to download a component artifact when the core device applies a deployment\. The deployment fails as a result of this error\.

When you receive this error, the log also includes a stack trace that you can use to identify the specific issue\. Each of the following entries corresponds to a message that you might see in the stack trace of the `Failed to download artifact` error message\.

#### software\.amazon\.awssdk\.services\.s3\.model\.S3Exception: null \(Service: S3, Status Code: 403, Request ID: null, \.\.\.\)<a name="core-error-failed-to-download-artifact-s3-permissions"></a>

The [PackageDownloadException error](#core-error-failed-to-download-artifact-package-download-exception) might include this stack trace in the following cases:
+ The component artifact isn't available at the Amazon S3 URL that you specify in the component's recipe\. Check that you uploaded the artifact to the S3 bucket and that the artifact URL matches the Amazon S3 URL of the artifact in the bucket\.
+ The [core device role](device-service-role.md) doesn't allow the AWS IoT Greengrass Core software to download the component artifact from the Amazon S3 URL that you specify in the component's recipe\. Check that the device role allows `s3:GetObject` for the Amazon S3 URL where the artifact is available\.

### Error: com\.aws\.greengrass\.componentmanager\.exceptions\.ArtifactChecksumMismatchException: Integrity check for downloaded artifact failed\. Probably due to file corruption\.<a name="core-error-failed-to-download-artifact-checksum-mismatch-exception"></a>

You might see this error when the AWS IoT Greengrass Core software fails to download a component artifact when the core device applies a deployment\. The deployment fails because the downloaded artifact file's checksum doesn't match the checksum that AWS IoT Greengrass calculated when you created the component\.

Do the following:
+ Check if the artifact file changed in the S3 bucket where you host it\. If the file changed since you created the component, restore it to the previous version that the core device expects\. If you can't restore the file to its previous version, or if you want to use the new version of the file, create a new version of the component with the artifact file\.
+ Check your core device's internet connection\. This error can occur if the artifact file becomes corrupted while it downloads\. Create a new deployment and try again\.

### Error: com\.aws\.greengrass\.componentmanager\.exceptions\.NoAvailableComponentVersionException: Failed to negotiate component <name> version with cloud and no local applicable version satisfying requirement <requirements><a name="core-error-no-available-component-version"></a>

You might see this error when a core device can't find a component version that meets the requirements of the deployments for that core device\. The core device checks for the component in the AWS IoT Greengrass service and on the local device\. The error message includes each deployment's target and that deployment's version requirements for the component\. The deployment target can be a thing, a thing group, or `LOCAL_DEPLOYMENT`, which represents the local deployment on the core device\.

This issue can occur in the following cases:
+ The core device is the target of multiple deployments that have conflicting component version requirements\. For example, the core device might be the target of multiple deployments that include a `com.example.HelloWorld` component, where one deployment requires version 1\.0\.0 and the other requires version 1\.0\.1\. It's impossible to have a component that meets both requirements, so the deployment fails\.
+ The component version doesn't exist in the AWS IoT Greengrass service or on the local device\. The component might have been deleted, for example\.
+ There exists component versions that meet the version requirements, but none are compatible with the core device's platform\.
+ The core device's AWS IoT policy doesn't grant the `greengrass:ResolveComponentCandidates` permission\. Look for `Status Code: 403` in the error log to identify this issue\. To resolve this issue, add the `greengrass:ResolveComponentCandidates` permission to the core device's AWS IoT policy\. For more information, see [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy)\.

To resolve this issue, revise the deployments to include compatible component versions or remove incompatible ones\. For more information about how to revise cloud deployments, see [Revise deployments](revise-deployments.md)\. For more information about how to revise local deployments, see the [AWS IoT Greengrass CLI deployment create](gg-cli-deployment.md#deployment-create) command\.

### software\.amazon\.awssdk\.services\.secretsmanager\.model\.SecretsManagerException: User: <user> is not authorized to perform: secretsmanager:GetSecretValue on resource: <arn><a name="secret-manager-error-not-authorized-to-perform-get-secret-value"></a>

This error can occur when you use the [secret manager component](secret-manager-component.md) to deploy an AWS Secrets Manager secret\. If the core device's [token exchange IAM role](device-service-role.md) doesn't grant permission to get the secret, the deployment fails and the Greengrass logs include this error\.

**To authorize a core device to download a secret**

1. Add the `secretsmanager:GetSecretValue` permission to the core device's token exchange role\. The following example policy statement grants permission to get the value of a secret\.

   ```
   {
       "Effect": "Allow",
       "Action": [
           "secretsmanager:GetSecretValue"
       ],
       "Resource": [
           "arn:aws:secretsmanager:us-west-2:123456789012:secret:MyGreengrassSecret-abcdef"
       ]
   }
   ```

   For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

1. Reapply the deployment to the core device\. Do one of the following:
   + Revise the deployment without any changes\. The core device tries to download the secret again when it receives the revised deployment\. For more information, see [Revise deployments](revise-deployments.md)\.
   + Restart the AWS IoT Greengrass Core software to retry the deployment\. For more information, see [Run the AWS IoT Greengrass Core software](run-greengrass-core-v2.md)

   The deployment succeeds if secret manager downloads the secret successfully\.

### Info: com\.aws\.greengrass\.deployment\.exceptions\.RetryableDeploymentDocumentDownloadException: Greengrass Cloud Service returned an error when getting full deployment configuration<a name="core-error-getting-full-deployment-configuration"></a>

You might see this error when the core device receives a large deployment document, which is a deployment document larger than 7 KB \(for deployments that target things\) or 31 KB \(for deployments that target thing groups\)\. To retrieve a large deployment document, a core device's AWS IoT policy must allow the `greengrass:GetDeploymentConfiguration` permission\. This error can occur when the core device doesn't have this permission\. When this error occurs, the deployment retries indefinitely, and its status is **In progress** \(`IN_PROGRESS`\)\.

To resolve this issue, add the `greengrass:GetDeploymentConfiguration` permission to the core device's AWS IoT policy\. For more information, see [Update a core device's AWS IoT policy](device-auth.md#update-core-device-iot-policy)\.

### Warn: com\.aws\.greengrass\.deployment\.DeploymentService: Failed to get thing group hierarchy<a name="core-warning-failed-to-get-thing-group-hierarchy"></a>

You might see this warning when the core device receives a deployment and the core device's AWS IoT policy doesn't allow the `greengrass:ListThingGroupsForCoreDevice` permission\. When you create a deployment, the core device uses this permission to identify its thing groups and remove components for any thing groups from which you removed the core device\. If the core device runs [Greengrass nucleus](greengrass-nucleus-component.md) v2\.5\.0, the deployment fails\. If the core device runs Greengrass nucleus v2\.5\.1 or later, the deployment proceeds but doesn't remove components\. For more information about thing group removal behavior, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

To update the core device's behavior to remove components for thing groups from which you remove the core device, add the `greengrass:ListThingGroupsForCoreDevice` permission to the core device's AWS IoT policy\. For more information, see [Update a core device's AWS IoT policy](device-auth.md#update-core-device-iot-policy)\.

### Info: com\.aws\.greengrass\.deployment\.DeploymentDocumentDownloader: Calling Greengrass cloud to get full deployment configuration<a name="core-info-repetitive-get-full-deployment-configuration"></a>

You might see this information message printed multiple times without an error, because the core device logs the error at the `DEBUG` log level\. This issue can occur when the core device receives a large deployment document\. When this issue occurs, the deployment retries indefinitely, and its status is **In progress** \(`IN_PROGRESS`\)\. For more information about how to resolve this issue, see [this troubleshooting entry](#core-error-getting-full-deployment-configuration)\.

## Core device component issues<a name="greengrass-core-component-issues"></a>

Troubleshoot Greengrass component issues on core devices\.

**Topics**
+ [Instant exceeds minimum or maximum instant](#stream-manager-instant-exceeds-maximun-minimum)
+ [Warn: '<command>' is not recognized as an internal or external command](#component-warn-command-not-recognized)
+ [Python script doesn't log messages](#python-component-no-log-output)
+ [com\.aws\.greengrass\.componentmanager\.plugins\.docker\.exceptions\.DockerLoginException: Error logging into the registry using credentials \- 'The stub received bad data\.'](#docker-login-stub-received-bad-data)

### Instant exceeds minimum or maximum instant<a name="stream-manager-instant-exceeds-maximun-minimum"></a>

When you upgrade stream manager v2\.0\.7 to a version between v2\.0\.8 and v2\.0\.11, you might see the following error in the stream manager component's logs if the component fails to start\. 

```
2021-07-16T00:54:58.568Z [INFO] (Copier) aws.greengrass.StreamManager: stdout. Caused by: com.fasterxml.jackson.databind.JsonMappingException: Instant exceeds minimum or maximum instant (through reference chain: com.amazonaws.iot.greengrass.streammanager.export.PersistedSuccessExportStatesV1["lastExportTime"]). {scriptName=services.aws.greengrass.StreamManager.lifecycle.startup.script, serviceName=aws.greengrass.StreamManager, currentState=STARTING}
2021-07-16T00:54:58.579Z [INFO] (Copier) aws.greengrass.StreamManager: stdout. Caused by: java.time.DateTimeException: Instant exceeds minimum or maximum instant. {scriptName=services.aws.greengrass.StreamManager.lifecycle.startup.script, serviceName=aws.greengrass.StreamManager, currentState=STARTING}
```

If you deployed stream manager v2\.0\.7 and you want to upgrade to a later version, you must upgrade to stream manager v2\.0\.12 directly\. For more information about the stream manager component, see [Stream manager](stream-manager-component.md)\.

### Warn: '<command>' is not recognized as an internal or external command<a name="component-warn-command-not-recognized"></a>

You might see this error in a Greengrass component's logs when the AWS IoT Greengrass Core software fails to run a command in the component's lifecycle script\. The component's state becomes `BROKEN` as a result of this error\. This error can occur if the system user that runs the component, such as `ggc_user`, can't find the command's executable in the folders in the [PATH](https://en.wikipedia.org/wiki/PATH_(variable))\.

On Windows devices, check that the folder that contains the executable is in the `PATH` for the system user that runs the component\. If it's missing from the `PATH`, do one of the following:
+ Add the executable's folder to the `PATH` system variable, which is available to all users\. Then, restart the component\.

  If you run Greengrass nucleus 2\.5\.0, after you update the `PATH` system variable, you must restart the AWS IoT Greengrass Core software to run components with the updated `PATH`\. If the AWS IoT Greengrass Core software doesn't use the updated `PATH` after you restart the software, restart the device and try again\. For more information, see [Run the AWS IoT Greengrass Core software](run-greengrass-core-v2.md)\.
+ Add the executable's folder to the `PATH` user variable for the system user that runs the component\.

### Python script doesn't log messages<a name="python-component-no-log-output"></a>

Greengrass core devices collect logs that you can use to identify issues with components\. If your Python script's `stdout` and `stderr` messages don't appear in your component logs, you might need to flush the buffer or disable buffering for these standard output streams in Python\. Do any of the following:
+ Run Python with the [\-u](https://docs.python.org/3/using/cmdline.html#cmdoption-u) argument to disable buffering on `stdout` and `stderr`\.

------
#### [ Linux or Unix ]

  ```
  python3 -u hello_world.py
  ```

------
#### [ Windows ]

  ```
  py -3 -u hello_world.py
  ```

------
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

For more information about how to verify that your Python script outputs log messages, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

### com\.aws\.greengrass\.componentmanager\.plugins\.docker\.exceptions\.DockerLoginException: Error logging into the registry using credentials \- 'The stub received bad data\.'<a name="docker-login-stub-received-bad-data"></a>

You might see this error in the Greengrass nucleus logs when the [Docker application manager component](docker-application-manager-component.md) tries to download a Docker image from a private repository in Amazon Elastic Container Registry \(Amazon ECR\)\. This error occurs if you use the `wincred` [Docker credential helper](https://github.com/docker/docker-credential-helpers) \(`docker-credential-wincred`\)\. As a result, Amazon ECR is unable to store the login credentials\.

Take one of the following actions:
+ If you don't use the `wincred` Docker credential helper, remove the `docker-credential-wincred` program from the core device\.
+ If you use the `wincred` Docker credential helper, do the following:

  1. Rename the `docker-credential-wincred` program on the core device\. Replace `wincred` with a new name for the Windows Docker credential helper\. For example, you can rename it to `docker-credential-wincredreal`\.

  1. Update the `credsStore` option in the Docker configuration file \(`.docker/config.json`\) to use the new name for the Windows Docker credential helper\. For example, if you renamed the program to `docker-credential-wincredreal`, update the `credsStore` option to `wincredreal`\.

     ```
     {
       "credsStore": "wincredreal"
     }
     ```

## AWS Command Line Interface issues<a name="aws-cli-issues"></a>

Troubleshoot AWS CLI issues for AWS IoT Greengrass V2\.

**Topics**
+ [Error: Invalid choice: 'greengrassv2'](#aws-cli-invalid-choice-greengrassv2)

### Error: Invalid choice: 'greengrassv2'<a name="aws-cli-invalid-choice-greengrassv2"></a>

You might see this error when you run an AWS IoT Greengrass V2 command with the AWS CLI \(for example, `aws greengrassv2 list-core-devices`\)\.

This error indicates that you have a version of the AWS CLI that doesn't support AWS IoT Greengrass V2\. To use AWS IoT Greengrass V2 with the AWS CLI, you must have one of the following versions or later:<a name="minimum-aws-cli-versions"></a>
+ Minimum AWS CLI V1 version: v1\.18\.197
+ Minimum AWS CLI V2 version: v2\.1\.11

**Tip**  <a name="tip-check-aws-cli-version"></a>
You can run the following command to check the version of the AWS CLI that you have\.  

```
aws --version
```

To resolve this issue, update the AWS CLI to a later version that supports AWS IoT Greengrass V2\. For more information, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.