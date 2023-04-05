# Troubleshooting AWS IoT Greengrass V2<a name="troubleshooting"></a>

Use the troubleshooting information and solutions in this section to help resolve issues with AWS IoT Greengrass Version 2\.

**Topics**
+ [View AWS IoT Greengrass Core software and component logs](#troubleshoot-with-logs)
+ [AWS IoT Greengrass Core software issues](#greengrass-core-issues)
+ [AWS IoT Greengrass cloud issues](#greengrass-cloud-issues)
+ [Core device deployment issues](#greengrass-core-deployment-issues)
+ [Core device component issues](#greengrass-core-component-issues)
+ [Core device Lambda function component issues](#greengrass-core-lambda-function-issues)
+ [Component version discontinued](#discontinued-component-version)
+ [Greengrass Command Line Interface issues](#greengrass-cli-issues)
+ [AWS Command Line Interface issues](#aws-cli-issues)
+ [Detailed deployment error codes](troubleshooting-deployment.md)
+ [Detailed component status codes](troubleshooting-component.md)

## View AWS IoT Greengrass Core software and component logs<a name="troubleshoot-with-logs"></a>

The AWS IoT Greengrass Core software writes logs to the local file system that you can use to view real\-time information about the core device\. You can also configure core devices to write logs to CloudWatch Logs, so you can remotely troubleshoot core devices\. These logs can help you identify issues with components, deployments, and core devices\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

## AWS IoT Greengrass Core software issues<a name="greengrass-core-issues"></a>

Troubleshoot AWS IoT Greengrass Core software issues\. 

**Topics**
+ [Unable to set up core device](#unable-to-set-up-core-device)
+ [Unable to start the AWS IoT Greengrass Core software as a system service](#unable-to-start-system-service)
+ [Unable to set up nucleus as a system service](#unable-to-set-up-system-service)
+ [Unable to connect to AWS IoT Core](#core-error-unable-to-connect-to-aws-iot)
+ [Out of memory error](#java-out-of-memory)
+ [Unable to install Greengrass CLI](#unable-to-install-greengrass-cli)
+ [User root is not allowed to execute](#user-not-allowed-to-execute)
+ [com\.aws\.greengrass\.lifecyclemanager\.GenericExternalService: Could not determine user/group to run with](#missing-default-run-with-user)
+ [Failed to map segment from shared object: operation not permitted](#tmp-folder-noexec)
+ [Failed to set up Windows service](#failed-to-set-up-windows-service)
+ [com\.aws\.greengrass\.util\.exceptions\.TLSAuthException: Failed to get trust manager](#failed-to-get-trust-manager)
+ [com\.aws\.greengrass\.deployment\.IotJobsHelper: No connection available during subscribing to Iot Jobs descriptions topic\. Will retry in sometime](#iot-jobs-no-connection-available)
+ [software\.amazon\.awssdk\.services\.iam\.model\.IamException: The security token included in the request is invalid](#error-invalid-security-token)
+ [software\.amazon\.awssdk\.services\.iot\.model\.IotException: User: <user> is not authorized to perform: iot:GetPolicy](#missing-automatic-provisioning-permissions)
+ [Error: com\.aws\.greengrass\.shadowmanager\.sync\.model\.FullShadowSyncRequest: Could not execute cloud shadow get request](#shadow-manager-error-could-not-execute-shadow-get-request)
+ [Operation aws\.greengrass\#<operation> is not supported by Greengrass](#ipc-operation-not-supported)
+ [java\.io\.FileNotFoundException: <stream\-manager\-store\-root\-dir>/stream\_manager\_metadata\_store \(Permission denied\)](#stream-manager-store-root-folder-not-found)
+ [com\.aws\.greengrass\.security\.provider\.pkcs11\.PKCS11CryptoKeyService: Private key or certificate with label <label> does not exist](#pkcs11-provider-error-private-key-or-certificate-does-not-exist)
+ [software\.amazon\.awssdk\.services\.secretsmanager\.model\.SecretsManagerException: User: <user> is not authorized to perform: secretsmanager:GetSecretValue on resource: <arn>](#secret-manager-error-not-authorized-to-perform-get-secret-value)
+ [software\.amazon\.awssdk\.services\.secretsmanager\.model\.SecretsManagerException: Access to KMS is not allowed](#secret-manager-error-no-kms-access)
+ [java\.lang\.NoClassDefFoundError: com/aws/greengrass/security/CryptoKeySpi](#hardware-security-incompatible-nucleus-version)
+ [com\.aws\.greengrass\.security\.provider\.pkcs11\.PKCS11CryptoKeyService: CKR\_OPERATION\_NOT\_INITIALIZED](#ckr-operation-not-initialized)

### Unable to set up core device<a name="unable-to-set-up-core-device"></a>

If the AWS IoT Greengrass Core software installer fails and you aren't able to set up a core device, you might need to uninstall the software and try again\. For more information, see [Uninstall the AWS IoT Greengrass Core software](uninstall-greengrass-core-v2.md)\.

### Unable to start the AWS IoT Greengrass Core software as a system service<a name="unable-to-start-system-service"></a>

If the AWS IoT Greengrass Core software fails to start, [check the system service logs](monitor-logs.md#access-system-service-logs) to identify the issue\. One common issue is where Java isn't available on the PATH environment variable \(Linux\) or PATH system variable \(Windows\)\.

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

### com\.aws\.greengrass\.lifecyclemanager\.GenericExternalService: Could not determine user/group to run with<a name="missing-default-run-with-user"></a>

You might see this error when the core device tries to run a component, and the Greengrass nucleus doesn't specify a default system user to use to run components\.

To fix this issue, configure the Greengrass nucleus to specify the default system user that runs components\. For more information, see [Configure the user that runs components](configure-greengrass-core-v2.md#configure-component-user) and [Configure the default component user](configure-greengrass-core-v2.md#configure-default-component-user)\.

### Failed to map segment from shared object: operation not permitted<a name="tmp-folder-noexec"></a>

You might see this error when the AWS IoT Greengrass Core software fails to start because the `/tmp` folder is mounted with `noexec` permissions\. The [AWS Common Runtime \(CRT\) library](https://github.com/awslabs/aws-crt-java) uses the `/tmp` folder by default\.

Do one of the following:
+ Run the following command to remount the `/tmp` folder with `exec` permissions and try again\.

  ```
  sudo mount -o remount,exec /tmp
  ```
+ If you run Greengrass nucleus v2\.5\.0 or later, you can set a JVM option to change the folder that the AWS CRT library uses\. You can specify the `jvmOptions` parameter in the Greengrass nucleus component configuration in a deployment or when you install the AWS IoT Greengrass Core software\. Replace */path/to/use* with the path to a folder that the AWS CRT library can use\.

  ```
  {
    "jvmOptions": "-Daws.crt.lib.dir=\"/path/to/use\""
  }
  ```

### Failed to set up Windows service<a name="failed-to-set-up-windows-service"></a>

You might see this error if you install the AWS IoT Greengrass Core software on a Microsoft Windows 2016 device\. The AWS IoT Greengrass Core software is not supported on Windows 2016, for a list of supported operating systems, see [Supported platforms](setting-up.md#greengrass-v2-supported-platforms)\.

If you must use Windows 2016, you can do the following:

1. Unzip the downloaded AWS IoT Greengrass Core installation archive

1. In the `Greengrass` directory open the `bin/greengrass.xml.template` file\.

1. Add the `<autoRefresh>` tag to the end of the file just before the `</service>` tag\.

   ```
     </log>
     <autoRefresh>false</autoRefresh>
   </service>
   ```

### com\.aws\.greengrass\.util\.exceptions\.TLSAuthException: Failed to get trust manager<a name="failed-to-get-trust-manager"></a>

You might see this error when you install the AWS IoT Greengrass Core software without a root certificate authority \(CA\) file\.

```
2022-06-05T10:00:39.556Z [INFO] (main) com.aws.greengrass.lifecyclemanager.Kernel: service-loaded. {serviceName=DeploymentService}
2022-06-05T10:00:39.943Z [WARN] (main) com.aws.greengrass.componentmanager.ClientConfigurationUtils: configure-greengrass-mutual-auth. Error during configure greengrass client mutual auth. {}
com.aws.greengrass.util.exceptions.TLSAuthException: Failed to get trust manager
```

Check that you specify a valid root CA file with the `rootCaPath` parameter in the configuration file that you provide to the installer\. For more information, see [Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\.

### com\.aws\.greengrass\.deployment\.IotJobsHelper: No connection available during subscribing to Iot Jobs descriptions topic\. Will retry in sometime<a name="iot-jobs-no-connection-available"></a>

You might see this warning message when the core device can't connect to AWS IoT Core to subscribe to deployment job notifications\. Do the following:
+ Check that the core device is connected to the internet and can reach the AWS IoT data endpoint that you configured\. For more information about endpoints that core devices use, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.
+ Check the Greengrass logs for other errors that reveal other root causes\.

### software\.amazon\.awssdk\.services\.iam\.model\.IamException: The security token included in the request is invalid<a name="error-invalid-security-token"></a>

You might see this error when you [install the AWS IoT Greengrass Core software with automatic provisioning](quick-installation.md), and the installer uses an AWS session token that isn't valid\. Do the following:
+ If you use temporary security credentials, check that the session token is correct and that you are copying and pasting the complete session token\.
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

### software\.amazon\.awssdk\.services\.iot\.model\.IotException: User: <user> is not authorized to perform: iot:GetPolicy<a name="missing-automatic-provisioning-permissions"></a>

You might see this error when you [install the AWS IoT Greengrass Core software with automatic provisioning](quick-installation.md), and the installer uses AWS credentials that don't have the required permissions\. For more information about the permissions that are required, see [Minimal IAM policy for installer to provision resources](provision-minimal-iam-policy.md)\.

Check the permissions for the credentials' IAM identity, and grant the IAM identity any required permissions that are missing\.

### Error: com\.aws\.greengrass\.shadowmanager\.sync\.model\.FullShadowSyncRequest: Could not execute cloud shadow get request<a name="shadow-manager-error-could-not-execute-shadow-get-request"></a>

You might see this error when you use the [shadow manager component](shadow-manager-component.md) to [sync device shadows with AWS IoT Core](sync-shadows-with-iot-core.md)\. The HTTP 403 status code indicates that this error occurred because the core device's AWS IoT policy doesn't grant permission to call `GetThingShadow`\.

```
com.aws.greengrass.shadowmanager.sync.model.FullShadowSyncRequest: Could not execute cloud shadow get request. {thing name=MyGreengrassCore, shadow name=MyShadow}
2021-07-14T21:09:02.456Z [ERROR] (pool-2-thread-109) com.aws.greengrass.shadowmanager.sync.SyncHandler: sync. Skipping sync request. {thing name=MyGreengrassCore, shadow name=MyShadow}
com.aws.greengrass.shadowmanager.exception.SkipSyncRequestException: software.amazon.awssdk.services.iotdataplane.model.IotDataPlaneException: null (Service: IotDataPlane, Status Code: 403, Request ID: f6e713ba-1b01-414c-7b78-5beb3f3ad8f6, Extended Request ID: null)
```

To sync local shadows with AWS IoT Core, the core device's AWS IoT policy must grant the following permissions:
+ `iot:GetThingShadow`
+ `iot:UpdateThingShadow`
+ `iot:DeleteThingShadow`

Check the core device's AWS IoT policy, and add any required permissions that are missing\. For more information, see the following:
+ [AWS IoT Core policy actions](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policy-actions.html) in the *AWS IoT Developer Guide*
+ [Update a core device's AWS IoT policy](device-auth.md#update-core-device-iot-policy)

### Operation aws\.greengrass\#<operation> is not supported by Greengrass<a name="ipc-operation-not-supported"></a>

You might see this error when you use an [interprocess communication \(IPC\) operation](interprocess-communication.md) in a custom Greengrass component, and the required AWS\-provided component isn't installed on the core device\.

To fix this issue, add the required component as a [dependency in your component recipe](component-recipe-reference.md#recipe-reference-component-dependencies), so the AWS IoT Greengrass Core software installed the required component when you deploy your component\.
+ [Retrieve secret values](ipc-secret-manager.md) – `aws.greengrass.SecretManager`
+ [Interact with local shadows](ipc-local-shadows.md) – `aws.greengrass.ShadowManager`
+ [Manage local deployments and components](ipc-local-deployments-components.md) – `aws.greengrass.Cli` v2\.6\.0 or later
+ [Authenticate and authorize client devices](ipc-client-device-auth.md) – `aws.greengrass.clientdevices.Auth` v2\.2\.0 or later

### java\.io\.FileNotFoundException: <stream\-manager\-store\-root\-dir>/stream\_manager\_metadata\_store \(Permission denied\)<a name="stream-manager-store-root-folder-not-found"></a>

You might see this error in the stream manager log file \(`aws.greengrass.StreamManager.log`\) when you configure [stream manager](stream-manager-component.md) to use a root folder that doesn't exist or have the correct permissions\. For more information about how to configure this folder, see [stream manager configuration](stream-manager-component.md#stream-manager-component-configuration)\.

### com\.aws\.greengrass\.security\.provider\.pkcs11\.PKCS11CryptoKeyService: Private key or certificate with label <label> does not exist<a name="pkcs11-provider-error-private-key-or-certificate-does-not-exist"></a>

This error occurs when the [PKCS\#11 provider component](pkcs11-provider-component.md) can't find or load the private key or certificate that you specify when you configure the AWS IoT Greengrass Core software to use a [hardware security module \(HSM\)](hardware-security.md)\. Do the following:
+ Check that the private key and certificate are stored in the HSM using the slot, user PIN, and object label that you configure the AWS IoT Greengrass Core software to use\.
+ Check that the private key and certificate use the same object label in the HSM\.
+ If your HSM supports object IDs, check that the private key and certificate use the same object ID in the HSM\.

Check the documentation for your HSM to learn how to query details about the security tokens in the HSM\. If you need to change the slot, object label, or object ID for a security token, check the documentation for your HSM to learn how to do so\.

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

### software\.amazon\.awssdk\.services\.secretsmanager\.model\.SecretsManagerException: Access to KMS is not allowed<a name="secret-manager-error-no-kms-access"></a>

This error can occur when you use the [secret manager component](secret-manager-component.md) to deploy an AWS Secrets Manager secret that is encrypted by an AWS Key Management Service key\. If the core device's [token exchange IAM role](device-service-role.md) doesn't grant permission to decrypt the secret, the deployment fails and the Greengrass logs include this error\.

To fix the issue, add the `kms:Decrypt` permission to the core device's token exchange role\. For more information, see the following:
+ [Secret encryption and decryption](https://docs.aws.amazon.com/secretsmanager/latest/userguide/security-encryption.html) in the *AWS Secrets Manager User Guide*
+ [Authorize core devices to interact with AWS services](device-service-role.md)

### java\.lang\.NoClassDefFoundError: com/aws/greengrass/security/CryptoKeySpi<a name="hardware-security-incompatible-nucleus-version"></a>

You might see this error when you try to install the AWS IoT Greengrass Core software with [hardware security](hardware-security.md) and you use an earlier Greengrass nucleus version that doesn't support hardware security integration\. To use hardware security integration, you must use Greengrass nucleus v2\.5\.3 or later\.

### com\.aws\.greengrass\.security\.provider\.pkcs11\.PKCS11CryptoKeyService: CKR\_OPERATION\_NOT\_INITIALIZED<a name="ckr-operation-not-initialized"></a>

You might see this error when you use the TPM2 library when running AWS IoT Greengrass Core as a system service\.

This error indicates that you need to add an environment variable that provides the location of the PKCS\#11 store in the AWS IoT Greengrass Core systemd service file\. 

For more information, see the Requirements section of the [PKCS\#11 provider](pkcs11-provider-component.md) component documentation\.

## AWS IoT Greengrass cloud issues<a name="greengrass-cloud-issues"></a>

Use the following information to troubleshoot issues with the AWS IoT Greengrass console and API\. Each entry corresponds to an error message that you might see when you perform an action\.

### An error occurred \(AccessDeniedException\) when calling the CreateComponentVersion operation: User: arn:aws:iam::123456789012:user/<username> is not authorized to perform: null<a name="cloud-error-create-component-version-not-authorized-to-perform-null"></a>

You might see this error when you create a component version from the AWS IoT Greengrass console or with the [CreateComponentVersion](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateComponentVersion.html) operation\.

This error indicates that your recipe isn't valid JSON or YAML\. Check the syntax of your recipe, fix any syntax issues, and try again\. You can use an online JSON or YAML syntax checker to identify syntax issues in your recipe\.

### Invalid Input: Encountered following errors in Artifacts: \{<s3ArtifactUri> = Specified artifact resource cannot be accessed\}<a name="cloud-error-specified-artifact-resource-cannot-be-accessed"></a>

You might see this error when you create a component version from the AWS IoT Greengrass console or with the [CreateComponentVersion](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateComponentVersion.html) operation\. This error indicates that an S3 artifact in the component recipe isn't valid\.

Do the following:
+ Check that the S3 bucket is in the same AWS Region where you create the component\. AWS IoT Greengrass doesn't support cross\-Region requests for component artifacts\.
+ Check that the artifact URI is a valid S3 object URL, and check the artifact exists at that S3 object URL\.
+ Check that your AWS account has permission to access the artifact at its S3 object URL\.

## Core device deployment issues<a name="greengrass-core-deployment-issues"></a>

Troubleshoot deployment issues on Greengrass core devices\. Each entry corresponds to a log message that you might see on your core device\.

**Topics**
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.PackageDownloadException: Failed to download artifact](#core-error-failed-to-download-artifact-package-download-exception)
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.ArtifactChecksumMismatchException: Integrity check for downloaded artifact failed\. Probably due to file corruption\.](#core-error-failed-to-download-artifact-checksum-mismatch-exception)
+ [Error: com\.aws\.greengrass\.componentmanager\.exceptions\.NoAvailableComponentVersionException: Failed to negotiate component <name> version with cloud and no local applicable version satisfying requirement <requirements>](#core-error-no-available-component-version)
+ [software\.amazon\.awssdk\.services\.greengrassv2data\.model\.ResourceNotFoundException: The latest version of Component <componentName> doesn't claim platform <coreDevicePlatform> compatibility](#deployment-error-no-platform-compatibility)
+ [com\.aws\.greengrass\.componentmanager\.exceptions\.PackagingException: The deployment attempts to update the nucleus from aws\.greengrass\.Nucleus\-<version> to aws\.greengrass\.Nucleus\-<version> but no component of type nucleus was included as target component](#deployment-error-nucleus-minor-version-update)
+ [Info: com\.aws\.greengrass\.deployment\.exceptions\.RetryableDeploymentDocumentDownloadException: Greengrass Cloud Service returned an error when getting full deployment configuration](#core-error-getting-full-deployment-configuration)
+ [Warn: com\.aws\.greengrass\.deployment\.DeploymentService: Failed to get thing group hierarchy](#core-warning-failed-to-get-thing-group-hierarchy)
+ [Info: com\.aws\.greengrass\.deployment\.DeploymentDocumentDownloader: Calling Greengrass cloud to get full deployment configuration](#core-info-repetitive-get-full-deployment-configuration)

### Error: com\.aws\.greengrass\.componentmanager\.exceptions\.PackageDownloadException: Failed to download artifact<a name="core-error-failed-to-download-artifact-package-download-exception"></a>

You might see this error when the AWS IoT Greengrass Core software fails to download a component artifact when the core device applies a deployment\. The deployment fails as a result of this error\.

When you receive this error, the log also includes a stack trace that you can use to identify the specific issue\. Each of the following entries corresponds to a message that you might see in the stack trace of the `Failed to download artifact` error message\.

**Topics**
+ [software\.amazon\.awssdk\.services\.s3\.model\.S3Exception: null \(Service: S3, Status Code: 403, Request ID: null, \.\.\.\)](#core-error-failed-to-download-artifact-s3-permissions)
+ [software\.amazon\.awssdk\.services\.s3\.model\.S3Exception: Access Denied \(Service: S3, Status Code: 403, Request ID: <requestID>](#core-error-failed-to-download-artifact-get-bucket-location-403)

#### software\.amazon\.awssdk\.services\.s3\.model\.S3Exception: null \(Service: S3, Status Code: 403, Request ID: null, \.\.\.\)<a name="core-error-failed-to-download-artifact-s3-permissions"></a>

The [PackageDownloadException error](#core-error-failed-to-download-artifact-package-download-exception) might include this stack trace in the following cases:
+ The component artifact isn't available at the S3 object URL that you specify in the component's recipe\. Check that you uploaded the artifact to the S3 bucket and that the artifact URI matches the S3 object URL of the artifact in the bucket\.
+ The core device's [token exchange role](device-service-role.md) doesn't allow the AWS IoT Greengrass Core software to download the component artifact from the S3 object URL that you specify in the component's recipe\. Check that the token exchange role allows `s3:GetObject` for the S3 object URL where the artifact is available\.

#### software\.amazon\.awssdk\.services\.s3\.model\.S3Exception: Access Denied \(Service: S3, Status Code: 403, Request ID: <requestID><a name="core-error-failed-to-download-artifact-get-bucket-location-403"></a>

The [PackageDownloadException error](#core-error-failed-to-download-artifact-package-download-exception) might include this stack trace when the core device doesn't have permission to call `s3:GetBucketLocation`\. The error message also includes the following message\.

```
reason: Failed to determine S3 bucket location
```

Check that the core device's [token exchange role](device-service-role.md) allows `s3:GetBucketLocation` for the S3 bucket where the artifact is available\.

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

### software\.amazon\.awssdk\.services\.greengrassv2data\.model\.ResourceNotFoundException: The latest version of Component <componentName> doesn't claim platform <coreDevicePlatform> compatibility<a name="deployment-error-no-platform-compatibility"></a>

You might see this error when you deploy a component to a core device, and the component doesn't list a platform that is compatible with the core device's platform\. Do one of the following:
+ If the component is a custom Greengrass component, you can update the component to be compatible with the core device\. Add a new manifest that matches the core device's platform, or update an existing manifest to match the core device's platform\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.
+ If the component is provided by AWS, check if another version of the component is compatible with the core device\. If no version is compatible, contact us on [AWS re:Post](https://repost.aws/) using the [AWS IoT Greengrass tag](https://repost.aws/tags/TA4ckIed1sR4enZBey29rKTg/aws-io-t-greengrass), or contact [AWS Support](http://aws.amazon.com/contact-us/)\.

### com\.aws\.greengrass\.componentmanager\.exceptions\.PackagingException: The deployment attempts to update the nucleus from aws\.greengrass\.Nucleus\-<version> to aws\.greengrass\.Nucleus\-<version> but no component of type nucleus was included as target component<a name="deployment-error-nucleus-minor-version-update"></a>

You might see this error when you deploy a component that depends on the [Greengrass nucleus](greengrass-nucleus-component.md), and the core device runs an earlier Greengrass nucleus version than the latest minor version available\. This error occurs because the AWS IoT Greengrass Core software tries to automatically update components to the latest compatible version\. However, the AWS IoT Greengrass Core software prevents the Greengrass nucleus from updating to a new minor version, because several AWS\-provided components depend on specific minor versions of the Greengrass nucleus\. For more information, see [Greengrass nucleus update behavior](update-greengrass-core-v2.md#ota-update-behavior-nucleus)\.

You must [revise the deployment](revise-deployments.md) to specify the Greengrass nucleus version that you want to use\. Do one of the following:
+ Revise the deployment to specify the Greengrass nucleus version that the core device currently runs\.
+ Revise the deployment to specify a later minor version of the Greengrass nucleus\. If you choose this option, you must also update the versions of all AWS\-provided components that depend on specific minor versions of the Greengrass nucleus\. For more information, see [AWS\-provided components](public-components.md)\.

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
+ [Warn: '<command>' is not recognized as an internal or external command](#component-warn-command-not-recognized)
+ [Python script doesn't log messages](#python-component-no-log-output)
+ [Component configuration doesn't update when changing default configuration](#update-component-configuration-to-default-configuration)
+ [awsiot\.greengrasscoreipc\.model\.UnauthorizedError](#ipc-unauthorized-error)
+ [com\.aws\.greengrass\.authorization\.exceptions\.AuthorizationException: Duplicate policy ID "<id>" for principal "<componentList>"](#ipc-duplicate-authorization-policy-ids)
+ [com\.aws\.greengrass\.tes\.CredentialRequestHandler: Error in retrieving AwsCredentials from TES \(HTTP 400\)](#token-exchange-service-credentials-http-400)
+ [com\.aws\.greengrass\.tes\.CredentialRequestHandler: Error in retrieving AwsCredentials from TES \(HTTP 403\)](#token-exchange-service-credentials-http-403)
+ [com\.aws\.greengrass\.tes\.CredentialsProviderError: Could not load credentials from any providers](#token-exchange-service-credentials-provider-error)
+ [Received error when attempting to retrieve ECS metadata: Could not connect to the endpoint URL: "<tokenExchangeServiceEndpoint>"](#token-exchange-service-not-running)
+ [copyFrom: <configurationPath> is already a container, not a leaf](#configuration-key-is-container-cannot-become-leaf)
+ [com\.aws\.greengrass\.componentmanager\.plugins\.docker\.exceptions\.DockerLoginException: Error logging into the registry using credentials \- 'The stub received bad data\.'](#docker-login-stub-received-bad-data)
+ [java\.io\.IOException: Cannot run program "cmd" \.\.\.: \[LogonUser\] The password for this account has expired\.](#windows-account-password-expired)
+ [aws\.greengrass\.StreamManager: Instant exceeds minimum or maximum instant](#stream-manager-instant-exceeds-maximun-minimum)

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

### Component configuration doesn't update when changing default configuration<a name="update-component-configuration-to-default-configuration"></a>

When you change the `DefaultConfiguration` in a component's recipe, the new default configuration won't replace the component's existing configuration during a deployment\. To apply the new default configuration, you must reset the component's configuration to its default settings\. When you deploy the component, specify a single empty string as the [reset update](update-component-configurations.md#reset-configuration-update)\.

------
#### [ Console ]

**Reset paths**  

```
[""]
```

------
#### [ AWS CLI ]

The following command creates a deployment to a core device\.

```
aws greengrassv2 create-deployment --cli-input-json file://reset-configuration-deployment.json
```

The `reset-configuration-deployment.json` file contains the following JSON document\.

```
{
  "targetArn": "arn:aws:iot:us-west-2:123456789012:thing/MyGreengrassCore",
  "deploymentName": "Deployment for MyGreengrassCore",
  "components": {
    "com.example.HelloWorld": {
      "componentVersion": "1.0.0",
      "configurationUpdate": {,
        "reset": [""]
      }
    }
  }
}
```

------
#### [ Greengrass CLI ]

The following [Greengrass CLI](greengrass-cli-component.md) command creates a local deployment on a core device\.

```
sudo greengrass-cli deployment create \
  --recipeDir recipes \
  --artifactDir artifacts \
  --merge "com.example.HelloWorld=1.0.0" \
  --update-config reset-configuration-deployment.json
```

The `reset-configuration-deployment.json` file contains the following JSON document\.

```
{
  "com.example.HelloWorld": {
    "RESET": [""]
  }
}
```

------

### awsiot\.greengrasscoreipc\.model\.UnauthorizedError<a name="ipc-unauthorized-error"></a>

You might see this error in a Greengrass component's logs when the component doesn't have permission to perform an IPC operation on a resource\. To grant a component permission to call an IPC operation, define an IPC authorization policy in the component's configuration\. For more information, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies)\.

**Tip**  <a name="tip-reset-configuration-to-apply-new-default-configuration"></a>
If you change the `DefaultConfiguration` in a component's recipe, you must reset the component's configuration to its new default configuration\. When you deploy the component, specify a single empty string as the [reset update](update-component-configurations.md#reset-configuration-update)\. For more information, see [Component configuration doesn't update when changing default configuration](#update-component-configuration-to-default-configuration)\.

### com\.aws\.greengrass\.authorization\.exceptions\.AuthorizationException: Duplicate policy ID "<id>" for principal "<componentList>"<a name="ipc-duplicate-authorization-policy-ids"></a>

You might see this error if multiple IPC authorization policies, including across all components on the core device, use the same policy ID\.

Check your components' IPC authorization policies, fix any duplicates, and try again\. To create unique policy IDs, we recommend that you combine the component name, IPC service name, and a counter\. For more information, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies)\.

**Tip**  <a name="tip-reset-configuration-to-apply-new-default-configuration"></a>
If you change the `DefaultConfiguration` in a component's recipe, you must reset the component's configuration to its new default configuration\. When you deploy the component, specify a single empty string as the [reset update](update-component-configurations.md#reset-configuration-update)\. For more information, see [Component configuration doesn't update when changing default configuration](#update-component-configuration-to-default-configuration)\.

### com\.aws\.greengrass\.tes\.CredentialRequestHandler: Error in retrieving AwsCredentials from TES \(HTTP 400\)<a name="token-exchange-service-credentials-http-400"></a>

You might see this error when a core device can't get AWS credentials from the [token exchange service](interact-with-aws-services.md)\. The HTTP 400 status code indicates that this error occurred because the core device's [token exchange IAM role](device-service-role.md) doesn't exist or doesn't have a trust relationship that allows the AWS IoT credentials provider to assume it\.

Do the following:

1. Identify the token exchange role that the core device uses\. The error message includes the core device's AWS IoT role alias, which points to the token exchange role\. Run the following command on your development computer, and replace *MyGreengrassCoreTokenExchangeRoleAlias* with the name of the AWS IoT role alias from the error message\.

   ```
   aws iot describe-role-alias --role-alias MyGreengrassCoreTokenExchangeRoleAlias
   ```

   The response includes the Amazon Resource Name \(ARN\) of the token exchange IAM role\.

   ```
   {
     "roleAliasDescription": {
       "roleAlias": "MyGreengrassCoreTokenExchangeRoleAlias",
       "roleAliasArn": "arn:aws:iot:us-west-2:123456789012:rolealias/MyGreengrassCoreTokenExchangeRoleAlias",
       "roleArn": "arn:aws:iam::123456789012:role/MyGreengrassV2TokenExchangeRole",
       "owner": "123456789012",
       "credentialDurationSeconds": 3600,
       "creationDate": "2021-02-05T16:46:18.042000-08:00",
       "lastModifiedDate": "2021-02-05T16:46:18.042000-08:00"
     }
   }
   ```

1. Check that the role exists\. Run the following command, and replace *MyGreengrassV2TokenExchangeRole* with the name of the token exchange role\.

   ```
   aws iam get-role --role-name MyGreengrassV2TokenExchangeRole
   ```

   If the command returns a `NoSuchEntity` error, the role doesn't exist, and you must create it\. For more information about how to create and configure this role, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

1. Check that the role has a trust relationship that allows the AWS IoT credentials provider to assume it\. The response from the previous step contains an `AssumeRolePolicyDocument`, which defines the role's trust relationships\. The role must define a trust relationship that allows `credentials.iot.amazonaws.com` to assume it\. This document should look similar to the following example\.

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
       }
     ]
   }
   ```

   If the role's trust relationships don't allow `credentials.iot.amazonaws.com` to assume it, you must add this trust relationship to the role\. For more information, see [Modifying a role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_modify.html) in the *AWS Identity and Access Management User Guide*\.

### com\.aws\.greengrass\.tes\.CredentialRequestHandler: Error in retrieving AwsCredentials from TES \(HTTP 403\)<a name="token-exchange-service-credentials-http-403"></a>

You might see this error when a core device can't get AWS credentials from the [token exchange service](interact-with-aws-services.md)\. The HTTP 403 status code indicates that this error occurred because the core device's AWS IoT policies don't grant the `iot:AssumeRoleWithCertificate` permission for the core device's AWS IoT role alias\.

Review the core device's AWS IoT policies, and add the `iot:AssumeRoleWithCertificate` permission for the core device's AWS IoT role alias\. The error message includes the core device's current AWS IoT role alias\. For more information about this permission and how to update the core device's AWS IoT policies, see [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy) and [Update a core device's AWS IoT policy](device-auth.md#update-core-device-iot-policy)\.

### com\.aws\.greengrass\.tes\.CredentialsProviderError: Could not load credentials from any providers<a name="token-exchange-service-credentials-provider-error"></a>

You might see this error when the component tries to request AWS credentials and can't connect to the [token exchange service](interact-with-aws-services.md)\.

Do the following:
+ Check that the component declares a dependency on the token exchange service component, `aws.greengrass.TokenExchangeService`\. If it doesn't, add the dependency and redeploy the component\.
+ If the component runs in docker, ensure that you apply the right network settings and environment variables, according to [Use AWS credentials in Docker container components \(Linux\)](run-docker-container.md#docker-container-token-exchange-service)\.
+ If the component is written in NodeJS, set [dns\.setDefaultResultOrder](https://nodejs.org/docs/latest/api/dns.html#dnssetdefaultresultorderorder) to **ipv4first**\.
+ Inspect `/etc/hosts` for an entry that starts with `::1` and contains `localhost`\. Remove the entry to see if it caused the component to connect to the token exchange service at the wrong address\.

### Received error when attempting to retrieve ECS metadata: Could not connect to the endpoint URL: "<tokenExchangeServiceEndpoint>"<a name="token-exchange-service-not-running"></a>

You might see this error when the component doesn't run the [token exchange service](interact-with-aws-services.md) and a component tries to request AWS credentials\.

Do the following:
+ Check that the component declares a dependency on the token exchange service component, `aws.greengrass.TokenExchangeService`\. If it doesn't, add the dependency and redeploy the component\.
+ Check whether the component uses AWS credentials in its `install` lifecycle\. AWS IoT Greengrass doesn't guarantee the availability of the token exchange service during the `install` lifecycle\. Update the component to move the code that uses AWS credentials into the `startup` or `run` lifecycle, then redeploy the component\.

### copyFrom: <configurationPath> is already a container, not a leaf<a name="configuration-key-is-container-cannot-become-leaf"></a>

You might see this error when you change a configuration value from a container type \(a list or object\) to a non\-container type \(a string, number, or Boolean\)\. Do the following:

1. Check the component's recipe to see whether its default configuration sets that configuration value to a list or an object\. If so, remove or change that configuration value\.

1. Create a deployment to reset that configuration value to its default value\. For more information, see [Create deployments](create-deployments.md) and [Update component configurations](update-component-configurations.md)\.

Then, you can set that configuration value to a string, number, or Boolean\.

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

### java\.io\.IOException: Cannot run program "cmd" \.\.\.: \[LogonUser\] The password for this account has expired\.<a name="windows-account-password-expired"></a>

You might see this error on a Windows core device when the system user that runs the component's processes, such as `ggc_user`, has an expired password\. As a result, the AWS IoT Greengrass Core software is unable to run component processes as that system user\.

**To update a Greengrass system user's password**

1. Run the following command as an administrator to set the user's password\. Replace *ggc\_user* with the system user, and replace *password* with the password to set\.

   ```
   net user ggc_user password
   ```

1. Use the [PsExec utility](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) to store the user's new password in the Credential Manager instance for the LocalSystem account\. Replace *password* with the user's password that you set\.

   ```
   psexec -s cmd /c cmdkey /generic:ggc_user /user:ggc_user /pass:password
   ```

**Tip**  <a name="windows-password-expiration-tip"></a>
Depending on your Windows configuration, the user's password might be set to expire at a date in the future\. To ensure your Greengrass applications continue to operate, track when the password expires, and update it before it expires\. You can also set the user's password to never expire\.  
To check when a user and its password expire, run the following command\.  

  ```
  net user ggc_user | findstr /C:expires
  ```
To set a user's password to never expire, run the following command\.  

  ```
  wmic UserAccount where "Name='ggc_user'" set PasswordExpires=False
  ```

### aws\.greengrass\.StreamManager: Instant exceeds minimum or maximum instant<a name="stream-manager-instant-exceeds-maximun-minimum"></a>

When you upgrade stream manager v2\.0\.7 to a version between v2\.0\.8 and v2\.0\.11, you might see the following error in the stream manager component's logs if the component fails to start\. 

```
2021-07-16T00:54:58.568Z [INFO] (Copier) aws.greengrass.StreamManager: stdout. Caused by: com.fasterxml.jackson.databind.JsonMappingException: Instant exceeds minimum or maximum instant (through reference chain: com.amazonaws.iot.greengrass.streammanager.export.PersistedSuccessExportStatesV1["lastExportTime"]). {scriptName=services.aws.greengrass.StreamManager.lifecycle.startup.script, serviceName=aws.greengrass.StreamManager, currentState=STARTING}
2021-07-16T00:54:58.579Z [INFO] (Copier) aws.greengrass.StreamManager: stdout. Caused by: java.time.DateTimeException: Instant exceeds minimum or maximum instant. {scriptName=services.aws.greengrass.StreamManager.lifecycle.startup.script, serviceName=aws.greengrass.StreamManager, currentState=STARTING}
```

If you deployed stream manager v2\.0\.7 and you want to upgrade to a later version, you must upgrade to stream manager v2\.0\.12 directly\. For more information about the stream manager component, see [Stream manager](stream-manager-component.md)\.

## Core device Lambda function component issues<a name="greengrass-core-lambda-function-issues"></a>

Troubleshoot Lambda function component issues on core devices\.

**Topics**
+ [The following cgroup subsystems are not mounted: devices, memory](#lambda-cgroups-not-mounted)
+ [ipc\_client\.py:64,HTTP Error 400:Bad Request, b'No subscription exists for the source <label\-or\-lambda\-arn> and subject <label\-or\-lambda\-arn>](#v1-lambda-no-subscription-exists)

### The following cgroup subsystems are not mounted: devices, memory<a name="lambda-cgroups-not-mounted"></a>

You might see this error when you run a containerized Lambda function in the following cases:
+ The core device doesn't have cgroup v1 enabled for the memory or device cgroups\.
+ The core device has cgroups v2 enabled\. Greengrass Lambda functions require cgroups v1, and cgroups v1 and v2 are mutually exclusive\.

To enable cgroups v1, boot the device with the following Linux kernel parameters\.

```
cgroup_enable=memory cgroup_memory=1 systemd.unified_cgroup_hierarchy=0
```

**Tip**  
On a Raspberry Pi, edit the `/boot/cmdline.txt` file to set the device's kernel parameters\.

### ipc\_client\.py:64,HTTP Error 400:Bad Request, b'No subscription exists for the source <label\-or\-lambda\-arn> and subject <label\-or\-lambda\-arn><a name="v1-lambda-no-subscription-exists"></a>

You might see this error when you run a V1 Lambda function, which uses the AWS IoT Greengrass Core SDK, on a V2 core device without specifying a subscription in the [legacy subscription router component](legacy-subscription-router-component.md)\. To fix this issue, deploy and configure the legacy subscription router to specify the required subscriptions\. For more information, see [Import V1 Lambda functions](set-up-v2-test-device.md#run-v1-lambda-functions)\.

## Component version discontinued<a name="discontinued-component-version"></a>

You might see a notification on your Personal Health Dashboard \(PHD\) when a component version on your core device is discontinued\. The component version sends this notification to your PHD within 60 minutes of being discontinued\.

To see which deployments you need to revise, do the following using the AWS Command Line Interface:

1. Run the following command to get a list of your core devices\.

   ```
   aws greengrassv2 list-core-devices
   ```

1. Run the following command to retrieve the status of the components on each core device from Step 1\. Replace `coreDeviceName` with the name of each core device to query\.

   ```
   aws greengrassv2 list-installed-components --core-device-thing-name coreDeviceName
   ```

1. Gather the core devices with the discontinued component version installed from the previous steps\.

1. Run the following command to retrieve the status of all deployment jobs for each core device from Step 3\. Replace `coreDeviceName` with the name of the core device to query\.

   ```
   aws greengrassv2 list-effective-deployments --core-device-thing-name coreDeviceName
   ```

   The response contains the list of deployment jobs for the core device\. You can revise the deployment to choose another component version\. For more information about how to revise a deployment, see [Revise deployments](https://docs.aws.amazon.com/greengrass/v2/developerguide/revise-deployments.html)\.

## Greengrass Command Line Interface issues<a name="greengrass-cli-issues"></a>

Troubleshoot issues with the [Greengrass CLI](gg-cli.md)\.

**Topics**
+ [java\.lang\.RuntimeException: Unable to create ipc client](#greengrass-cli-unable-to-create-ipc-client)

### java\.lang\.RuntimeException: Unable to create ipc client<a name="greengrass-cli-unable-to-create-ipc-client"></a>

You might see this error when you run a Greengrass CLI command and you specify a different root folder than where the AWS IoT Greengrass Core software is installed\.

Do one of the following to set the root path, and replace */greengrass/v2* with the path to your AWS IoT Greengrass Core software installation:<a name="greengrass-cli-set-root-path"></a>
+ Set the `GGC_ROOT_PATH` environment variable to `/greengrass/v2`\.
+ Add the `--ggcRootPath /greengrass/v2` argument to your command as shown in the following example\.

  ```
  greengrass-cli --ggcRootPath /greengrass/v2 <command> <subcommand> [arguments]
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