# Configure IDT settings to run the AWS IoT Greengrass qualification suite<a name="set-config"></a>

Before you run tests, you must configure settings for AWS credentials and devices on your host computer\.

## Configure AWS credentials in config\.json<a name="cfg-aws-gg"></a>

You must configure your IAM user credentials in the `<device_tester_extract_location>/configs/config.json` file\. Use the credentials for the IDT for AWS IoT Greengrass V2 user created in [Create and configure an AWS account](dev-tst-prereqs.md#config-aws-account-for-idt)\. You can specify your credentials in one of two ways:
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

To configure IDT for AWS IoT Greengrass V2 to use AWS credentials from your `credentials` file, edit your `config.json` file as follows:

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

Environment variables are variables maintained by the operating system and used by system commands\. They are not saved if you close the SSH session\. IDT for AWS IoT Greengrass V2 can use the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables to store your AWS credentials\.

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

In addition to AWS credentials, IDT for AWS IoT Greengrass V2 needs information about the devices that tests are run on\. Example information would be IP address, login information, operating system, and CPU architecture\.

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
      },
      {
        "name": "ml",
        "value": "dlr | tensorflowlite | dlr,tensorflowlite | no"
      },
      {
        "name": "docker",
        "value": "yes | no"
      },
      {
        "name": "streamManagement",
        "value": "yes | no"
      }, 
      {
        "name": "hsi", 
        "value": "hsm | no" 
      }
    ],
    "devices": [
      {
        "id": "<device-id>",
        "operatingSystem": "Linux | Windows",
        "connectivity": {
          "protocol": "ssh",
          "ip": "<ip-address>",
          "port": 22,
          "publicKeyPath": "<public-key-path>",
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

All properties that contain values are required, as described here:

`id`  
A user\-defined alphanumeric ID that uniquely identifies a collection of devices called a *device pool*\. Devices that belong to a pool must have identical hardware\. When you run a suite of tests, devices in the pool are used to parallelize the workload\. Multiple devices are used to run different tests\.

`sku`  
An alphanumeric value that uniquely identifies the device under test\. The SKU is used to track qualified boards\.  
If you want to list your device in the AWS Partner Device Catalog, the SKU you specify here must match the SKU that you use in the listing process\.

`features`  
An array that contains the device's supported features\. All features are required\.    
`arch`  
The supported operating system architectures that the test run validates\. Valid values are:  
+ `x86_64`
+ `armv6l`
+ `armv7l`
+ `aarch64`  
`ml`  
<a name="description-ml"></a>Validates that the device meets all of the required technical dependencies to use the AWS\-provided machine learning \(ML\) components\.  
Enabling this feature also validates <a name="description-ml-inference-phrase"></a>that the device can perform ML inference using the [Deep Learning Runtime](https://github.com/neo-ai/neo-ai-dlr) and [TensorFlow Lite](https://www.tensorflow.org/lite/guide/python) ML frameworks\.  
Valid values are any combination of `dlr` and `tensorflowlite`, or `no`\.  
`docker`  
<a name="description-docker"></a>Validates that the device meets all required technical dependencies to use the AWS\-provided Docker application manager \(`aws.greengrass.DockerApplicationManager`\) component\.  
Enabling this feature also validates <a name="description-docker-app-manager-qual-phrase"></a>that the device can download a Docker container image from Amazon ECR \.  
Valid values are any combination of `yes` or `no`\.  
`streamManagement`  
<a name="description-sm"></a>Validates that the device can download, install, and run the [AWS IoT Greengrass stream manager](manage-data-streams.md)\.  
Valid values are any combination of `yes` or `no`\.  
`hsi`  
<a name="description-hsi"></a>Validates that the device can authenticate connections to the AWS IoT and AWS IoT Greengrass services using a private key and certificate that are stored in a hardware security module \(HSM\)\. This test also verifies that the AWS\-provided [PKCS\#11 provider component](pkcs11-provider-component.md) can interface with the HSM using a vendor\-provided PKCS\#11 library\. For more information, see [Hardware security integration](hardware-security.md)\.  
Valid values are `hsm` or `no`\.
IDT v4\.2\.0 and later versions support testing the `ml`, `docker`, and `streamManagement` features\. If you don't want to test these features, set the corresponding value to `no`\. 
Testing the `hsi` is available only with IDT v4\.5\.1 and later versions\.

`devices.id`  
A user\-defined unique identifier for the device being tested\.

`devices.operatingSystem`  
The device operating system\. Supported values are `Linux` and `Windows`\.

`connectivity.protocol`  
The communication protocol used to communicate with this device\. Currently, the only supported value is `ssh` for physical devices\.

`connectivity.ip`  
The IP address of the device being tested\.  
<a name="connectivity-protocol-ssh-only"></a>This property applies only if `connectivity.protocol` is set to `ssh`\.

`connectivity.port`  
Optional\. The port number to use for SSH connections\.  
The default value is 22\.  
This property applies only if `connectivity.protocol` is set to `ssh`\.

`connectivity.publicKeyPath`  
Optional\. The full path to the public key used to authenticate connections to the device under test\.   
When you specify the `publicKeyPath`, IDT validates the device’s public key when it establishes an SSH connection to the device under test\. If this value is not specified, IDT creates an SSH connection, but doesn’t validate the device’s public key\.   
We strongly recommend that you specify the path to the public key, and that you use a secure method to fetch this public key\. For standard command line\-based SSH clients, the public key is provided in the `known_hosts` file\. If you specify a separate public key file, this file must use the same format as the `known_hosts` file, that is, ` ip-address key-type public-key`\.

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

IDT for AWS IoT Greengrass V2 also needs additional information about the location of test artifacts and AWS IoT Greengrass software\.

You must provide this information using the `userdata.json` template located in ` <device_tester_extract_location>/configs/userdata.json`:

```
{
    "TempResourcesDirOnDevice": "/path/to/temp/folder",
    "InstallationDirRootOnDevice": "/path/to/installation/folder",
    "GreengrassNucleusZip": "/path/to/aws.greengrass.nucleus.zip",
    "PreInstalled": "yes/no",
    "GreengrassV2TokenExchangeRole": "custom-iam-role-name",
	"hsm": {
        "greengrassPkcsPluginJar": "/path/to/aws.greengrass.crypto.Pkcs11Provider-latest.jar",
        "pkcs11ProviderLibrary": "/path/to/pkcs11-vendor-library",
        "slotId": "slot-id",
        "slotLabel": "slot-label",
        "slotUserPin": "slot-pin",
        "keyLabel": "key-label",
        "preloadedCertificateArn": "certificate-arn"
        "rootCA": "path/to/root-ca"
    }
}
```

All properties that contain values are required as described here:

`TempResourcesDirOnDevice`  
The full path to a temporary folder on the device under test in which to store test artifacts\. Make sure that sudo permissions are not required to write to this directory\.   
IDT deletes the contents of this folder when it finishes running a test\.

`InstallationDirRootOnDevice`  
The full path to a folder on the device in which to install AWS IoT Greengrass\. For PreInstalled Greengrass, this is the path to the Greengrass installation directory\.  
You must set the required file permissions for this folder\. Run the following command for each folder in the installation path\.  

```
sudo chmod 755 folder-name
```

`GreengrassNucleusZip`  
The full path to the Greengrass nucleus ZIP \(`greengrass-nucleus-latest.zip`\) file on your host computer\. This field is not required for testing with PreInstalled Greengrass\.  
For information about the supported versions of the Greengrass nucleus for IDT for AWS IoT Greengrass, see [Latest IDT version for AWS IoT Greengrass V2](dev-test-versions.md#idt-latest-version)\. To download the latest Greengrass software, see [Download the AWS IoT Greengrass software](https://docs.aws.amazon.com/greengrass/v2/developerguide/dev-tst-prereqs.html#config-gg)\.

`PreInstalled`  
This feature is available for IDT v4\.5\.8 and later versions on Linux devices only\.  
\(Optional\) When the value is *yes*, IDT will assume the path mentioned in `InstallationDirRootOnDevice` to be the directory where Greengrass is installed\.  
For more information about how to install Greengrass on your device, see [Install AWS IoT Greengrass Core software with automatic resource provisioning](quick-installation.md)\. IDT assumes that the thing and thing group are created during installation setup\. Make sure that these values are reflected in the `effectiveConfig.yaml` file\. IDT checks for the file `effectiveConfig.yaml` under `<InstallationDirRootOnDevice>/config/effectiveConfig.yaml`\.  
For running tests with HSM, make sure that the `aws.greengrass.crypto.Pkcs11Provider` field is updated in `effectiveConfig.yaml`\.

  `GreengrassV2TokenExchangeRole`  
\(Optional\) The custom IAM role that you want to use as the token exchange role that the device under test assumes to interact with AWS resources\.   
IDT uses this custom IAM role instead of creating the default token exchange role during the test run\. If you use a custom role, you can update the [IAM permissions for the test user](dev-tst-prereqs.md#configure-idt-permissions) to exclude the `iamResourcesUpdate` statement that allows the user to create and delete IAM roles and policies\. 
For more information about creating a custom IAM role as your token exchange role, see [Configure a custom token exchange role](device-config-setup.md#configure-custom-tes-role-for-idt)\.

`hsm`  
This feature is available for IDT v4\.5\.1 and later\.  
\(Optional\) The configuration information for testing with an AWS IoT Greengrass Hardware Security Module \(HSM\)\. Otherwise, the `hsm` property should be omitted\. For more information, see [Hardware security integration](hardware-security.md)\.  
<a name="connectivity-protocol-ssh-only"></a>This property applies only if `connectivity.protocol` is set to `ssh`\.  
The HSM configuration may be considered sensitive data if the hardware security module is shared between IDT and another system\. In this situation, you may avoid securing these configuration values in plaintext by storing them in an AWS Parameter Store SecureString parameter and configuring IDT to fetch them during test execution\. For more information, see [Fetch configuration from AWS Parameter Store](#fetch-config)  
`hsm.greengrassPkcsPluginJar`  
The full path to the [PKCS\#11 provider component](pkcs11-provider-component.md) that you download to the IDT host machine\. AWS IoT Greengrass provides this component as JAR file that you can download to specify as a provisioning plugin during installation\. You can download the latest version of the component's JAR file as the following URL: [https://d2s8p88vqu9w66\.cloudfront\.net/releases/Pkcs11Provider/aws\.greengrass\.crypto\.Pkcs11Provider\-latest\.jar](https://d2s8p88vqu9w66.cloudfront.net/releases/Pkcs11Provider/aws.greengrass.crypto.Pkcs11Provider-latest.jar)\.  
`hsm.pkcs11ProviderLibrary`  
The full path to the PKCS\#11 library that is provided by the hardware security module \(HSM\) vendor to interact with the HSM\.  
`hsm.slotId`  
The slot ID that is used to identify the HSM slot to which you load the key and certificate\.  
`hsm.slotLabel`  
The slot label that is used to identify the HSM slot to which you load the key and certificate\.  
`hsm.slotUserPin`  
The user PIN that IDT uses to authenticate AWS IoT Greengrass Core software to the HSM\.  
As a security best practice, don't use the same user PIN on production devices\.  
`hsm.keyLabel`  
The label used to identify the key in the hardware module\. Both the key and the certificate must use the same key label\.  
`hsm.preloadedCertificateArn`  
The Amazon Resource Name \(ARN\) of the uploaded device certificate in the AWS IoT cloud\.  
You must have previously generated this certificate using the key in the HSM, imported it into your HSM, and uploaded it to the AWS IoT cloud\. For information about generating and importing the certificate, see the documentation for your HSM\.  
You must upload the certificate to the same account and Region that you provide in [config\.json\.](#cfg-aws-gg)\. For more information about uploading your certificate to AWS IoT, see [Register a client certificate manually](https://docs.aws.amazon.com/iot/latest/developerguide/manual-cert-registration.html) in the *AWS IoT Developer Guide*\.  
`hsm.rootCAPath`  
\(Optional\) The full path on the IDT host machine to the root certificate authority \(CA\) that signed your certificate\. This is required if the certificate in your HSM created is not signed by the Amazon root CA\.

## Fetch configuration from AWS Parameter Store<a name="fetch-config"></a>

AWS IoT Device Tester \(IDT\) includes an optional feature to fetch configuration values from the [AWS Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)\. AWS Parameter Store allows for secure and encrypted storage of configurations\. When configured, IDT can fetch parameters from AWS Parameter Store in place of storing parameters in plaintext inside the `userdata.json` file\. This is useful for any sensitive data that should be stored encrypted, such as: passwords, pins, and other secrets\.

1. To use this feature, you must update the permissions used in creating your [IDT user](https://docs.aws.amazon.com/greengrass/v2/developerguide/dev-tst-prereqs.html) to allow the GetParameter action on the parameters that IDT is configured to use\. The below is an example of a permission statement that can be added to the IDT user\. For more information, see [AWS Systems Manager userguide](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-access.html)\.

   ```
   {
          "Sid":"parameterStoreResources",
          "Effect": "Allow",
           "Action": [
               "ssm:GetParameter"
           ],
           "Resource": "arn:aws:ssm:*:*:parameter/IDT*"
   }
   ```

   The above permission is configured to allow fetching all parameters with a name beginning with `IDT`, by using the wildcard character `*`\. You should customize this to your needs so IDT has access to fetch any configured parameters based on the naming of the parameters you are using\.

1. You need to store your configuration values inside AWS Paramater Store\. This can be done from the AWS console or from the AWS CLI\. AWS Parameter Store allows you to choose encrypted or unencrypted storage\. For storage of sensitive values like secrets, passwords, and pins, you should use the encrypted option which is a parameter type of SecureString\. To upload a parameter using the AWS CLI, you can use the following command:

   ```
   aws ssm put-parameter --name IDT-example-name --value IDT-example-value --type SecureString
   ```

   You can verify that a parameter is stored using the following command\. \(Optional\) Use the `--with-decryption` flag to fetch a decrypted SecureString parameter\.

   ```
   aws ssm get-parameter --name IDT-example-name
   ```

   Using the AWS CLI will upload the parameter in the AWS region of the current CLI user and IDT will fetch parameters from the region configured in `config.json`\. To check your region from the AWS CLI, use the following:

   ```
   aws configure get region
   ```

1. Once you have a configuration value in the AWS Cloud, you can update any value inside the IDT configuration to fetch from the AWS Cloud\. To do so, you use a placeholder in your IDT configuration of the form `{{AWS.Parameter.parameter_name}}` to fetch the parameter by that name from the AWS Parameter Store\.

   For example, suppose you want to use the `IDT-example-name` parameter from Step 2 as the HSM keyLabel in your HSM configuration\. To do this, you can update your `userdata.json` as follows:

   ```
   "hsm": {
           "keyLabel": "{{AWS.Parameter.IDT-example-name}}",
           [...]
       }
   ```

   IDT will fetch the value of this parameter at runtime that was set to `IDT-example-value` in Step 2\. This configuration is similar to setting `"keyLabel": "IDT-example-value"` but, instead, that value is store as encrypted in the AWS Cloud\.