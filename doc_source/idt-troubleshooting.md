# Troubleshooting IDT for AWS IoT Greengrass<a name="idt-troubleshooting"></a>

IDT for AWS IoT Greengrass writes these errors to various locations based on the type of errors\. Errors are written to the console, log files, and test reports\.

## Resolving IDT for AWS IoT Greengrass errors<a name="idt-gg-resolve-errors"></a>

When you use IDT, you must get the correct configuration files in place before you run IDT for AWS IoT Greengrass\. If you are getting parsing and configuration errors, your first step is to locate and use a configuration template appropriate for your environment\.

If you are still having issues, see the following debugging process\.

**Topics**
+ [Where do I look for errors?](#where-to-look)
+ [Command not found errors while testing](#cmd-not-found)
+ [Conflict errors](#conflict-error)
+ [Could not start test error](#could-not-start-test)
+ [Parsing errors](#parse-error)
+ [Permission denied errors](#pwd-sudo)
+ [Required parameter missing error](#param-missing)
+ [Security exception on macOS](#macos-notarization-exception)
+ [SSH connection errors](#ssh-connect-errors)
+ [Timeout errors](#test-timeout)

### Where do I look for errors?<a name="where-to-look"></a>

High\-level errors display on the console while the test is running, and a summary of the failed tests displays when all tests are complete\. `awsiotdevicetester_report.xml` contains a summary of all the errors that caused a test to fail\. The log files for each test run are stored in a directory named with an UUID for the test execution that was displayed on the console during the test run\.

The test logs directory is located in `<device-tester-extract-location>/results/<execution-id>/logs/`\. This directory contains the following files, which are useful for debugging\.


| File | Description | 
| --- | --- | 
| test\_manager\.log |  All of the logs that were written to the console while the test was running\. A summary of the results is located at the end of this file, which includes a list of which tests failed\. The warning and error logs in this file can give you some information about the failures\.   | 
| <test\-group\-id>\_\_<test\-name>\.log | Detailed logs for the specific test\. | 

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

Occasionally, you might see the following error when you run the AWS IoT Greengrass qualification suite concurrently on more than one device\.

```
ConflictException: Component [com.example.IDTHelloWorld : 1.0.0] for account [account-id] already exists with state: [DEPLOYABLE] { RespMetadata: { StatusCode: 409, RequestID: “id” }, Message_: “Component [com.example.IDTHelloWorld : 1.0.0] for account [account-id] already exists with state: [DEPLOYABLE]” }
```

Concurrent test execution is not yet supported for the AWS IoT Greengrass qualification suite\. Run the qualification suite sequentially for each device\.

### Could not start test error<a name="could-not-start-test"></a>

You might encounter errors that point to failures that occurred when the test was attempting to start\. There are several possible causes, so do the following:
+ Make sure that the pool name you included in your execution command actually exists\. The pool name is referenced directly from your `device.json` file\.
+ Make sure that the devices in your pool have correct configuration parameters\.

### Parsing errors<a name="parse-error"></a>

Occasionally, a typo in a JSON configuration can lead to parsing errors\. Most of the time, the issue is a result of omitting a bracket, comma, or quotation mark from your JSON file\. IDT performs JSON validation and prints debugging information\. It prints the line where the error occurred, the line number, and the column number of the syntax error\. This information should be enough to help you fix the error, but if you still cannot locate the error, you can perform validation manually in your IDE, a text editor such as Atom or Sublime, or through an online tool like JSONLint\.

### Permission denied errors<a name="pwd-sudo"></a>

IDT performs operations on various directories and files in a device under test\. Some of these operations require root access\. To automate these operations, IDT must be able to run commands with sudo without typing a password\. 

Follow these steps to allow sudo access without typing a password\.

**Note**  
`user` and `username` refer to the SSH user used by IDT to access the device under test\.

1. Use sudo usermod \-aG sudo *<ssh\-username>* to add your SSH user to the sudo group\.

1. Sign out and then sign in for changes to take effect\.

1. Open `/etc/sudoers` file and add the following line to the end of the file: `<ssh-username> ALL=(ALL) NOPASSWD: ALL`
**Note**  
As a best practice, we recommend that you use sudo visudo when you edit `/etc/sudoers`\.

### Required parameter missing error<a name="param-missing"></a>

Because new features are being added to IDT, changes to the configuration files might be introduced\. Using an old configuration file might break your configuration\. If this happens, the `<test_case_id>.log` file under `/results/<execution-id>/logs` explicitly lists all missing parameters\. IDT also validates your JSON configuration file schemas to ensure that the latest supported version has been used\.

### Security exception on macOS<a name="macos-notarization-exception"></a>

When you run IDT on host machine that uses macOS 10\.15, the notarization ticket for IDT is not correctly detected and IDT is blocked from being run\. To run IDT, you will need to grant a security exception to the `devicetester_mac_x86-64` executable\. Do one of the following:

**To grant a security exception to IDT executables**

1. Launch **System Preferences** from the Apple menu\.

1. Choose **Security & Privacy**, then on the **General** tab, click the lock icon to make changes to security settings\.

1. Look for the message `"devicetester_mac_x86-64" was blocked from use because it is not from an identified developer.` and choose **Allow Anyway**\.

1. Accept the security warning\.

### SSH connection errors<a name="ssh-connect-errors"></a>

When IDT cannot connect to a device under test, connection failures are logged in `/results/<execution-id>/logs/<test-case-id>.log`\. SSH failure messages appear at the top of this log file because connecting to a device under test is one of the first operations that IDT performs\.

Most Windows configurations use the PuTTy terminal application to connect to Linux hosts\. This application requires that standard PEM private key files are converted into a proprietary Windows format called PPK\. When IDT is configured in your `device.json` file, use PEM files only\. If you use a PPK file, IDT cannot create an SSH connection with the AWS IoT Greengrass device and cannot run tests\.

### Timeout errors<a name="test-timeout"></a>

You can increase the timeout for each test by specifying a timeout multiplier, which is applied to the default value of each test's timeout\. Any value configured for this flag must be greater than or equal to 1\.0\.

To use the timeout multiplier, use the flag `--timeout-multiplier` when running the tests\. For example:

```
./devicetester_linux run-suite --suite-id GGV2Q_1.0.0 --pool-id DevicePool1 --timeout-multiplier 2.5
```

For more information, run `run-suite --help`\.