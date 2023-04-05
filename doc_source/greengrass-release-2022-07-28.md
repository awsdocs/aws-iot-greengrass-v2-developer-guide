# Release: AWS IoT Greengrass Core v2\.7\.0 software update on July 28, 2022<a name="greengrass-release-2022-07-28"></a>

This release provides version 2\.7\.0 of the Greengrass nucleus component, version 2\.1\.0 of the stream manager component, and version 2\.2\.5 of the Lambda manager component\.

**Release date:** July 28, 2022

**Release highlights**
+ **Stream manager telemetry metrics** – Stream manager now automatically sends telemetry metrics to Amazon EventBridge, so you can create cloud applications that monitor and analyze the volume of data that your core devices upload\. For more information, see [Gather system health telemetry data from AWS IoT Greengrass core devices](telemetry.md)\.
+ **Custom certificate authority \(CA\)** – Client certificates signed by a custom certificate CA, where the CA isn't registered with AWS IoT, are now supported\. For more information, see [Use a device certificate signed by a private CA](configure-greengrass-core-v2.md#configure-nucleus-private-ca)\.

**Topics**
+ [Public component updates](#greengrass-2022-07-28-components)

## Public component updates<a name="greengrass-2022-07-28-components"></a>

The following table lists AWS\-provided components that include new and updated features\.

**Important**  <a name="component-patch-update-note"></a>
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all of that component's dependencies\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.


| **Component** | **Details** | 
| --- | --- | 
| Greengrass nucleus |  Version 2\.7\.0 of the [Greengrass nucleus](greengrass-nucleus-component.md) is available\. <a name="changelog-nucleus-2.7.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-07-28.html)  | 
| Stream manager |  Version 2\.1\.0 of the [stream manager](stream-manager-component.md) component is available\. <a name="changelog-stream-manager-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-07-28.html)  | 
| Lambda manager |  Version 2\.2\.5 of the [Lambda manager](lambda-manager-component.md) component is available\. <a name="changelog-lambda-manager-2.2.5"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-release-2022-07-28.html)  | 