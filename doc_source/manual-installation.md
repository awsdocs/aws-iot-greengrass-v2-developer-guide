# Install AWS IoT Greengrass Core software with manual resource provisioning<a name="manual-installation"></a>

The AWS IoT Greengrass Core software includes an installer that sets up your device as a Greengrass core device\. To set up a device manually, you can create the required AWS IoT and IAM resources for the device to use\. If you create these resources manually, you don't need to provide AWS credentials to the installer\.

When you manually install the AWS IoT Greengrass Core software, you can also configure the device to use a network proxy or connect to AWS on port 443\. You might need to specify these configuration options if your device runs behind a firewall or a network proxy, for example\. For more information, see [Connect on port 443 or through a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy)\.

You can also configure the AWS IoT Greengrass Core software to use a hardware security module \(HSM\) through the [PKCS\#11 interface](https://en.wikipedia.org/wiki/PKCS_11)\. This feature enables you to securely store private key and certificate files so that they aren't exposed or duplicated in software\. You can store private keys and certificates on a hardware module such as an HSM, a Trusted Platform Module \(TPM\), or another cryptographic element\. This feature is available on Linux devices only\. For more information about hardware security and requirements to use it, see [Hardware security integration](hardware-security.md)\.

**Important**  <a name="install-greengrass-core-requirements-note"></a>
Before you download the AWS IoT Greengrass Core software, check that your core device meets the [requirements](setting-up.md#greengrass-v2-requirements) to install and run the AWS IoT Greengrass Core software v2\.0\.

**Topics**
+ [Retrieve AWS IoT endpoints](#retrieve-iot-endpoints)
+ [Create an AWS IoT thing](#create-iot-thing)
+ [Create the thing certificate](#create-thing-certificate)
+ [Configure the thing certificate](#configure-thing-certificate)
+ [Create a token exchange role](#create-token-exchange-role)
+ [Download certificates to the device](#download-thing-certificates)
+ [Set up the device environment](#set-up-device-environment)
+ [Download the AWS IoT Greengrass Core software](#download-greengrass-core-v2)
+ [Install the AWS IoT Greengrass Core software](#run-greengrass-core-v2-installer-manual)

## Retrieve AWS IoT endpoints<a name="retrieve-iot-endpoints"></a>

Get the AWS IoT endpoints for your AWS account, and save them to use later\. Your device uses these endpoints to connect to AWS IoT\. Do the following:

1. Get the AWS IoT data endpoint for your AWS account\.

   ```
   aws iot describe-endpoint --endpoint-type iot:Data-ATS
   ```

   The response looks similar to the following example, if the request succeeds\.

   ```
   {
     "endpointAddress": "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
   }
   ```

1. Get the AWS IoT credentials endpoint for your AWS account\.

   ```
   aws iot describe-endpoint --endpoint-type iot:CredentialProvider
   ```

   The response looks similar to the following example, if the request succeeds\.

   ```
   {
     "endpointAddress": "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
   }
   ```

## Create an AWS IoT thing<a name="create-iot-thing"></a>

AWS IoT *things* represent devices and logical entities that connect to AWS IoT\. Greengrass core devices are AWS IoT things\. When you register a device as an AWS IoT thing, that device can use a digital certificate to authenticate with AWS\.

In this section, you create an AWS IoT thing that represents your device\.

**To create an AWS IoT thing**

1. Create an AWS IoT thing for your device\. On your development computer, run the following command\.
   + Replace *MyGreengrassCore* with the thing name to use\. This name is also the name of your Greengrass core device\.
**Note**  <a name="install-argument-thing-name-constraint"></a>
The thing name can't contain colon \(`:`\) characters\.

   ```
   aws iot create-thing --thing-name MyGreengrassCore
   ```

   The response looks similar to the following example, if the request succeeds\.

   ```
   {
     "thingName": "MyGreengrassCore",
     "thingArn": "arn:aws:iot:us-west-2:123456789012:thing/MyGreengrassCore",
     "thingId": "8cb4b6cd-268e-495d-b5b9-1713d71dbf42"
   }
   ```

1. \(Optional\) Add the AWS IoT thing to a new or existing thing group\. You use thing groups to manage fleets of Greengrass core devices\. When you deploy software components to your devices, you can target individual devices or groups of devices\. You can add a device to a thing group with an active Greengrass deployment to deploy that thing group's software components to the device\. Do the following:

   1. \(Optional\) Create an AWS IoT thing group\.
      + Replace *MyGreengrassCoreGroup* with the name of the thing group to create\.
**Note**  <a name="install-argument-thing-group-name-constraint"></a>
The thing group name can't contain colon \(`:`\) characters\.

      ```
      aws iot create-thing-group --thing-group-name MyGreengrassCoreGroup
      ```

      The response looks similar to the following example, if the request succeeds\.

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

## Create the thing certificate<a name="create-thing-certificate"></a>

<a name="create-thing-certificate-intro-1"></a>When you register a device as an AWS IoT thing, that device can use a digital certificate to authenticate with AWS\. This certificate allows the device to communicate with AWS IoT and AWS IoT Greengrass\.

<a name="create-thing-certificate-intro-2"></a>In this section, you create and download certificates that your device can use to connect to AWS\.

<a name="create-thing-certificate-intro-3"></a>If you want to configure the AWS IoT Greengrass Core software to use a hardware security module \(HSM\) to securely store the private key and certificate, follow the steps to create the certificate from a private key in an HSM\. Otherwise, follow the steps to create the certificate and private key in the AWS IoT service\. The hardware security feature is available on Linux devices only\. For more information about hardware security and requirements to use it, see [Hardware security integration](hardware-security.md)\.

### Create the certificate and private key in the AWS IoT service<a name="create-thing-certificate-cloud"></a><a name="create-thing-certificate-cloud-steps"></a>

**To create the thing certificate**

1. Create a folder where you download the certificates for the AWS IoT thing\.

   ```
   mkdir greengrass-v2-certs
   ```

1. Create and download the certificates for the AWS IoT thing\.

   ```
   aws iot create-keys-and-certificate --set-as-active --certificate-pem-outfile greengrass-v2-certs/device.pem.crt --public-key-outfile greengrass-v2-certs/public.pem.key --private-key-outfile greengrass-v2-certs/private.pem.key
   ```

   The response looks similar to the following example, if the request succeeds\.

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

   Save the certificate's Amazon Resource Name \(ARN\) to use to configure the certificate later\.

### Create the certificate from a private key in an HSM<a name="create-thing-certificate-hardware-security"></a>

**Note**  
This feature is available for v2\.5\.3 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 

**To create the thing certificate**

1. On the core device, initialize a PKCS\#11 token in the HSM, and generate a private key\. The private key must be an RSA key with an RSA\-2048 key size \(or larger\) or an ECC key\.
**Note**  <a name="hardware-security-module-requirements-key-notes"></a>
To use a hardware security module with ECC keys, you must use [Greengrass nucleus](greengrass-nucleus-component.md) v2\.5\.6 or later\.  
To use a hardware security module and [secret manager](secret-manager-component.md), you must use a hardware security module with RSA keys\.

   Check the documentation for your HSM to learn how to initialize the token and generate the private key\. If your HSM supports object IDs, specify an object ID when you generate the private key\. Save the slot ID, user PIN, object label, object ID \(if your HSM uses one\) that you specify when you initialize the token and generate the private key\. You use these values later when you import the thing certificate to the HSM and configure the AWS IoT Greengrass Core software\.

1. Create a certificate signing request \(CSR\) from the private key\. AWS IoT uses this CSR to create a thing certificate for the private key that you generated in the HSM\. For information about how to create a CSR from the private key, see the documentation for your HSM\. The CSR is a file, such as `iotdevicekey.csr`\.

1. Copy the CSR from the device to your development computer\. If SSH and SCP are enabled on the development computer and the device, you can use the `scp` command on your development computer to transfer the CSR\. Replace *device\-ip\-address* with the IP address of your device, and replace *\~/iotdevicekey\.csr* with the path to the CSR file on the device\.

   ```
   scp device-ip-address:~/iotdevicekey.csr iotdevicekey.csr
   ```

1. On your development computer, create a folder where you download the certificate for the AWS IoT thing\.

   ```
   mkdir greengrass-v2-certs
   ```

1. Use the CSR file to create and download the certificate for the AWS IoT thing to your development computer\.

   ```
   aws iot create-certificate-from-csr --set-as-active --certificate-signing-request=file://iotdevicekey.csr --certificate-pem-outfile greengrass-v2-certs/device.pem.crt
   ```

   The response looks similar to the following example, if the request succeeds\.

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
   -----END CERTIFICATE-----"
   }
   ```

   Save the certificate's ARN to use to configure the certificate later\.

## Configure the thing certificate<a name="configure-thing-certificate"></a>

Attach the thing certificate to the AWS IoT thing that you created earlier, and add an AWS IoT policy to the certificate to define the AWS IoT permissions for the core device\.

**To configure the thing's certificate**

1. Attach the certificate to the AWS IoT thing\.
   + Replace *MyGreengrassCore* with the name of your AWS IoT thing\.
   + Replace the certificate Amazon Resource Name \(ARN\) with the ARN of the certificate that you created in the previous step\.

   ```
   aws iot attach-thing-principal --thing-name MyGreengrassCore --principal arn:aws:iot:us-west-2:123456789012:cert/aa0b7958770878eabe251d8a7ddd547f4889c524c9b574ab9fbf65f32248b1d4
   ```

   The command doesn't have any output if the request succeeds\.

1. Create and attach an AWS IoT policy that defines the AWS IoT permissions for your Greengrass core device\. The following policy allows access to all MQTT topics and Greengrass operations, so your device works with custom applications and future changes that require new Greengrass operations\. You can restrict this policy down based on your use case\. For more information, see [Minimal AWS IoT policy for AWS IoT Greengrass V2 core devices](device-auth.md#greengrass-core-minimal-iot-policy)\.

   If you have set up a Greengrass core device before, you can attach its AWS IoT policy instead of creating a new one\.

   Do the following:

   1. Create a file that contains the AWS IoT policy document that Greengrass core devices require\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

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

   1. Create an AWS IoT policy from the policy document\.
      + Replace *GreengrassV2IoTThingPolicy* with the name of the policy to create\.

      ```
      aws iot create-policy --policy-name GreengrassV2IoTThingPolicy --policy-document file://greengrass-v2-iot-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

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

## Create a token exchange role<a name="create-token-exchange-role"></a>

<a name="installation-create-token-exchange-role-intro"></a>Greengrass core devices use an IAM service role, called the *token exchange role*, to authorize calls to AWS services\. The device uses the AWS IoT credentials provider to get temporary AWS credentials for this role, which allows the device to interact with AWS IoT, send logs to Amazon CloudWatch Logs, and download custom component artifacts from Amazon S3\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

<a name="installation-create-token-exchange-role-alias-intro"></a>You use an AWS IoT *role alias* to configure the token exchange role for Greengrass core devices\. Role aliases enable you to change the token exchange role for a device but keep the device configuration the same\. For more information, see [Authorizing direct calls to AWS services](https://docs.aws.amazon.com/iot/latest/developerguide/authorizing-direct-aws.html) in the *AWS IoT Core Developer Guide*\.

In this section, you create a token exchange IAM role and an AWS IoT role alias that points to the role\. If you have already set up a Greengrass core device, you can use its token exchange role and role alias instead of creating new ones\. Then, you configure your device's AWS IoT thing to use that role and alias\.

**To create a token exchange IAM role**

1. <a name="create-token-exchange-role-create-iam-role"></a>Create an IAM role that your device can use as a token exchange role\. Do the following:

   1. Create a file that contains the trust policy document that the token exchange role requires\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

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
      + Replace *GreengrassV2TokenExchangeRole* with the name of the IAM role to create\.

      ```
      aws iam create-role --role-name GreengrassV2TokenExchangeRole --assume-role-policy-document file://device-role-trust-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "Role": {
          "Path": "/",
          "RoleName": "GreengrassV2TokenExchangeRole",
          "RoleId": "AROAZ2YMUHYHK5OKM77FB",
          "Arn": "arn:aws:iam::123456789012:role/GreengrassV2TokenExchangeRole",
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
      ```

   1. Create a file that contains the access policy document that the token exchange role requires\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

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
              "logs:CreateLogGroup",
              "logs:CreateLogStream",
              "logs:PutLogEvents",
              "logs:DescribeLogStreams",
              "s3:GetBucketLocation"
            ],
            "Resource": "*"
          }
        ]
      }
      ```
**Note**  
This access policy doesn't allow access to component artifacts in S3 buckets\. To deploy custom components that define artifacts in Amazon S3, you must add permissions to the role to allow your core device to retrieve component artifacts\. For more information, see [Allow access to S3 buckets for component artifacts](device-service-role.md#device-service-role-access-s3-bucket)\.  
If you don't yet have an S3 bucket for component artifacts, you can add these permissions later after you create a bucket\.

   1. Create the IAM policy from the policy document\.
      + Replace *GreengrassV2TokenExchangeRoleAccess* with the name of the IAM policy to create\.

      ```
      aws iam create-policy --policy-name GreengrassV2TokenExchangeRoleAccess --policy-document file://device-role-access-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "Policy": {
          "PolicyName": "GreengrassV2TokenExchangeRoleAccess",
          "PolicyId": "ANPAZ2YMUHYHACI7C5Z66",
          "Arn": "arn:aws:iam::123456789012:policy/GreengrassV2TokenExchangeRoleAccess",
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
      + Replace *GreengrassV2TokenExchangeRole* with the name of the IAM role\.
      + Replace the policy ARN with the ARN of the IAM policy that you created in the previous step\.

      ```
      aws iam attach-role-policy --role-name GreengrassV2TokenExchangeRole --policy-arn arn:aws:iam::123456789012:policy/GreengrassV2TokenExchangeRoleAccess
      ```

      The command doesn't have any output if the request succeeds\.

1. <a name="create-token-exchange-role-create-iot-role-alias"></a>Create an AWS IoT role alias that points to the token exchange role\.
   + Replace *GreengrassCoreTokenExchangeRoleAlias* with the name of the role alias to create\.
   + Replace the role ARN with the ARN of the IAM role that you created in the previous step\.

   ```
   aws iot create-role-alias --role-alias GreengrassCoreTokenExchangeRoleAlias --role-arn arn:aws:iam::123456789012:role/GreengrassV2TokenExchangeRole
   ```

   The response looks similar to the following example, if the request succeeds\.

   ```
   {
     "roleAlias": "GreengrassCoreTokenExchangeRoleAlias",
     "roleAliasArn": "arn:aws:iot:us-west-2:123456789012:rolealias/GreengrassCoreTokenExchangeRoleAlias"
   }
   ```
**Note**  
To create a role alias, you must have permission to pass the token exchange IAM role to AWS IoT\. If you receive an error message when you try to create a role alias, check that your AWS user has this permission\. For more information, see [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *AWS Identity and Access Management User Guide*\.

1. Create and attach an AWS IoT policy that allows your Greengrass core device to use the role alias to assume the token exchange role\. If you have set up a Greengrass core device before, you can attach its role alias AWS IoT policy instead of creating a new one\. Do the following:

   1. \(Optional\) Create a file that contains the AWS IoT policy document that the role alias requires\.

      <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

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
            "Resource": "arn:aws:iot:us-west-2:123456789012:rolealias/GreengrassCoreTokenExchangeRoleAlias"
          }
        ]
      }
      ```

   1. Create an AWS IoT policy from the policy document\.
      + Replace *GreengrassCoreTokenExchangeRoleAliasPolicy* with the name of the AWS IoT policy to create\.

      ```
      aws iot create-policy --policy-name GreengrassCoreTokenExchangeRoleAliasPolicy --policy-document file://greengrass-v2-iot-role-alias-policy.json
      ```

      The response looks similar to the following example, if the request succeeds\.

      ```
      {
        "policyName": "GreengrassCoreTokenExchangeRoleAliasPolicy",
        "policyArn": "arn:aws:iot:us-west-2:123456789012:policy/GreengrassCoreTokenExchangeRoleAliasPolicy",
        "policyDocument": "{
          \"Version\":\"2012-10-17\",
          \"Statement\": [
            {
              \"Effect\": \"Allow\",
              \"Action\": \"iot:AssumeRoleWithCertificate\",
              \"Resource\": \"arn:aws:iot:us-west-2:123456789012:rolealias/GreengrassCoreTokenExchangeRoleAlias\"
            }
          ]
        }",
        "policyVersionId": "1"
      }
      ```

   1. Attach the AWS IoT policy to the AWS IoT thing's certificate\.
      + Replace *GreengrassCoreTokenExchangeRoleAliasPolicy* with the name of the role alias AWS IoT policy\.
      + Replace the target ARN with the ARN of the certificate for your AWS IoT thing\.

      ```
      aws iot attach-policy --policy-name GreengrassCoreTokenExchangeRoleAliasPolicy --target arn:aws:iot:us-west-2:123456789012:cert/aa0b7958770878eabe251d8a7ddd547f4889c524c9b574ab9fbf65f32248b1d4
      ```

      The command doesn't have any output if the request succeeds\.

## Download certificates to the device<a name="download-thing-certificates"></a>

Earlier, you downloaded your device's certificate to your development computer\. In this section, you copy the certificate to your core device to set up the device with the certificates that it uses to connect to AWS IoT\. You also download the Amazon root certificate authority \(CA\) certificate\. If you use an HSM, you also import the certificate file into the HSM in this section\.
+ If you created the thing certificate and private key in the AWS IoT service earlier, follow the steps to download the certificates with private key and certificate files\.
+ If you created the thing certificate from a private key in a hardware security module \(HSM\) earlier, follow the steps to download the certificates with the private key and certificate in an HSM\.

### Download certificates with private key and certificate files<a name="download-thing-certificates-without-hardware-security"></a>

**To download certificates to the device**

1. <a name="installation-copy-thing-certificate-to-device"></a>Copy the AWS IoT thing certificate from your development computer to the device\. If SSH and SCP are enabled on the development computer and the device, you can use the `scp` command on your development computer to transfer the certificate\. Replace *device\-ip\-address* with the IP address of your device\.

   ```
   scp -r greengrass-v2-certs/ device-ip-address:~
   ```

1. <a name="installation-create-greengrass-root-folder"></a>Create the Greengrass root folder on the device\. You'll later install the AWS IoT Greengrass Core software to this folder\.

------
#### [ Linux or Unix ]
   + Replace */greengrass/v2* with the folder to use\.

   ```
   sudo mkdir -p /greengrass/v2
   ```

------
#### [ Windows Command Prompt ]
   + Replace *C:\\greengrass\\v2* with the folder to use\.

   ```
   mkdir C:\greengrass\v2
   ```

------
#### [ PowerShell ]
   + Replace *C:\\greengrass\\v2* with the folder to use\.

   ```
   mkdir C:\greengrass\v2
   ```

------

1. <a name="installation-set-greengrass-root-folder-permissions"></a>\(Linux only\) Set the permissions of the parent of the Greengrass root folder\.
   + Replace */greengrass* with the parent of the root folder\.

   ```
   sudo chmod 755 /greengrass
   ```

1. Copy the AWS IoT thing certificates to the Greengrass root folder\.

------
#### [ Linux or Unix ]
   + Replace */greengrass/v2* with the Greengrass root folder\.

   ```
   sudo cp -R ~/greengrass-v2-certs/* /greengrass/v2
   ```

------
#### [ Windows Command Prompt ]
   + Replace *C:\\greengrass\\v2* with the folder to use\.

   ```
   robocopy %USERPROFILE%\greengrass-v2-certs C:\greengrass\v2 /E
   ```

------
#### [ PowerShell ]
   + Replace *C:\\greengrass\\v2* with the folder to use\.

   ```
   cp -Path ~\greengrass-v2-certs\* -Destination C:\greengrass\v2
   ```

------

1. <a name="installation-download-root-ca-certificate"></a>Download the Amazon root certificate authority \(CA\) certificate\. AWS IoT certificates are associated with Amazon's root CA certificate by default\.

------
#### [ Linux or Unix ]

   ```
   sudo curl -o /greengrass/v2/AmazonRootCA1.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   curl -o C:\greengrass\v2\AmazonRootCA1.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
   ```

------
#### [ PowerShell ]

   ```
   iwr -Uri https://www.amazontrust.com/repository/AmazonRootCA1.pem -OutFile C:\greengrass\v2\AmazonRootCA1.pem
   ```

------

### Download certificates with the private key and certificate in an HSM<a name="download-thing-certificates-with-hardware-security"></a>

**Note**  
This feature is available for v2\.5\.3 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 

**To download certificates to the device**

1. <a name="installation-copy-thing-certificate-to-device"></a>Copy the AWS IoT thing certificate from your development computer to the device\. If SSH and SCP are enabled on the development computer and the device, you can use the `scp` command on your development computer to transfer the certificate\. Replace *device\-ip\-address* with the IP address of your device\.

   ```
   scp -r greengrass-v2-certs/ device-ip-address:~
   ```

1. <a name="installation-create-greengrass-root-folder"></a>Create the Greengrass root folder on the device\. You'll later install the AWS IoT Greengrass Core software to this folder\.

------
#### [ Linux or Unix ]
   + Replace */greengrass/v2* with the folder to use\.

   ```
   sudo mkdir -p /greengrass/v2
   ```

------
#### [ Windows Command Prompt ]
   + Replace *C:\\greengrass\\v2* with the folder to use\.

   ```
   mkdir C:\greengrass\v2
   ```

------
#### [ PowerShell ]
   + Replace *C:\\greengrass\\v2* with the folder to use\.

   ```
   mkdir C:\greengrass\v2
   ```

------

1. <a name="installation-set-greengrass-root-folder-permissions"></a>\(Linux only\) Set the permissions of the parent of the Greengrass root folder\.
   + Replace */greengrass* with the parent of the root folder\.

   ```
   sudo chmod 755 /greengrass
   ```

1. Import the thing certificate file, `~/greengrass-v2-certs/device.pem.crt`, into the HSM\. Check the documentation for your HSM to learn how to import certificates into it\. Import the certificate using the same token, slot ID, user PIN, object label, and object ID \(if your HSM uses one\) where you generated the private key in the HSM earlier\.
**Note**  
If you generated the private key earlier without an object ID, and the certificate has an object ID, set the private key's object ID to the same value as the certificate\. Check the documentation for your HSM to learn how to set the object ID for the private key object\.

1. \(Optional\) Delete the thing certificate file, so that it exists only in the HSM\.

   ```
   rm ~/greengrass-v2-certs/device.pem.crt
   ```

1. <a name="installation-download-root-ca-certificate"></a>Download the Amazon root certificate authority \(CA\) certificate\. AWS IoT certificates are associated with Amazon's root CA certificate by default\.

------
#### [ Linux or Unix ]

   ```
   sudo curl -o /greengrass/v2/AmazonRootCA1.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   curl -o C:\greengrass\v2\AmazonRootCA1.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
   ```

------
#### [ PowerShell ]

   ```
   iwr -Uri https://www.amazontrust.com/repository/AmazonRootCA1.pem -OutFile C:\greengrass\v2\AmazonRootCA1.pem
   ```

------

## Set up the device environment<a name="set-up-device-environment"></a>

Follow the steps in this section to set up a Linux or Windows device to use as your AWS IoT Greengrass core device\.

### Set up a Linux device<a name="set-up-linux-device-environment"></a><a name="set-up-linux-device-environment-procedure"></a>

**To set up a Linux device for AWS IoT Greengrass V2**

1. Install the Java runtime, which AWS IoT Greengrass Core software requires to run\. We recommend that you use [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\. The following commands show you how to install OpenJDK on your device\.
   + For Debian\-based or Ubuntu\-based distributions:

     ```
     sudo apt install default-jdk
     ```
   + For Red Hat\-based distributions:

     ```
     sudo yum install java-11-openjdk-devel
     ```
   + For Amazon Linux 2:

     ```
     sudo amazon-linux-extras install java-openjdk11
     ```

   When the installation completes, run the following command to verify that Java runs on your Linux device\.

   ```
   java -version
   ```

   The command prints the version of Java that runs on the device\. For example, on a Debian\-based distribution, the output might look similar to the following sample\.

   ```
   openjdk version "11.0.9.1" 2020-11-04
   OpenJDK Runtime Environment (build 11.0.9.1+1-post-Debian-1deb10u2)
   OpenJDK 64-Bit Server VM (build 11.0.9.1+1-post-Debian-1deb10u2, mixed mode)
   ```

1. \(Optional\) Create the default system user and group that runs components on the device\. You can also choose to let the AWS IoT Greengrass Core software installer create this user and group during installation with the `--component-default-user` installer argument\. For more information, see [Installer arguments](configure-installer.md)\.

   ```
   sudo useradd --system --create-home ggc_user
   sudo groupadd --system ggc_group
   ```

1. Verify that the user that runs the AWS IoT Greengrass Core software \(typically `root`\), has permission to run `sudo` with any user and any group\.

   1. Run the following command to open the `/etc/sudoers` file\.

      ```
      sudo visudo
      ```

   1. Verify that the permission for the user looks like the following example\.

      ```
      root    ALL=(ALL:ALL) ALL
      ```

1. \(Optional\) To [run containerized Lambda functions](run-lambda-functions.md), you must enable [cgroups](https://en.wikipedia.org/wiki/Cgroups) v1, and you must enable and mount the *memory* and *devices* cgroups\. If you don't plan to run containerized Lambda functions, you can skip this step\.

   To enable these cgroups options, boot the device with the following Linux kernel parameters\.

   ```
   cgroup_enable=memory cgroup_memory=1 systemd.unified_cgroup_hierarchy=0
   ```

   For information about viewing and setting kernel parameters for your device, see the documentation for your operating system and boot loader\. Follow the instructions to permanently set the kernel parameters\.
**Tip: Set kernel parameters on a Raspberry Pi**  
If your device is a Raspberry Pi, you can complete the following steps to view and update its Linux kernel parameters:  
Open the `/boot/cmdline.txt` file\. This file specifies Linux kernel parameters to apply when the Raspberry Pi boots\.  
For example, on a Linux\-based system, you can run the following command to use GNU nano to open the file\.  

      ```
      sudo nano /boot/cmdline.txt
      ```
Verify that the `/boot/cmdline.txt` file contains the following kernel parameters\. The `systemd.unified_cgroup_hierarchy=0` parameter specifies to use cgroups v1 instead of cgroups v2\.  

      ```
      cgroup_enable=memory cgroup_memory=1 systemd.unified_cgroup_hierarchy=0
      ```
If the `/boot/cmdline.txt` file doesn't contain these parameters, or it contains these parameters with different values, update the file to contain these parameters and values\.
If you updated the `/boot/cmdline.txt` file, reboot the Raspberry Pi to apply the changes\.  

      ```
      sudo reboot
      ```

1. Install all other required dependencies on your device as indicated by the list of requirements in [Device requirements](setting-up.md#greengrass-v2-requirements)\.

### Set up a Windows device<a name="set-up-windows-device-environment"></a>

**Note**  
This feature is available for v2\.5\.0 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.<a name="set-up-windows-device-environment-procedure"></a>

**To set up a Windows device for AWS IoT Greengrass V2**

1. Install the Java runtime, which AWS IoT Greengrass Core software requires to run\. We recommend that you use [Amazon Corretto 11](http://aws.amazon.com/corretto/) or [OpenJDK 11](https://openjdk.java.net/)\.

1. Check whether Java is available on the [PATH](https://en.wikipedia.org/wiki/PATH_(variable)) system variable, and add it if not\. The LocalSystem account runs the AWS IoT Greengrass Core software, so you must add Java to the PATH system variable instead of the PATH user variable for your user\. Do the following:

   1. Press the Windows key to open the start menu\.

   1. Type **environment variables** to search for the system options from the start menu\.

   1. In the start menu search results, choose **Edit the system environment variables** to open the **System properties** window\.

   1. Choose **Environment variables\.\.\.** to open the **Environment Variables** window\.

   1. Under **System variables**, select **Path**, and then choose **Edit**\. In the **Edit environment variable** window, you can view each path on a separate line\.

   1. Check if the path to the Java installation's `bin` folder is present\. The path might look similar to the following example\.

      ```
      C:\Program Files\Amazon Corretto\jdk11.0.13_8\bin
      ```

   1. If the Java installation's `bin` folder is missing from **Path**, choose **New** to add it, and then choose **OK**\.

1. <a name="set-up-windows-device-environment-open-cmd"></a>Open the Windows Command Prompt \(`cmd.exe`\) as an administrator\.

1. <a name="set-up-windows-device-environment-create"></a>Create the default user in the LocalSystem account on the Windows device\. Replace *password* with a secure password\.

   ```
   net user /add ggc_user password
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

1. <a name="set-up-windows-device-psexec"></a>Download and install the [PsExec utility](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) from Microsoft on the device\. 

1. <a name="set-up-windows-device-credentials"></a>Use the PsExec utility to store the user name and password for the default user in the Credential Manager instance for the LocalSystem account\. Replace *password* with the user's password that you set earlier\.

   ```
   psexec -s cmd /c cmdkey /generic:ggc_user /user:ggc_user /pass:password
   ```

   If the **PsExec License Agreement** opens, choose **Accept** to agree to the license and run the command\.
**Note**  
On Windows devices, the LocalSystem account runs the Greengrass nucleus, and you must use the PsExec utility to store the default user information in the LocalSystem account\. Using the Credential Manager application stores this information in the Windows account of the currently logged on user, instead of the LocalSystem account\.

## Download the AWS IoT Greengrass Core software<a name="download-greengrass-core-v2"></a>

You can download the latest version of the AWS IoT Greengrass Core software from the following location:
+ [https://d2s8p88vqu9w66\.cloudfront\.net/releases/greengrass\-nucleus\-latest\.zip](https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip)

**Note**  
You can download a specific version of the AWS IoT Greengrass Core software from the following location\. Replace *version* with the version to download\.  

```
https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-version.zip
```

**To download the AWS IoT Greengrass Core software**

1. <a name="installation-download-ggc-software-step"></a>On your core device, download the AWS IoT Greengrass Core software to a file named `greengrass-nucleus-latest.zip`\.

------
#### [ Linux or Unix ]

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip
   ```

------
#### [ PowerShell ]

   ```
   iwr -Uri https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip -OutFile greengrass-nucleus-latest.zip
   ```

------

   <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

1. <a name="installation-unzip-ggc-software-step"></a>Unzip the AWS IoT Greengrass Core software to a folder on your device\. Replace *GreengrassInstaller* with the folder that you want to use\.

------
#### [ Linux or Unix ]

   ```
   unzip greengrass-nucleus-latest.zip -d GreengrassInstaller && rm greengrass-nucleus-latest.zip
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   mkdir GreengrassInstaller && tar -xf greengrass-nucleus-latest.zip -C GreengrassInstaller && del greengrass-nucleus-latest.zip
   ```

------
#### [ PowerShell ]

   ```
   Expand-Archive -Path greengrass-nucleus-latest.zip -DestinationPath .\GreengrassInstaller
   rm greengrass-nucleus-latest.zip
   ```

------

1. \(Optional\) Run the following command to see the version of the AWS IoT Greengrass Core software\.

   ```
   java -jar ./GreengrassInstaller/lib/Greengrass.jar --version
   ```

**Important**  <a name="installer-folder-2.4.0-warning"></a>
If you install a version of the Greengrass nucleus earlier than v2\.4\.0, don't remove this folder after you install the AWS IoT Greengrass Core software\. The AWS IoT Greengrass Core software uses the files in this folder to run\.  
If you downloaded the latest version of the software, you install v2\.4\.0 or later, and you can remove this folder after you install the AWS IoT Greengrass Core software\.

**\(Optional\) To verify the Greengrass nucleus software signature**
**Note**  
This feature is available with Greengrass nucleus version 2\.9\.5 and later\.

1. Use the following command to verify your Greengrass nucleus artifact's signature:

------
#### [ Linux or Unix ]

   ```
   jarsigner -verify -certs -verbose greengrass-nucleus-latest.zip
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   The file name might look different depending on the JDK version you install\. Replace *`jdk17.0.6_10`* with the JDK version you installed\.

   ```
   "C:\Program Files\Amazon Corretto\jdk17.0.6_10\bin\jarsigner.exe" -verify -certs -verbose greengrass-nucleus-latest.zip
   ```

------
#### [ PowerShell ]

   The file name might look different depending on the JDK version you install\. Replace *`jdk17.0.6_10`* with the JDK version you installed\.

   ```
   'C:\Program Files\Amazon Corretto\jdk17.0.6_10\bin\jarsigner.exe' -verify -certs -verbose greengrass-nucleus-latest.zip
   ```

------

1. The `jarsigner` invocation yields output that indicates the results of the verification\.

   1. If the Greengrass nucleus zip file is signed, the output contains the following statement:

      ```
      jar verified.
      ```

   1. If the Greengrass nucleus zip file isn't signed, the output contains the following statement:

      ```
      jar is unsigned.
      ```

1. If you provided the Jarsigner `-certs` option along with `-verify` and `-verbose` options, the output also includes detailed signer certificate information\.

## Install the AWS IoT Greengrass Core software<a name="run-greengrass-core-v2-installer-manual"></a>

Run the installer with arguments that specify the following actions:
+ Install from a partial configuration file that specifies to use the AWS resources and certificates that you created earlier\. The AWS IoT Greengrass Core software uses a configuration file that specifies the configuration of every Greengrass component on the device\. The installer creates a complete configuration file from the partial configuration file that you provide\.
+ <a name="install-argument-component-default-user"></a>Specify to use the `ggc_user` system user to run software components on the core device\. On Linux devices, this command also specifies to use the `ggc_group` system group, and the installer creates the system user and group for you\.
+ <a name="install-argument-system-service"></a>Set up the AWS IoT Greengrass Core software as a system service that runs at boot\. On Linux devices, this requires the [Systemd](https://en.wikipedia.org/wiki/Systemd) init system\.
**Important**  <a name="windows-system-service-requirement-important-note"></a>
On Windows core devices, you must set up the AWS IoT Greengrass Core software as a system service\.

For more information about the arguments that you can specify, see [Installer arguments](configure-installer.md)\.

**Note**  
<a name="jvm-tuning-note"></a>If you are running AWS IoT Greengrass on a device with limited memory, you can control the amount of memory that AWS IoT Greengrass Core software uses\. To control memory allocation, you can set JVM heap size options in the `jvmOptions` configuration parameter in your nucleus component\. For more information, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.
+ If you created the thing certificate and private key in the AWS IoT service earlier, follow the steps to install the AWS IoT Greengrass Core software with private key and certificate files\.
+ If you created the thing certificate from a private key in a hardware security module \(HSM\) earlier, follow the steps to install the AWS IoT Greengrass Core software with the private key and certificate in an HSM\.

### Install the AWS IoT Greengrass Core software with private key and certificate files<a name="manual-installation-without-hardware-security"></a>

**To install the AWS IoT Greengrass Core software**

1. <a name="installer-check-greengrass-core-software-version"></a>Check the version of the AWS IoT Greengrass Core software\.
   + Replace *GreengrassInstaller* with the path to the folder that contains the software\.

   ```
   java -jar ./GreengrassInstaller/lib/Greengrass.jar --version
   ```

1. Use a text editor to create a configuration file named `config.yaml` to provide to the installer\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano GreengrassInstaller/config.yaml
   ```

   Copy the following YAML content into the file\. This partial configuration file specifies system parameters and Greengrass nucleus parameters\.

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
       version: "2.9.4"
       configuration:
         awsRegion: "us-west-2"
         iotRoleAlias: "GreengrassCoreTokenExchangeRoleAlias"
         iotDataEndpoint: "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
         iotCredEndpoint: "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
   ```

   Then, do the following:
   + Replace each instance of */greengrass/v2* with the Greengrass root folder\.
   + Replace *MyGreengrassCore* with the name of the AWS IoT thing\.
   + Replace *2\.9\.4* with the version of the AWS IoT Greengrass Core software\.
   + Replace *us\-west\-2* with the AWS Region where you created the resources\.
   + Replace *GreengrassCoreTokenExchangeRoleAlias* with the name of the token exchange role alias\.
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
       version: "2.9.4"
       configuration:
         awsRegion: "us-west-2"
         iotRoleAlias: "GreengrassCoreTokenExchangeRoleAlias"
         iotCredEndpoint: "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
         iotDataEndpoint: "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
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
   + Replace */greengrass/v2* or *C:\\greengrass\\v2* with the Greengrass root folder\.
   + Replace each instance of *GreengrassInstaller* with the folder where you unpacked the installer\.

------
#### [ Linux or Unix ]

   ```
   sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE \
     -jar ./GreengrassInstaller/lib/Greengrass.jar \
     --init-config ./GreengrassInstaller/config.yaml \
     --component-default-user ggc_user:ggc_group \
     --setup-system-service true
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   java -Droot="C:\greengrass\v2" "-Dlog.store=FILE" ^
     -jar ./GreengrassInstaller/lib/Greengrass.jar ^
     --init-config ./GreengrassInstaller/config.yaml ^
     --component-default-user ggc_user ^
     --setup-system-service true
   ```

------
#### [ PowerShell ]

   ```
   java -Droot="C:\greengrass\v2" "-Dlog.store=FILE" `
     -jar ./GreengrassInstaller/lib/Greengrass.jar `
     --init-config ./GreengrassInstaller/config.yaml `
     --component-default-user ggc_user `
     --setup-system-service true
   ```

------
**Important**  <a name="windows-system-service-installer-argument-important-note"></a>
On Windows core devices, you must specify `--setup-system-service true` to set up the AWS IoT Greengrass Core software as a system service\.

   <a name="installer-setup-system-service-output-message"></a>If you specify `--setup-system-service true`, the installer prints `Successfully set up Nucleus as a system service` if it set up and ran the software as a system service\. Otherwise, the installer doesn't output any message if it installs the software successfully\.
**Note**  <a name="installer-deploy-dev-tools-without-provision"></a>
You can't use the `deploy-dev-tools` argument to deploy local development tools when you run the installer without the `--provision true` argument\. For information about deploying the Greengrass CLI directly on your device, see [Greengrass Command Line Interface](gg-cli.md)\.

1. <a name="installer-verify-installation"></a>Verify the installation by viewing the files in the root folder\.

------
#### [ Linux or Unix ]

   ```
   ls /greengrass/v2
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   dir C:\greengrass\v2
   ```

------
#### [ PowerShell ]

   ```
   ls C:\greengrass\v2
   ```

------

   If the installation succeeded, the root folder contains several folders, such as `config`, `packages`, and `logs`\.

### Install the AWS IoT Greengrass Core software with the private key and certificate in an HSM<a name="manual-installation-with-hardware-security"></a>

**Note**  
This feature is available for v2\.5\.3 and later of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. AWS IoT Greengrass doesn't currently support this feature on Windows core devices\. 

**To install the AWS IoT Greengrass Core software**

1. <a name="installer-check-greengrass-core-software-version"></a>Check the version of the AWS IoT Greengrass Core software\.
   + Replace *GreengrassInstaller* with the path to the folder that contains the software\.

   ```
   java -jar ./GreengrassInstaller/lib/Greengrass.jar --version
   ```

1. To enable the AWS IoT Greengrass Core software to use the private key and certificate in the HSM, install the [PKCS\#11 provider component](pkcs11-provider-component.md) when you install the AWS IoT Greengrass Core software\. The PKCS\#11 provider component is a plugin that you can configure during installation\. You can download the latest version of the PKCS\#11 provider component from the following location:
   + [https://d2s8p88vqu9w66\.cloudfront\.net/releases/Pkcs11Provider/aws\.greengrass\.crypto\.Pkcs11Provider\-latest\.jar](https://d2s8p88vqu9w66.cloudfront.net/releases/Pkcs11Provider/aws.greengrass.crypto.Pkcs11Provider-latest.jar)

   Download the PKCS\#11 provider plugin to a file named `aws.greengrass.crypto.Pkcs11Provider.jar`\. Replace *GreengrassInstaller* with the folder that you want to use\.

   ```
   curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/Pkcs11Provider/aws.greengrass.crypto.Pkcs11Provider-latest.jar > GreengrassInstaller/aws.greengrass.crypto.Pkcs11Provider.jar
   ```

   <a name="core-software-license"></a>By downloading this software, you agree to the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

1. Use a text editor to create a configuration file named `config.yaml` to provide to the installer\.

   <a name="nano-command-intro"></a>For example, on a Linux\-based system, you can run the following command to use GNU nano to create the file\.

   ```
   nano GreengrassInstaller/config.yaml
   ```

   Copy the following YAML content into the file\. This partial configuration file specifies system parameters, Greengrass nucleus parameters, and PKCS\#11 provider parameters\.

   ```
   ---
   system:
     certificateFilePath: "pkcs11:object=iotdevicekey;type=cert"
     privateKeyPath: "pkcs11:object=iotdevicekey;type=private"
     rootCaPath: "/greengrass/v2/AmazonRootCA1.pem"
     rootpath: "/greengrass/v2"
     thingName: "MyGreengrassCore"
   services:
     aws.greengrass.Nucleus:
       componentType: "NUCLEUS"
       version: "2.9.4"
       configuration:
         awsRegion: "us-west-2"
         iotRoleAlias: "GreengrassCoreTokenExchangeRoleAlias"
         iotDataEndpoint: "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
         iotCredEndpoint: "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
     aws.greengrass.crypto.Pkcs11Provider:
       configuration:
         name: "softhsm_pkcs11"
         library: "/usr/local/Cellar/softhsm/2.6.1/lib/softhsm/libsofthsm2.so"
         slot: 1
         userPin: "1234"
   ```

   Then, do the following:
   + Replace each instance of *iotdevicekey* in the PKCS\#11 URIs with the object label where you created the private key and imported the certificate\.
   + Replace each instance of */greengrass/v2* with the Greengrass root folder\.
   + Replace *MyGreengrassCore* with the name of the AWS IoT thing\.
   + Replace *2\.9\.4* with the version of the AWS IoT Greengrass Core software\.
   + Replace *us\-west\-2* with the AWS Region where you created the resources\.
   + Replace *GreengrassCoreTokenExchangeRoleAlias* with the name of the token exchange role alias\.
   + Replace the `iotDataEndpoint` with your AWS IoT data endpoint\.
   + Replace the `iotCredEndpoint` with your AWS IoT credentials endpoint\.
   + Replace the configuration parameters for the `aws.greengrass.crypto.Pkcs11Provider` component with the values for the HSM configuration on the core device\.
**Note**  
In this configuration file, you can customize other nucleus configuration options such as the ports and network proxy to use, as shown in the following example\. For more information, see [Greengrass nucleus configuration](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration)\.  

   ```
   ---
   system:
     certificateFilePath: "pkcs11:object=iotdevicekey;type=cert"
     privateKeyPath: "pkcs11:object=iotdevicekey;type=private"
     rootCaPath: "/greengrass/v2/AmazonRootCA1.pem"
     rootpath: "/greengrass/v2"
     thingName: "MyGreengrassCore"
   services:
     aws.greengrass.Nucleus:
       componentType: "NUCLEUS"
       version: "2.9.4"
       configuration:
         awsRegion: "us-west-2"
         iotRoleAlias: "GreengrassCoreTokenExchangeRoleAlias"
         iotDataEndpoint: "device-data-prefix-ats.iot.us-west-2.amazonaws.com"
         iotCredEndpoint: "device-credentials-prefix.credentials.iot.us-west-2.amazonaws.com"
         mqtt:
           port: 443
         greengrassDataPlanePort: 443
         networkProxy:
           noProxyAddresses: "http://192.168.0.1,www.example.com"
           proxy:
             url: "https://my-proxy-server:1100"
             username: "Mary_Major"
             password: "pass@word1357"
     aws.greengrass.crypto.Pkcs11Provider:
       configuration:
         name: "softhsm_pkcs11"
         library: "/usr/local/Cellar/softhsm/2.6.1/lib/softhsm/libsofthsm2.so"
         slot: 1
         userPin: "1234"
   ```

1. Run the installer, and specify `--init-config` to provide the configuration file\.
   + Replace */greengrass/v2* with the Greengrass root folder\.
   + Replace each instance of *GreengrassInstaller* with the folder where you unpacked the installer\.

   ```
   sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE \
     -jar ./GreengrassInstaller/lib/Greengrass.jar \
     --trusted-plugin ./GreengrassInstaller/aws.greengrass.crypto.Pkcs11Provider.jar \
     --init-config ./GreengrassInstaller/config.yaml \
     --component-default-user ggc_user:ggc_group \
     --setup-system-service true
   ```
**Important**  <a name="windows-system-service-installer-argument-important-note"></a>
On Windows core devices, you must specify `--setup-system-service true` to set up the AWS IoT Greengrass Core software as a system service\.

   <a name="installer-setup-system-service-output-message"></a>If you specify `--setup-system-service true`, the installer prints `Successfully set up Nucleus as a system service` if it set up and ran the software as a system service\. Otherwise, the installer doesn't output any message if it installs the software successfully\.
**Note**  <a name="installer-deploy-dev-tools-without-provision"></a>
You can't use the `deploy-dev-tools` argument to deploy local development tools when you run the installer without the `--provision true` argument\. For information about deploying the Greengrass CLI directly on your device, see [Greengrass Command Line Interface](gg-cli.md)\.

1. <a name="installer-verify-installation"></a>Verify the installation by viewing the files in the root folder\.

------
#### [ Linux or Unix ]

   ```
   ls /greengrass/v2
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   dir C:\greengrass\v2
   ```

------
#### [ PowerShell ]

   ```
   ls C:\greengrass\v2
   ```

------

   If the installation succeeded, the root folder contains several folders, such as `config`, `packages`, and `logs`\.

<a name="install-greengrass-core-run-software"></a>If you installed the AWS IoT Greengrass Core software as a system service, the installer runs the software for you\. Otherwise, you must run the software manually\. For more information, see [Run the AWS IoT Greengrass Core software](run-greengrass-core-v2.md)\.

<a name="install-greengrass-core-next-steps-intro"></a>For more information about how to configure and use the software and AWS IoT Greengrass, see the following:<a name="install-greengrass-core-next-steps-links"></a>
+ [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)
+ [Develop AWS IoT Greengrass components](develop-greengrass-components.md)
+ [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)
+ [Greengrass Command Line Interface](gg-cli.md)