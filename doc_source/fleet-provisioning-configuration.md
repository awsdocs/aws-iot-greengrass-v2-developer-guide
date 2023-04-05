# Configure the AWS IoT fleet provisioning plugin<a name="fleet-provisioning-configuration"></a>

The AWS IoT fleet provisioning plugin provides the following configuration parameters that you can customize when you [install the AWS IoT Greengrass Core software with fleet provisioning](fleet-provisioning.md)\.

`rootPath`  
The path to the folder to use as the root for the AWS IoT Greengrass Core software\.

`awsRegion`  
The AWS Region that the fleet provisioning plugin uses to provision AWS resources\.

`iotDataEndpoint`  
<a name="nucleus-component-configuration-iot-data-endpoint"></a>The AWS IoT data endpoint for your AWS account\.

`iotCredentialEndpoint`  
<a name="nucleus-component-configuration-iot-cred-endpoint"></a>The AWS IoT credentials endpoint for your AWS account\.

`iotRoleAlias`  
<a name="nucleus-component-configuration-iot-role-alias"></a>The AWS IoT role alias that points to a token exchange IAM role\. The AWS IoT credentials provider assumes this role to allow the Greengrass core device to interact with AWS services\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

`provisioningTemplate`  
The AWS IoT fleet provisioning template to use to provision AWS resources\. This template must specify the following:  <a name="installation-fleet-provisioning-template-requirements"></a>
+ An AWS IoT thing resource\. You can specify a list of existing thing groups to deploy components to each device when it comes online\.
+ An AWS IoT policy resource\. This resource can define one of the following properties:
  + The name of an existing AWS IoT policy\. If you choose this option, the core devices that you create from this template use the same AWS IoT policy, and you can manage their permissions as a fleet\.
  + An AWS IoT policy document\. If you choose this option, each core device that you create from this template uses a unique AWS IoT policy, and you can manage permissions for each individual core device\.
+ An AWS IoT certificate resource\. This certificate resource must use the `AWS::IoT::Certificate::Id` parameter to attach the certificate to the core device\. For more information, see [Just\-in\-time provisioning](https://docs.aws.amazon.com/iot/latest/developerguide/jit-provisioning.html) in the *AWS IoT Developer Guide*\.
For more information, see [Provisioning templates](https://docs.aws.amazon.com/iot/latest/developerguide/provision-template.html) in the *AWS IoT Core Developer Guide*\.

`claimCertificatePath`  
The path to the provisioning claim certificate for the provisioning template that you specify in `provisioningTemplate`\. For more information, see [CreateProvisioningClaim](https://docs.aws.amazon.com/iot/latest/apireference/API_CreateProvisioningClaim.html) in the *AWS IoT Core API Reference*\.

`claimCertificatePrivateKeyPath`  
The path to the provisioning claim certificate private key for the provisioning template that you specify in `provisioningTemplate`\. For more information, see [CreateProvisioningClaim](https://docs.aws.amazon.com/iot/latest/apireference/API_CreateProvisioningClaim.html) in the *AWS IoT Core API Reference*\.  
Provisioning claim private keys should be secured at all times, including on Greengrass core devices\. We recommend that you use Amazon CloudWatch metrics and logs to monitor for indications of misuse, such as unauthorized use of the claim certificate to provision devices\. If you detect misuse, disable the provisioning claim certificate so that it can't be used for device provisioning\. For more information, see [Monitoring AWS IoT](https://docs.aws.amazon.com/iot/latest/developerguide/monitoring_overview.html) in the *AWS IoT Core Developer Guide*\.  
To help you better manage the number of devices, and which devices, that register themselves in your AWS account, you can specify a pre\-provisioning hook when you create a fleet provisioning template\. A pre\-provisioning hook is an AWS Lambda function that validates template parameters that devices provide during registration\. For example, you might create a pre\-provisioning hook that checks a device ID against a database to verify that the device has permission to provision\. For more information, see [Pre\-provisioning hooks](https://docs.aws.amazon.com/iot/latest/developerguide/pre-provisioning-hook.html) in the *AWS IoT Core Developer Guide*\.

`rootCaPath`  
The path to the Amazon root certificate authority \(CA\) certificate\.

`templateParameters`  
\(Optional\) The map of parameters to provide to the fleet provisioning template\. For more information, see [Provisioning templates' parameters section](https://docs.aws.amazon.com/iot/latest/developerguide/provision-template.html#parameters-section) in the *AWS IoT Core Developer Guide*\.

`deviceId`  
\(Optional\) The device identifier to use as the client ID when the fleet provisioning plugin creates an MQTT connection to AWS IoT\.  
Default: A random UUID\.

`mqttPort`  
\(Optional\) The port to use for MQTT connections\.  
Default: `8883`

`proxyUrl`  
\(Optional\) The URL of the proxy server in the format `scheme://userinfo@host:port`\. To use an HTTPS proxy, you must use version 1\.1\.0 or later of the fleet provisioning plugin\.  <a name="nucleus-component-configuration-proxy-url-segments"></a>
+ `scheme` – The scheme, which must be `http` or `https`\.
**Important**  
<a name="https-proxy-greengrass-nucleus-requirement"></a>Greengrass core devices must run [Greengrass nucleus ](greengrass-nucleus-component.md) v2\.5\.0 or later to use HTTPS proxies\.  
If you configure an HTTPS proxy, you must add the proxy server CA certificate to the core device's Amazon root CA certificate\. For more information, see [Enable the core device to trust an HTTPS proxy](configure-greengrass-core-v2.md#https-proxy-certificate-trust)\.
+ `userinfo` – \(Optional\) The user name and password information\. If you specify this information in the `url`, the Greengrass core device ignores the `username` and `password` fields\.
+ `host` – The host name or IP address of the proxy server\.
+ `port` – \(Optional\) The port number\. If you don't specify the port, then the Greengrass core device uses the following default values:
  + `http` – 80
  + `https` – 443

`proxyUserName`  
\(Optional\) The user name that authenticates the proxy server\.

`proxyPassword`  
\(Optional\) The user name that authenticates the proxy server\.

csrPath  
\(Optional\) The path to the certificate signing request \(CSR\) file to use to create the device certificate from a CSR\. For more information, see [Provisioning by claim](https://docs.aws.amazon.com/iot/latest/developerguide/provision-wo-cert.html#claim-based) in the * AWS IoT Core developer guide*\.

csrPrivateKeyPath  
\(Optional, required if `csrPath` is declared\) The path to the private key used to generate the CSR\. The private key must have been used to generate the CSR\. For more information, see [Provisioning by claim](https://docs.aws.amazon.com/iot/latest/developerguide/provision-wo-cert.html#claim-based) in the *AWS IoT Core developer guide*\.