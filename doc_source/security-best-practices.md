# Security best practices for AWS IoT Greengrass<a name="security-best-practices"></a>

This topic contains security best practices for AWS IoT Greengrass\.

## Grant minimum possible permissions<a name="least-privilege"></a>

Follow the principle of least privilege by using the minimum set of permissions in IAM roles\. Limit the use of the `*` wildcard for the `Action` and `Resource` properties in your IAM policies\. Instead, declare a finite set of actions and resources when possible\. For more information about least privilege and other policy best practices, see [Policy best practices](security_iam_id-based-policy-examples.md#security_iam_service-with-iam-policy-best-practices)\.

The least privilege best practice also applies to AWS IoT policies you attach to your Greengrass core\.

## Don't hardcode credentials in Greengrass components<a name="no-hardcoded-credentials"></a>

Don't hardcode credentials in your user\-defined Greengrass components\. To better protect your credentials:
+ To interact with AWS services, define permissions for specific actions and resources in the [Greengrass core device service role](device-service-role.md)\.
+ Use the [secret manager component](secret-manager-component.md) to store your credentials\. Or, if the function uses the AWS SDK, use credentials from the default credential provider chain\.

## Don't log sensitive information<a name="protect-pii"></a>

You should prevent the logging of credentials and other personally identifiable information \(PII\)\. We recommend that you implement the following safeguards even though access to local logs on a core device requires root privileges and access to CloudWatch Logs requires IAM permissions\.
+ Don't use sensitive information in MQTT topic paths\.
+ Don't use sensitive information in device \(thing\) names, types, and attributes in the AWS IoT Core registry\.
+ Don't log sensitive information in your user\-defined Greengrass components or Lambda functions\.
+ Don't use sensitive information in the names and IDs of Greengrass resources:
  + Core devices
  + Components
  + Deployments
  + Loggers

## Keep your device clock in sync<a name="device-clock"></a>

It's important to have an accurate time on your device\. X\.509 certificates have an expiry date and time\. The clock on your device is used to verify that a server certificate is still valid\. Device clocks can drift over time or batteries can get discharged\.

For more information, see the [ Keep your device's clock in sync](https://docs.aws.amazon.com/iot/latest/developerguide/security-best-practices.html#device-clock) best practice in the *AWS IoT Core Developer Guide*\.

## See also<a name="security-best-practices-see-also"></a>
+ [Security best practices in AWS IoT Core](https://docs.aws.amazon.com/iot/latest/developerguide/security-best-practices.html) in the *AWS IoT Developer Guide*
+ [ Ten security golden rules for IoT solutions](https://aws.amazon.com/blogs/iot/ten-security-golden-rules-for-iot-solutions/) on the *Internet of Things on AWS Official Blog*