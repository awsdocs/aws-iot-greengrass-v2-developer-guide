# Troubleshooting IDT for AWS IoT Greengrass V2<a name="idt-troubleshooting"></a>

IDT for AWS IoT Greengrass V2 writes errors to various locations based on the type of errors\. IDT writes errors to the console, log files, and test reports\. 

## Where to look for errors<a name="where-to-look"></a>

High\-level errors display on the console while the test is running, and a summary of the failed tests displays when all tests are complete\. `awsiotdevicetester_report.xml` contains a summary of all the errors that caused a test to fail\. IDT stores the log files for each test run in a directory named with a UUID for the test execution, displayed on the console during the test run\.

The IDT test logs directory is `<device-tester-extract-location>/results/<execution-id>/logs/`\. This directory contains the following files, which are useful for debugging\.


| File | Description | 
| --- | --- | 
| test\_manager\.log |  The logs written to the console while the test was running\. The summary of the results at the end of this file includes a list of which tests failed\. The warning and error logs in this file can give you some information about the failures\.   | 
| test\-group\-id/test\-case\-id/test\-name\.log | Detailed logs for the specific test in a test group\. For tests that deploy Greengrass components, the test case log file is called greengrass\-test\-run\.log\. | 
| test\-group\-id/test\-case\-id/greengrass\.log | Detailed logs for AWS IoT Greengrass Core software\. IDT copies this file from the device under test when it runs tests that install AWS IoT Greengrass Core software on the device\. For more information about the messages in this log file, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\. | 
| test\-group\-id/test\-case\-id/component\-name\.log | Detailed logs for Greengrass components that are deployed during test runs\. IDT copies component log files from the device under test when it runs tests that deploys specific components\. The name of each component log file corresponds to the name of the deployed component\. For more information about the messages in this log file, see [Troubleshooting AWS IoT Greengrass V2](troubleshooting.md)\. | 

## Resolving IDT for AWS IoT Greengrass V2 errors<a name="idt-gg-resolve-errors"></a>

Before you run IDT for AWS IoT Greengrass, get the correct configuration files in place\. If you receive parsing and configuration errors, your first step is to locate and use a configuration template appropriate for your environment\.

If you are still having issues, see the following debugging process\.

