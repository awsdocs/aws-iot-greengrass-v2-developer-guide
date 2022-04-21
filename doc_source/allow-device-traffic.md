# Allow device traffic through a proxy or firewall<a name="allow-device-traffic"></a>

Greengrass core devices and Greengrass components perform outbound requests to AWS services and other websites\. As a security measure, you might limit outbound traffic to a small range of endpoints and ports\. You can use the following information about endpoints and ports to limit device traffic through a proxy, firewall, or [Amazon VPC security group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)\. For more information about how to configure a core device to use a proxy, see [Connect on port 443 or through a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy)\.

**Topics**
+ [Endpoints for basic operation](#core-endpoints)
+ [Endpoints for installation with automatic provisioning](#automatic-provisioning-endpoints)
+ [Endpoints for AWS\-provided components](#public-component-endpoints)

## Endpoints for basic operation<a name="core-endpoints"></a>

Greengrass core devices use the following endpoints and ports for basic operation\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `greengrass-ats.iot.region.amazonaws.com`   | 8443 or 443 | Yes |  Used for data plane operations, such as installing deployments and working with client devices\.   | 
|  `prefix-ats.iot.region.amazonaws.com`   |  MQTT: 8883 or 443 HTTPS: 8443 or 443  | Yes |  Used for data plane operations for device management, such as MQTT communication and shadow sync with AWS IoT Core\.   | 
|  `prefix.credentials.iot.region.amazonaws.com`  | 443 | Yes |  Used to acquire AWS credentials, which the core device uses to download component artifacts from Amazon S3 and perform other operations\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.  | 
|  `*.s3.amazonaws.com`  | 443 | Yes |  Used for deployments\. This format includes the `*` character, because endpoint prefixes are controlled internally and might change at any time\.  | 
|  `data.iot.region.amazonaws.com`  | 443 | No |  Required if the core device runs a version of the [Greengrass nucleus](greengrass-nucleus-component.md) earlier than v2\.4\.0 and is configured to use a network proxy\. The core device uses this endpoint for MQTT communication with AWS IoT Core when behind a proxy\. For more information, see [Configure a network proxy](configure-greengrass-core-v2.md#configure-network-proxy)\.  | 

## Endpoints for installation with automatic provisioning<a name="automatic-provisioning-endpoints"></a>

Greengrass core devices use the following endpoints and ports when you [install the AWS IoT Greengrass Core software with automatic resource provisioning](quick-installation.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `iot.region.amazonaws.com`  | 443 | Yes |  Used to create AWS IoT resources and retrieve information about existing AWS IoT resources\.  | 
|  `iam.amazonaws.com`  | 443 | Yes |  Used to create IAM resources and retrieve information about existing IAM resources\.  | 
|  `sts.region.amazonaws.com`  | 443 | Yes |  Used to get the ID of your AWS account\.  | 
|  `greengrass.region.amazonaws.com`  | 443 | No |  Required if you use the `--deploy-dev-tools` argument to deploy the Greengrass CLI component to the core device\.  | 

## Endpoints for AWS\-provided components<a name="public-component-endpoints"></a>

Greengrass core devices use additional endpoints depending on which software components they run\. You can find the endpoints that each AWS\-provided component requires in the **Requirements** section on each component's page in this developer guide\. For more information, see [AWS\-provided components](public-components.md)\.