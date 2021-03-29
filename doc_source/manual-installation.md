# Manual installation<a name="manual-installation"></a>

The AWS IoT Greengrass Core software includes an installer that sets up your device as a Greengrass core device\. To set up a device manually, you can create the required AWS IoT and IAM resources for the device to use\. If you create these resources manually, you don't need to provide AWS credentials to the installer\.

When you manually install the AWS IoT Greengrass Core software, you can also configure the device to use a network proxy or connect to AWS on port 443\. You might need to specify these configuration options if your device runs behind a firewall or a network proxy, for example\. For more information, see [Connect on port 443 or through a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy)\.

**Important**  <a name="install-greengrass-core-requirements-note"></a>
Before you download the AWS IoT Greengrass Core software, check that your core device meets the [requirements](setting-up.md#greengrass-v2-requirements) to install and run the AWS IoT Greengrass Core software v2\.0\.

**Topics**
+ [Create an AWS IoT thing](#create-iot-thing)
+ [Create a token exchange role](#create-token-exchange-role)
+ [Download certificates to the device](#download-thing-certificates)
+ [Download the AWS IoT Greengrass Core software](#download-greengrass-core-v2)
+ [Install the AWS IoT Greengrass Core software](#run-greengrass-core-v2-installer-manual)

## Create an AWS IoT thing<a name="create-iot-thing"></a>

AWS IoT *things* represent devices and logical entities that connect to AWS IoT\. Greengrass core devices are AWS IoT things\. When you register a device as an AWS IoT thing, that device can use a digital certificate to authenticate with AWS\. This certificate allows the device to communicate with AWS IoT and AWS IoT Greengrass\.

In this section, you create an AWS IoT thing and download certificates that your device can use to connect to AWS\.

**To create an AWS IoT thing**

1. Create an AWS IoT thing for your device\. On your development computer, run the following command\.
   + Replace *GreengrassCore* with the thing name to use\. This name is also the name of your Greengrass core device\.

   ```
   aws iot create-thing --thing-name MyGreengrassCore
   ```

   The response looks similar to the following example if the request succeeds\.

   ```
   {
     "thingName": "MyGreengrassCore",
     "thingArn": "arn:aws:iot:us-west-2:123456789012:thing/MyGreengrassCore",
     "thingId": "8cb4b6cd-268e-495d-b5b9-1713d71dbf42"
   }
   ```

1. Create a folder where you download the certificates for the AWS IoT thing\.

   ```
   mkdir greengrass-v2-certs
   ```

1. Create and download the certificates for the AWS IoT thing\.

   ```
   aws iot create-keys-and-certificate --set-as-active --certificate-pem-outfile greengrass-v2-certs/device.pem.crt --public-key-outfile greengrass-v2-certs/public.pem.key --private-key-outfile greengrass-v2-certs/private.pem.key
   ```

   The response looks similar to the following example if the request succeeds\.

   ```
   {
     "certificateArn": "arn:aws:iot:us-west-2:123456789012:cert/aa0b7958770878eabe251d8a7ddd547f4889c524c9b574ab9fbf65f32248b1d4",
     "certificateId": "aa0b7958770878eabe251d8a7ddd547f4889c524c9b574ab9fbf65f32248b1d4",
     "certificatePem": "-----BEGIN CERTIFICATE-----
   MIICiTCCAfICCQD6m7oRw0uXOjANBgkqhkiG9w
    0BAQUFADCBiDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdTZ
    WF0dGxlMQ8wDQYDVQQKEwZBbWF6b24xFDASBgNVBAsTC0lBTSBDb25zb2xlMRIw
    EAYDVQQDEwlUZXN0Q2lsYWMxHzAdBgkqhkiG9w0BCQEWEG5vb25lQGFtYXpvbi5
    jb20wHhcNMTEwNDI1MjA0NTIxWhcNMTIwNDI0MjA0NTIxWjCBiDELMAkGA1UEBh
    MCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdTZWF0dGxlMQ8wDQYDVQQKEwZBb
    WF6b24xFDASBgNVBAsTC0lBTSBDb25zb2xlMRIwEAYDVQQDEwlUZXN0Q2lsYWMx
    HzAdBgkqhkiG9w0BCQEWEG5vb25lQGFtYXpvbi5jb20wgZ8wDQYJKoZIhvcNAQE
    BBQADgY0AMIGJAoGBAMaK0dn+a4GmWIWJ21uUSfwfEvySWtC2XADZ4nB+BLYgVI
    k60CpiwsZ3G93vUEIO3IyNoH/f0wYK8m9TrDHudUZg3qX4waLG5M43q7Wgc/MbQ
    ITxOUSQv7c7ugFFDzQGBzZswY6786m86gpEIbb3OhjZnzcvQAaRHhdlQWIMm2nr
    AgMBAAEwDQYJKoZIhvcNAQEFBQADgYEAtCu4nUhVVxYUntneD9+h8Mg9q6q+auN
    KyExzyLwaxlAoo7TJHidbtS4J5iNmZgXL0FkbFFBjvSfpJIlJ00zbhNYS5f6Guo
    EDmFJl0ZxBHjJnyp378OD8uTs7fLvjx79LjSTbNYiytVbZPQUQ5Yaxu2jXnimvw
    3rrszlaEXAMPLE=
   -----END CERTIFICATE-----",
     "keyPair": {
       "PublicKey": "-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkEXAMPLEQEFAAOCAQ8AMIIBCgKCAQEAEXAMPLE1nnyJwKSMHw4h\nMMEXAMPLEuuN/dMAS3fyce8DW/4+EXAMPLEyjmoF/YVF/gHr99VEEXAMPLE5VF13\n59VK7cEXAMPLE67GK+y+jikqXOgHh/xJTwo+sGpWEXAMPLEDz18xOd2ka4tCzuWEXAMPLEahJbYkCPUBSU8opVkR7qkEXAMPLE1DR6sx2HocliOOLtu6Fkw91swQWEXAMPLE\GB3ZPrNh0PzQYvjUStZeccyNCx2EXAMPLEvp9mQOUXP6plfgxwKRX2fEXAMPLEDa\nhJLXkX3rHU2xbxJSq7D+XEXAMPLEcw+LyFhI5mgFRl88eGdsAEXAMPLElnI9EesG\nFQIDAQAB\n-----END PUBLIC KEY-----\n",
       "PrivateKey": "-----BEGIN RSA PRIVATE KEY-----\nkey omitted for security reasons\n-----END RSA PRIVATE KEY-----\n"
     }
   }
   ```

1. Attach the certificate to the AWS IoT thing\.
   + Replace *GreengrassCore* with the name of your AWS IoT thing\.
   + Replace the certificate Amazon Resource Name \(ARN\) with the ARN of the certificate that you created in the previous step\.

   ```
   aws iot attach-thing-principal --thing-name MyGreengrassCore --principal arn:aws:iot:us-west-2:123456789012:cert/aa0b7958770878eabe251d8a7ddd547f4889c524c9b574ab9fbf65f32248b1d4
   ```

   The command doesn't have any output if the request succeeds\.

1. Create and attach an AWS IoT policy that defines the AWS IoT permissions for your Greengrass core device\. If you have set up a Greengrass core device before, you can attach its AWS IoT policy instead of creating a new one\. Do the following:

   1. \(Optional\) Create a file that contains the AWS IoT policy document that Greengrass core devices require\.

      ```
      nano greengrass-v2-iot-policy.json
      ```

      Copy the following JSON into the file\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "iot:Publish",
              "iot:Subscribe",
              "iot:Receive",
              "iot:Connect",
              "greengrass:*"
            ],
            "Resource": [
              "*"
            ]
          }
        ]
      }
      ```

   1. \(Optional\) Create an AWS IoT policy from the policy document\.
      + Replace *GreengrassV2IoTThingPolicy* with the name of the policy to create\.

      ```
      aws iot create-policy --policy-name GreengrassV2IoTThingPolicy --policy-document file://greengrass-v2-iot-policy.json
      ```

      The response looks similar to the following example if the request succeeds\.

      ```
      {
        "policyName": "GreengrassV2IoTThingPolicy",
        "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/GreengrassV2IoTThingPolicy",
        "policyDocument": "{
          \"Version\": \"2012-10-17\",
          \"Statement\": [
            {
              \"Effect\": \"Allow\",
              \"Action\": [
                \"iot:Publish\",
                \"iot:Subscribe\",
                \"iot:Receive\",
                \"iot:Connect\",
                \"greengrass:*\"
              ],
              \"Resource\": [
                \"*\"
              ]
            }
          ]
        }",
        "policyVersionId": "1"
      }
      ```

   1. Attach the AWS IoT policy to the AWS IoT thing's certificate\.
      + Replace *GreengrassV2IoTThingPolicy* with the name of the policy to attach\.
      + Replace the target ARN with the ARN of the certificate for your AWS IoT thing\.

      ```
      aws iot attach-policy --policy-name GreengrassV2IoTThingPolicy --target arn:aws:iot:us-west-2:123456789012:cert/aa0b7958770878eabe251d8a7ddd547f4889c524c9b574ab9fbf65f32248b1d4
      ```

      The command doesn't have any output if the request succeeds\.

1. \(Optional\) Add the AWS IoT thing to a new or existing thing group\. You use thing groups to manage fleets of Greengrass core devices\. When you deploy software components to your devices, you can choose to target individual devices or groups of devices\. You can add a device to a thing group with an active Greengrass deployment to deploy that thing group's software components to the device\. Do the following:

   1. \(Optional\) Create an AWS IoT thing group\.
      + Replace *MyGreengrassCoreGroup* with the name of the thing group to create\.

      ```
      aws iot create-thing-group --thing-group-name MyGreengrassCoreGroup
      ```

      The response looks similar to the following example if the request succeeds\.

      ```
      {
        "thingGroupName": "MyGreengrassCoreGroup",
        "thingGroupArn": "arn:aws:iot:us-west-2:123456789012:thinggroup/MyGreengrassCoreGroup",
        "thingGroupId": "4df721e1-ff9f-4f97-92dd-02db4e3f03aa"
      }
      ```

   1. Add the AWS IoT thing to a thing group\.
      + Replace *MyGreengrassCore* with the name of your AWS IoT thing\.
      + Replace *MyGreengrassCoreGroup* with the name of the thing group\.

      ```
      aws iot add-thing-to-thing-group --thing-name MyGreengrassCore --thing-group-name MyGreengrassCoreGroup
      ```

      The command doesn't have any output if the request succeeds\.

1. Get the AWS IoT endpoints for your AWS account, and save them to use later\. Your device uses these endpoints to connect to AWS IoT\. Do the following:

   1. Get the AWS IoT data endpoint for your AWS account\.

      ```
      aws iot describe-endpoint --endpoint-type iot:Data-ATS
      ```

      The response looks similar to the following example if the request succeeds\.

      ```
      {
        "endpointAddress": "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
      }
      ```

   1. Get the AWS IoT credentials endpoint for your AWS account\.

      ```
      aws iot describe-endpoint --endpoint-type iot:CredentialProvider
      ```

      The response looks similar to the following example if the request succeeds\.

      ```
      {
        "endpointAddress": "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
      }
      ```

## Create a token exchange role<a name="create-token-exchange-role"></a>

Greengrass core devices use an IAM service role, called the *token exchange role*, to authorize calls to AWS services\. The device uses the AWS IoT credentials provider to get temporary AWS credentials for this role, which allows the device to interact with AWS IoT, send logs to Amazon CloudWatch Logs, and download custom component artifacts from Amazon S3\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

You use an AWS IoT *role alias* to configure the token exchange role for a Greengrass core device\. Role aliases enable you to change the token exchange role for a device but keep the device configuration the same\. For more information, see [Authorizing direct calls to AWS services](https://docs.aws.amazon.com/iot/latest/developerguide/authorizing-direct-aws.html) in the *AWS IoT Core Developer Guide*\.

In this section, you create a token exchange IAM role and an AWS IoT role alias that points to the role\. If you have already set up a Greengrass core device, you can use its token exchange role and role alias instead of creating new ones\. Then, you configure your device's AWS IoT thing to use that role and alias\.

**To create a token exchange IAM role**

1. \(Optional\) Create an IAM role that your device can use as a token exchange role\. Do the following:

   1. Create a file that contains the trust policy document that the token exchange role requires\.

      ```
      nano device-role-trust-policy.json
      ```

      Copy the following JSON into the file\.

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

   1. Create the token exchange role with the trust policy document\.
      + Replace *MyGreengrassV2TokenExchangeRole* with the name of the IAM role to create\.

      ```
      aws iam create-role --role-name MyGreengrassV2TokenExchangeRole --assume-role-policy-document file://device-role-trust-policy.json
      ```

      The response looks similar to the following example if the request succeeds\.

      ```
      {
        "Role": {
          "Path": "/",
          "RoleName": "MyGreengrassV2TokenExchangeRole",
          "RoleId": "AROAZ2YMUHYHK5OKM77FB",
          "Arn": "arn:aws:iam::123456789012:role/MyGreengrassV2TokenExchangeRole",
          "CreateDate": "2021-02-06T00:13:29+00:00",
          "AssumeRolePolicyDocument": {
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
        }
      }
      ```

   1. Create a file that contains the access policy document that the token exchange role requires\.

      ```
      nano device-role-access-policy.json
      ```

      Copy the following JSON into the file\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "iot:DescribeCertificate",
              "logs:CreateLogGroup",
              "logs:CreateLogStream",
              "logs:PutLogEvents",
              "logs:DescribeLogStreams",
              "iot:Connect",
              "iot:Publish",
              "iot:Subscribe",
              "iot:Receive",
              "s3:GetBucketLocation"
            ],
            "Resource": "*"
          }
        ]
      }
      ```
**Note**  
This access policy doesn't allow access to component artifacts in S3 buckets\. To deploy custom components that define artifacts in S3, you must add permissions to the role to allow your core device to retrieve component artifacts\. For more information, see [Allow access to S3 buckets for component artifacts](device-service-role.md#device-service-role-access-s3-bucket)\.  
If you don't yet have an S3 bucket for component artifacts, you can add these permissions later after you create a bucket\.

   1. Create the IAM policy from the policy document\.
      + Replace *MyGreengrassV2TokenExchangeRoleAccess* with the name of the IAM policy to create\.

      ```
      aws iam create-policy --policy-name MyGreengrassV2TokenExchangeRoleAccess --policy-document file://device-role-access-policy.json
      ```

      The response looks similar to the following example if the request succeeds\.

      ```
      {
        "Policy": {
          "PolicyName": "MyGreengrassV2TokenExchangeRoleAccess",
          "PolicyId": "ANPAZ2YMUHYHACI7C5Z66",
          "Arn": "arn:aws:iam::123456789012:policy/MyGreengrassV2TokenExchangeRoleAccess",
          "Path": "/",
          "DefaultVersionId": "v1",
          "AttachmentCount": 0,
          "PermissionsBoundaryUsageCount": 0,
          "IsAttachable": true,
          "CreateDate": "2021-02-06T00:37:17+00:00",
          "UpdateDate": "2021-02-06T00:37:17+00:00"
        }
      }
      ```

   1. Attach the IAM policy to the token exchange role\.
      + Replace *MyGreengrassV2TokenExchangeRole* with the name of the IAM role\.
      + Replace the policy ARN with the ARN of the IAM policy that you created in the previous step\.

      ```
      aws iam attach-role-policy --role-name MyGreengrassV2TokenExchangeRole --policy-arn arn:aws:iam::123456789012:policy/MyGreengrassV2TokenExchangeRoleAccess
      ```

      The command doesn't have any output if the request succeeds\.

1. \(Optional\) Create an AWS IoT role alias that points to the token exchange role\.
   + Replace *MyGreengrassCoreTokenExchangeRoleAlias* with the name of the role alias to create\.
   + Replace the role ARN with the ARN of the IAM role that you created in the previous step\.

   ```
   aws iot create-role-alias --role-alias MyGreengrassCoreTokenExchangeRoleAlias --role-arn arn:aws:iam::123456789012:role/MyGreengrassV2TokenExchangeRole
   ```

   The response looks similar to the following example if the request succeeds\.

   ```
   {
     "roleAlias": "MyGreengrassCoreTokenExchangeRoleAlias",
     "roleAliasArn": "arn:aws:iot:us-west-2:123456789012:rolealias/MyGreengrassCoreTokenExchangeRoleAlias"
   }
   ```
**Note**  
To create a role alias, you must have permission to pass the token exchange IAM role to AWS IoT\. If you receive an error when you try to create a role alias, check that your AWS user has this permission\. For more information, see [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *AWS Identity and Access Management User Guide*\.

1. Create and attach an AWS IoT policy that allows your Greengrass core device to use the role alias to assume the token exchange role\. If you have set up a Greengrass core device before, you can attach its role alias AWS IoT policy instead of creating a new one\. Do the following:

   1. \(Optional\) Create a file that contains the AWS IoT policy document that the role alias requires\.

      ```
      nano greengrass-v2-iot-role-alias-policy.json
      ```

      Copy the following JSON into the file\.
      + Replace the resource ARN with the ARN of your role alias\.

      ```
      {
        "Version":"2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": "iot:AssumeRoleWithCertificate",
            "Resource": "arn:aws:iot:us-west-2:123456789012:rolealias/MyGreengrassCoreTokenExchangeRoleAlias"
          }
        ]
      }
      ```

   1. \(Optional\) Create an AWS IoT policy from the policy document\.
      + Replace *MyGreengrassCoreTokenExchangeRoleAliasPolicy* with the name of the AWS IoT policy to create\.

      ```
      aws iot create-policy --policy-name MyGreengrassCoreTokenExchangeRoleAliasPolicy --policy-document file://greengrass-v2-iot-role-alias-policy.json
      ```

      The response looks similar to the following example if the request succeeds\.

      ```
      {
        "policyName": "MyGreengrassCoreTokenExchangeRoleAliasPolicy",
        "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/MyGreengrassCoreTokenExchangeRoleAliasPolicy",
        "policyDocument": "{
          \"Version\":\"2012-10-17\",
          \"Statement\": [
            {
              \"Effect\": \"Allow\",
              \"Action\": \"iot:AssumeRoleWithCertificate\",
              \"Resource\": \"arn:aws:iot:us-west-2:123456789012:rolealias/MyGreengrassCoreTokenExchangeRoleAlias\"
            }
          ]
        }",
        "policyVersionId": "1"
      }
      ```

   1. Attach the AWS IoT policy to the AWS IoT thing's certificate\.
      + Replace *MyGreengrassCoreTokenExchangeRoleAliasPolicy* with the name of the role alias AWS IoT policy\.
      + Replace the target ARN with the ARN of the certificate for your AWS IoT thing\.

      ```
      aws iot attach-policy --policy-name MyGreengrassCoreTokenExchangeRoleAliasPolicy --target arn:aws:iot:us-west-2:123456789012:cert/aa0b7958770878eabe251d8a7ddd547f4889c524c9b574ab9fbf65f32248b1d4
      ```

      The command doesn't have any output if the request succeeds\.

## Download certificates to the device<a name="download-thing-certificates"></a>

Earlier, you downloaded your device's certificates to your development computer\. In this section, you copy these certificates to your device set up the device with the certificates that it uses to connect to AWS IoT\.

**To download certificates to the device**

1. Copy the AWS IoT thing certificates from your development computer to the device\. You might be able to use the `scp` command, for example\.
   + Replace *device\-ip\-address* with the IP of your device\.

   ```
   scp -r greengrass-v2-certs/ device-ip-address:~
   ```

1. On the device, download the Amazon root certificate authority \(CA\) certificate\. AWS IoT certificates are associated with Amazon's root CA certificate by default\.

   ```
   curl -o ~/greengrass-v2-certs/AmazonRootCA1.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
   ```

1. Create the Greengrass root folder on the device\. You install the AWS IoT Greengrass Core software to this folder later\.
   + Replace */greengrass/v2* with the folder to use\.

   ```
   sudo mkdir -p /greengrass/v2
   ```

1. Set the permissions of the parent of the Greengrass root folder\.
   + Replace */greengrass* with the parent of the root folder\.

   ```
   sudo chmod 755 /greengrass
   ```

1. Copy the AWS IoT thing certificates to the Greengrass root folder\.
   + Replace */greengrass/v2* with the Greengrass root folder\.

   ```
   sudo cp -R ~/greengrass-v2-certs/* /greengrass/v2
   ```

## Download the AWS IoT Greengrass Core software<a name="download-greengrass-core-v2"></a>

You can download the latest version of the AWS IoT Greengrass Core software from the following location:
+ [https://d2s8p88vqu9w66\.cloudfront\.net/releases/greengrass\-nucleus\-latest\.zip](https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip)

**Note**  
You can download a specific version of the AWS IoT Greengrass Core software from the following location\. Replace *version* with the version to download\.  

```
https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-version.zip
```

**To download the AWS IoT Greengrass Core software \(Linux\)**

1. On your device, download the AWS IoT Greengrass Core software to a file named `greengrass-nucleus-latest-zip`\.

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

   <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

1. Unzip the AWS IoT Greengrass Core software to a folder on your device\. Replace *GreengrassCore* with the folder that you want to use\.

   ```
   unzip greengrass-nucleus-latest.zip -d GreengrassCore && rm greengrass-nucleus-latest.zip
   ```
**Tip**  
You can run the following command to see the version of the AWS IoT Greengrass Core software\.  

   ```
   java -jar ./GreengrassCore/lib/Greengrass.jar --version
   ```

## Install the AWS IoT Greengrass Core software<a name="run-greengrass-core-v2-installer-manual"></a>

Run the installer with arguments that specify the following actions:
+ Install from a partial configuration file that specifies to use the AWS resources and certificates that you created earlier\. The AWS IoT Greengrass Core software uses a configuration file that specifies the configuration of every Greengrass component on the device\. The installer creates a complete configuration file from the partial configuration file that you provide\.
+ <a name="install-argument-component-default-user"></a>Use the `ggc_user` system user and `ggc_group` system group to run software components on the core device\. The installer creates this default user and group if they don't exist\.
+ <a name="install-argument-setup-system-service"></a>Install the software as a system service that runs on boot, if your device has the [systemd](https://en.wikipedia.org/wiki/Systemd) init system\.

For more information about the arguments that you can specify, see [Installer arguments](configure-installer.md)\.

**Note**  
<a name="jvm-tuning-note"></a>If you are running AWS IoT Greengrass on a device with limited memory, you can control the amount of memory that AWS IoT Greengrass Core software uses\. To control memory allocation, you can set JVM heap size options in the `jvmOptions` configuration parameter in your nucleus component\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.

**To install the AWS IoT Greengrass Core software \(Linux\)**

1. Check the version of the AWS IoT Greengrass Core software\.
   + Replace *GreengrassCore* with the path to the folder that contains the software\.

   ```
   java -jar ./GreengrassCore/lib/Greengrass.jar --version
   ```

1. Create the configuration file to provide to the installer\.

   ```
   nano GreengrassCore/config.yaml
   ```

   Copy the following YAML into the file\. This partial configuration file specifies system parameters and Greengrass nucleus parameters\.

   ```
   ---
   system:
     certificateFilePath: "/greengrass/v2/device.pem.crt"
     privateKeyPath: "/greengrass/v2/private.pem.key"
     rootCaPath: "/greengrass/v2/AmazonRootCA1.pem"
     rootpath: "/greengrass/v2"
     thingName: "MyGreengrassCore"
   services:
     aws.greengrass.Nucleus:
       componentType: "NUCLEUS"
       version: "2.0.5"
       configuration:
         awsRegion: "us-west-2"
         iotRoleAlias: "MyGreengrassCoreTokenExchangeRoleAlias"
         iotDataEndpoint: "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
         iotCredEndpoint: "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
   ```

   Then, do the following:
   + Replace each instance of */greengrass/v2* with the Greengrass root folder\.
   + Replace *MyGreengrassCore* with the name of the AWS IoT thing\.
   + Replace *2\.0\.5* with the version of the AWS IoT Greengrass Core software\.
   + Replace *us\-west\-2* with the AWS Region where you created the resources\.
   + Replace *MyGreengrassCoreTokenExchangeRoleAlias* with the token exchange role alias\.
   + Replace the `iotDataEndpoint` with your AWS IoT data endpoint\.
   + Replace the `iotCredEndpoint` with your AWS IoT credentials endpoint\.
**Note**  
In this configuration file, you can customize other nucleus configuration options such as the ports and network proxy to use, as shown in the following example\. For more information, see [Greengrass nucleus configuration](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration)\.  

   ```
   ---
   system:
     certificateFilePath: "/greengrass/v2/device.pem.crt"
     privateKeyPath: "/greengrass/v2/private.pem.key"
     rootCaPath: "/greengrass/v2/AmazonRootCA1.pem"
     rootpath: "/greengrass/v2"
     thingName: "MyGreengrassCore"
   services:
     aws.greengrass.Nucleus:
       componentType: "NUCLEUS"
       version: "2.0.5"
       configuration:
         awsRegion: "us-west-2"
         iotCredEndpoint: "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
         iotDataEndpoint: "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
         iotRoleAlias: "MyGreengrassCoreTokenExchangeRoleAlias"
         mqtt:
           port: 443
         greengrassDataPlanePort: 443
         networkProxy:
           noProxyAddresses: "http://192.168.0.1,www.example.com"
           proxy:
             url: "https://my-proxy-server:1100"
             username: "Mary_Major"
             password: "pass@word1357"
   ```

1. Run the installer, and specify `--init-config` to provide the configuration file\.
   + Replace */greengrass/v2* with the Greengrass root folder\.
   + Replace each instance of *GreengrassCore* with the folder where you unpacked the installer\.

   ```
   sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE \
     -jar ./GreengrassCore/lib/Greengrass.jar \
     --init-config ./GreengrassCore/config.yaml \
     --component-default-user ggc_user:ggc_group \
     --setup-system-service true
   ```

   If you specify `--setup-system-service true`, the installer prints `Successfully set up Nucleus as a system service` if it set up and ran the software as a system service\. Otherwise, the installer doesn't output any message if it installs the software successfully\.

1. View the files in the root folder to verify the installation\.

   ```
   ls /greengrass/v2
   ```

   If the installation succeeded, the root folder contains several folders, such as `config`, `packages`, and `logs`\.

<a name="install-greengrass-core-run-software"></a>If you installed the AWS IoT Greengrass Core software as a system service, the installer runs the software for you\. Otherwise, you must run the software manually\. For more information, see [Run the AWS IoT Greengrass Core software](run-greengrass-core-v2.md)\.

<a name="install-greengrass-core-next-steps-intro"></a>For more information about how to configure and use the software and AWS IoT Greengrass, see the following:<a name="install-greengrass-core-next-steps-links"></a>
+ [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)
+ [Manage AWS IoT Greengrass components](manage-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
+ [Greengrass Command Line Interface](gg-cli.md)