**Topics**
+ [Alias resolution errors](#alias-resolution-errors)
+ [Command not found errors while testing](#cmd-not-found)
+ [Conflict errors](#conflict-error)
+ [Could not start test error](#could-not-start-test)
+ [Docker qualification image exists errors](#docker-qualification-image-exists)
+ [Failed to read credential](#failed-to-read-credential-windows)
+ [Invalid signature exception](#invalid-signature-exception-lambda)
+ [Machine learning qualification errors](#machine-learning-qualification-failure)
+ [Parsing errors](#parse-error)
+ [Permission denied errors](#permission-denied-pwd-sudo)
+ [Qualification report generation error](#qualification-report-policy-error)
+ [Required parameter missing error](#required-param-missing)
+ [Security exception on macOS](#security-exception-macos)
+ [SSH connection errors](#ssh-connect-errors)
+ [Stream manager qualification errors](#stream-manager-qualification-failure)
+ [Throttling errors during Docker qualification](#throttling-docker-qualification)
+ [Timeout errors](#test-timeout)
+ [Version check errors](#version-compatibility-check-failure)

### Alias resolution errors<a name="alias-resolution-errors"></a>

When you run custom test suites, you might see the following error in the console and in the `test_manager.log`\. 

```
Couldn't resolve placeholders: couldn't do a json lookup: index out of range
```

This error can occur when the aliases configured in the IDT test orchestrator don't resolve correctly or if the resolved values aren't present in the configuration files\. To resolve this error, make sure that your `device.json` and `userdata.json ` contain the correct information required for your test suite\. For information about the configuration required for AWS IoT Greengrass qualification, see [Configure IDT settings to run the AWS IoT Greengrass qualification suite](set-config.md)\.

### Command not found errors while testing<a name="cmd-not-found"></a>

You need an older version of the OpenSSL library \(libssl1\.0\.0\) to run tests on AWS IoT Greengrass devices\. Most current Linux distributions use libssl version 1\.0\.2 or later \(v1\.1\.0\)\.

For example, on a Raspberry Pi, run the following commands to install the required version of libssl:

1. 

   ```
   wget http://ftp.us.debian.org/debian/pool/main/o/openssl/libssl1.0.0_1.0.2l-1~bpo8+1_armhf.deb
   ```

1. 

   ```
   sudo dpkg -i libssl1.0.0_1.0.2l-1~bpo8+1_armhf.deb
   ```

### Conflict errors<a name="conflict-error"></a>

You might see the following error when you run the AWS IoT Greengrass qualification suite concurrently on more than one device\.

```
ConflictException: Component [com.example.IDTHelloWorld : 1.0.0] for account [account-id] already exists with state: [DEPLOYABLE] { RespMetadata: { StatusCode: 409, RequestID: “id” }, Message_: “Component [com.example.IDTHelloWorld : 1.0.0] for account [account-id] already exists with state: [DEPLOYABLE]” }
```

Concurrent test execution isn't yet supported for the AWS IoT Greengrass qualification suite\. Run the qualification suite sequentially for each device\.

### Could not start test error<a name="could-not-start-test"></a>

You might encounter errors that point to failures that occurred when the test was attempting to start\. There are several possible causes, so do the following:
+ Make sure that the pool name in your execution command actually exists\. IDT references the pool name directly from your `device.json` file\.
+ Make sure that the devices in your pool have correct configuration parameters\.

### Docker qualification image exists errors<a name="docker-qualification-image-exists"></a>

The Docker application manager qualification tests use the `amazon/amazon-ec2-metadata-mock` container image in Amazon ECR to qualify the device under test\.

You might receive the following error if the image is already present in a Docker container on the device under test\.

```
The Docker image amazon/amazon-ec2-metadata-mock:version already exists on the device.
```

If you previously downloaded this image and ran the `amazon/amazon-ec2-metadata-mock` container on your device, make sure you remove this image from the device under test before you run the qualification tests\.

### Failed to read credential<a name="failed-to-read-credential-windows"></a>

When testing Windows devices, you might encounter the `Failed to read credential` error in the `greengrass.log` file if the user that you use to connect to the device under test is not set up in the credential manager on that device\. 

To resolve this error, configure the user and password for the IDT user in the credential manager on the device under test\. This is the same user and password that you specify in the [`device.json` file](set-config.md#device-config)\.

For more information, see [Configure user credentials for Windows devices](device-config-setup.md#configure-windows-user-for-idt)\.

### Invalid signature exception<a name="invalid-signature-exception-lambda"></a>

When you run Lambda qualification tests, you might encounter the `invalidsignatureexception` error if your IDT host machine experiences network access issues\. Reset your router and run the tests again\. 

### Machine learning qualification errors<a name="machine-learning-qualification-failure"></a>

When you run machine learning \(ML\) qualification tests, you might encounter qualification failures if your device doesn't meet the [requirements](dlr-component.md#dlr-component-requirements) to deploy the AWS\-provided ML components\. To troubleshoot ML qualification errors, do the following: 
+ Look for error details in the component logs for the components that were deployed during the test run\. Component logs are located in the `<device-tester-extract-location>/results/<execution-id>/logs/<test-group-id>` directory\.
+ Add the `-Dgg.persist=installed.software` argument to the `test.json` file for the failing test case\. The `test.json` file is located in the `<device-tester-extract-location>/tests/GGV2Q_version directory. `

### Parsing errors<a name="parse-error"></a>

Typos in a JSON configuration can lead to parsing errors\. Most of the time, the issue is a result of omitting a bracket, comma, or quotation mark from your JSON file\. IDT performs JSON validation and prints debugging information\. It prints the line where the error occurred, the line number, and the column number of the syntax error\. This information should be enough to help you fix the error, but if you still can't locate the error, you can perform validation manually in your IDE, a text editor such as Atom or Sublime, or through an online tool like JSONLint\.

### Permission denied errors<a name="permission-denied-pwd-sudo"></a>

IDT performs operations on various directories and files in a device under test\. Some of these operations require root access\. To automate these operations, IDT must be able to run commands with sudo without typing a password\. 

Follow these steps to allow sudo access without typing a password\.

**Note**  
`user` and `username` refer to the SSH user used by IDT to access the device under test\.

1. Use sudo usermod \-aG sudo *<ssh\-username>* to add your SSH user to the sudo group\.

1. Sign out and then sign in for changes to take effect\.

1. Open `/etc/sudoers` file and add the following line to the end of the file: `<ssh-username> ALL=(ALL) NOPASSWD: ALL`
**Note**  
As a best practice, we recommend that you use sudo visudo when you edit `/etc/sudoers`\.

### Qualification report generation error<a name="qualification-report-policy-error"></a>

IDT supports the four latest `major.minor` versions of the AWS IoT Greengrass V2 qualification suite \(GGV2Q\) to generate qualification reports that you can submit to AWS Partner Network to include your devices in the AWS Partner Device Catalog\. Earlier versions of the qualification suite don’t generate qualification reports\.

If you have questions about the support policy, contact AWS Support at [https://aws.amazon.com/contact-us/](https://aws.amazon.com/contact-us/)\.

### Required parameter missing error<a name="required-param-missing"></a>

When IDT adds new features, it might introduce changes to the configuration files\. Using an old configuration file might break your configuration\. If this happens, the `<test_case_id>.log` file under `/results/<execution-id>/logs` explicitly lists all missing parameters\. IDT also validates your JSON configuration file schemas to verify that you are using the latest supported version\.

### Security exception on macOS<a name="security-exception-macos"></a>

When you run IDT on a host computer that uses macOS 10\.15, the notarization ticket for IDT isn't detected, which blocks IDT from being run\. To run IDT, grant a security exception to the `devicetester_mac_x86-64` executable\. Do one of the following:

**To grant a security exception to IDT executables**

1. Launch **System Preferences** from the Apple menu\.

1. Choose **Security & Privacy**, then on the **General** tab, choose the lock icon to make changes to security settings\.

1. Look for the message `"devicetester_mac_x86-64" was blocked from use because it is not from an identified developer.` and choose **Allow Anyway**\.

1. Accept the security warning\.

### SSH connection errors<a name="ssh-connect-errors"></a>

When IDT can't connect to a device under test, it logs connection failures in `/results/<execution-id>/logs/<test-case-id>.log`\. SSH messages appear at the top of this log file because connecting to a device under test is one of the first operations that IDT performs\.

Most Windows configurations use the PuTTy terminal application to connect to Linux hosts\. This application requires that you convert standard PEM private key files into a proprietary Windows format called PPK\. If you configure SSH in your `device.json` file, use PEM files\. If you use a PPK file, IDT can't create an SSH connection with the AWS IoT Greengrass device and can't run tests\.

Starting with IDT v4\.4\.0, if you haven't enabled SFTP on your device under test, then you might see the following error in the log file\.

```
SSH connection failed with EOF
```

To resolve this error, enable SFTP on your device\.

### Stream manager qualification errors<a name="stream-manager-qualification-failure"></a>

When you run stream manager qualification tests, you might see the following error in the `com.aws.StreamManagerExport.log` file\.

```
Failed to upload data to S3
```

This error can occur when stream manager uses the AWS credentials in the `~/root/.aws/credentials` file on your device instead of using the environment credentials that IDT exports to the device under test\. To prevent this issue, delete the `credentials` file on your device, and re\-run the qualification test\.

### Throttling errors during Docker qualification<a name="throttling-docker-qualification"></a>

Docker Hub limits the number of pull requests that anonymous and Free Docker Hub users can make\. When you run IDT tests for Docker qualification, you might receive one of the following errors if you exceed the rate limits for anonymous or free user pull requests:

```
ERROR: toomanyrequests: Too Many Requests.
```

```
You have reached your pull rate limit.
```

To resolve these errors, you can wait for a few hours before you run the qualification test\. If you plan on consistently running a large number of tests, which can result in submitting a large number of pull requests, see the [Docker Hub website](https://www.docker.com/increase-rate-limits) for information about rate limits, and options for authenticating and upgrading your Docker account\. 

### Timeout errors<a name="test-timeout"></a>

You can increase the timeout for each test by specifying a timeout multiplier applied to the default value of each test's timeout\. Any value configured for this flag must be greater than or equal to 1\.0\.

To use the timeout multiplier, use the flag `--timeout-multiplier` when running the tests\. For example:

```
./devicetester_linux run-suite --suite-id GGV2Q_1.0.0 --pool-id DevicePool1 --timeout-multiplier 2.5
```

For more information, run `run-suite --help`\.

Some timeout errors occur when IDT test cases can’t be completed because of configuration issues\. You can’t resolve these errors by increasing the timeout multiplier\. Use the logs from the test run to troubleshoot the underlying configuration issues\. 
+ If the MQTT or Lambda component logs contain `Access denied` errors, your Greengrass installation folder might not have the correct file permissions\. Run the following command for each folder in the installation path that you defined in your `userdata.json` file\. 

  ```
  sudo chmod 755 folder-name
  ```
+ If the Greengrass logs indicate that the Greengrass CLI deployment isn't complete, do the following:
  + Verify that `bash` is installed on the device under test\. 
  + If your `userdata.json` file includes the `GreengrassCliVersion` configuration parameter, remove it\. This parameter is deprecated in IDT v4\.1\.0 and later versions\. For more information, see [Configure userdata\.json](set-config.md#userdata-config)\.

### Version check errors<a name="version-compatibility-check-failure"></a>

IDT issues the following error when the AWS user credentials for the IDT user don't have the required IAM permissions\. 

```
Failed to check version compatibility
```

The AWS user that doesn't have the required IAM permissions\. 