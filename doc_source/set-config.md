# Configure IDT settings to run the AWS IoT Greengrass qualification suite<a name="set-config"></a>

Before you run tests, you must configure settings for AWS credentials and devices on your host computer\.

## Configure AWS credentials in config\.json<a name="cfg-aws-gg"></a>

You must configure your IAM user credentials in the `<device_tester_extract_location>/configs/config.json` file\. Use the credentials for the IDT for AWS IoT Greengrass user created in [Create and configure an AWS account](dev-tst-prereqs.md#config-aws-account-for-idt)\. You can specify your credentials in one of two ways:
+ In a credentials file
+ As environment variables

### Configure AWS credentials with a credentials file<a name="config-cred-file"></a>

IDT uses the same credentials file as the AWS CLI\. For more information, see [Configuration and credential files](https://docs.aws.amazon.com/cli/latest/userguide/cli-config-files.html)\.

The location of the credentials file varies, depending on the operating system you are using:
+ macOS, Linux: `~/.aws/credentials`
+ Windows: `C:\Users\UserName\.aws\credentials`

Add your AWS credentials to the `credentials` file in the following format:

```
[default]
aws_access_key_id = <your_access_key_id>
aws_secret_access_key = <your_secret_access_key>
```

To configure IDT for AWS IoT Greengrass to use AWS credentials from your `credentials` file, edit your `config.json` file as follows:

```
{
	"awsRegion": "region",
	"auth": {
		"method": "file",
		"credentials": {
			"profile": "default"
		}
	}
}
```

**Note**  
If you do not use the `default` AWS profile, be sure to change the profile name in your `config.json` file\. For more information, see [Named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)\.

### Configure AWS credentials with environment variables<a name="config-env-vars"></a>

Environment variables are variables maintained by the operating system and used by system commands\. They are not saved if you close the SSH session\. IDT for AWS IoT Greengrass can use the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables to store your AWS credentials\.

To set these variables on Linux, macOS, or Unix, use export:

```
export AWS_ACCESS_KEY_ID=<your_access_key_id>
export AWS_SECRET_ACCESS_KEY=<your_secret_access_key>
```

To set these variables on Windows, use set:

```
set AWS_ACCESS_KEY_ID=<your_access_key_id>
set AWS_SECRET_ACCESS_KEY=<your_secret_access_key>
```

To configure IDT to use the environment variables, edit the `auth` section in your `config.json` file\. Here is an example:

```
{
	"awsRegion": "region",
	"auth": {
		"method": "environment"
	}
}
```

## Configure device\.json<a name="device-config"></a>

In addition to AWS credentials, IDT for AWS IoT Greengrass needs information about the devices that tests are run on\. Example information would be IP address, login information, operating system, and CPU architecture\.

You must provide this information using the `device.json` template located in ` <device_tester_extract_location>/configs/device.json`:

```
[
  {
    "id": "<pool-id>",
    "sku": "<sku>",
    "features": [
      {
        "name": "arch",
        "value": "x86_64 | armv6l | armv7l | aarch64"
      }
    ],
    "devices": [
      {
        "id": "<device-id>",
        "operatingSystem": "linux",
        "connectivity": {
          "protocol": "ssh",
          "ip": "<ip-address>",
          "port": 22,
          "auth": {
            "method": "pki | password",
            "credentials": {
              "user": "<user-name>",
              "privKeyPath": "/path/to/private/key",
              "password": "<password>"
            }
          }
        }
      }
    ]
  }
]
```

**Note**  
Specify `privKeyPath` only if `method` is set to `pki`\.  
Specify `password` only if `method` is set to `password`\.

All fields that contain values are required, as described here:

`id`  
A user\-defined alphanumeric ID that uniquely identifies a collection of devices called a *device pool*\. Devices that belong to a pool must have identical hardware\. When you run a suite of tests, devices in the pool are used to parallelize the workload\. Multiple devices are used to run different tests\.

`sku`  
An alphanumeric value that uniquely identifies the device under test\. The SKU is used to track qualified boards\.  
If you want to list your device in the AWS Partner Device Catalog, the SKU you specify here must match the SKU that you use in the listing process\.

`features`  
An array that contains the device's supported features\. All features are required\.    
`arch`  
Supported operating system architectures:  
+ `x86_64`
+ `armv6l`
+ `armv7l`
+ `aarch64`

`devices.id`  
A user\-defined unique identifier for the device being tested\.

`devices.operatingSystem`  
The device operating system\. Currently, the only supported value is `linux`\.

`connectivity.protocol`  
The communication protocol used to communicate with this device\. Currently, the only supported value is `ssh` for physical devices\.

`connectivity.ip`  
The IP address of the device being tested\.  
<a name="connectivity-protocol-ssh-only"></a>This property applies only if `connectivity.protocol` is set to `ssh`\.

`connectivity.port`  
Optional\. The port number to use for SSH connections\.  
The default value is 22\.  
This property applies only if `connectivity.protocol` is set to `ssh`\.

`connectivity.auth`  
Authentication information for the connection\.  
<a name="connectivity-protocol-ssh-only"></a>This property applies only if `connectivity.protocol` is set to `ssh`\.    
`connectivity.auth.method`  
The authentication method used to access a device over the given connectivity protocol\.  
Supported values are:  
+ `pki`
+ `password`  
`connectivity.auth.credentials`  
The credentials used for authentication\.    
`connectivity.auth.credentials.password`  
The password used for signing in to the device being tested\.  
This value applies only if `connectivity.auth.method` is set to `password`\.  
`connectivity.auth.credentials.privKeyPath`  
The full path to the private key used to sign in to the device under test\.  
This value applies only if `connectivity.auth.method` is set to `pki`\.  
`connectivity.auth.credentials.user`  
The user name for signing in to the device being tested\.

## Configure userdata\.json<a name="userdata-config"></a>

IDT for AWS IoT Greengrass also needs additional information about the location of test artifacts and AWS IoT Greengrass software versions\.

You must provide this information using the `userdata.json` template located in ` <device_tester_extract_location>/configs/userdata.json`:

```
{
    "TempResourcesDirOnDevice": "/path/to/temp/folder",
    "InstallationDirRootOnDevice": "/path/to/installation/folder",
    "GreengrassNucleusZip": "/path/to/aws.greengrass.nucleus.zip",
    "GreengrassNucleusVersion": "nucleus-version",
    "GreengrassCliVersion": "cli-version"
}
```

All fields that contain values are required as described here:

`TempResourcesDirOnDevice`  
The full path to a temporary folder on the device under test in which to store test artifacts\. Make sure that sudo permissions are not required to write to this directory\.   
The contents of this folder are deleted when IDT finishes running a test\.

`InstallationRootOnDevice`  
The full path to a folder on the device in which to install AWS IoT Greengrass\.

`GreengrassNucleusZip`  
The full path to the Greengrass nucleus ZIP \(`greengrass-nucleus-latest.zip`\) file on your host computer\.

`GreengrassNucleusVersion`  
The version of the downloaded Greengrass nucleus component\. To check the version of the nucleus in the `greengrass-nucleus-latest.zip` file that you downloaded, extract the ZIP file and run the following command\.  

```
java -jar /path/to/extracted/nucleus/zip/Greengrass.jar —version
```

`GreengrassCliVersion`  
The version of the Greengrass CLI component that IDT uses for device qualification\. Use the same value as `GreengrassNucleusVersion`\. 

**Note**  
For information about the supported versions of the Greengrass nucleus and Greengrass CLI for IDT for AWS IoT Greengrass, see [Latest IDT version for AWS IoT Greengrass](dev-test-versions.md#idt-latest-version)\.