# IoT SiteWise publisher<a name="iotsitewise-publisher-component"></a>

The IoT SiteWise publisher component \(`aws.iot.SiteWiseEdgePublisher`\) enables AWS IoT SiteWise gateways to export data from the edge to the AWS Cloud\.

For more information about AWS IoT SiteWise gateways, see [Using AWS IoT SiteWise at the edge](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/gateways-ggv2.html) in the *AWS IoT SiteWise User Guide*\.

**Topics**
+ [Versions](#iotsitewise-publisher-component-versions)
+ [Type](#iotsitewise-publisher-component-type)
+ [Operating system](#iotsitewise-publisher-component-os-support)
+ [Requirements](#iotsitewise-publisher-component-requirements)
+ [Dependencies](#iotsitewise-publisher-component-dependencies)
+ [Configuration](#iotsitewise-publisher-component-configuration)
+ [Input data](#iotsitewise-publisher-component-input-data)
+ [Local log file](#iotsitewise-publisher-component-log-file)
+ [Licenses](#iotsitewise-publisher-component-licenses)
+ [Changelog](#iotsitewise-publisher-component-changelog)
+ [See also](#iotsitewise-publisher-component-see-also)

## Versions<a name="iotsitewise-publisher-component-versions"></a>

This component has the following versions:
+ 2\.1\.x
+ 2\.0\.x

## Type<a name="iotsitewise-publisher-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="iotsitewise-publisher-component-os-support"></a>

This component can be installed on Linux core devices only\.

## Requirements<a name="iotsitewise-publisher-component-requirements"></a>

This component has the following requirements:
+ The Greengrass core device must run on one of the following platforms:
  + OS: Ubuntu 20\.04 or 18\.04

    Architecture: x86\_64 \(AMD64\)
  + OS: Red Hat Enterprise Linux \(RHEL\) 8

    Architecture: x86\_64 \(AMD64\)
  + OS: Amazon Linux 2

    Architecture: x86\_64 \(AMD64\)
+ The Greengrass core device must connect to the Internet\.
+ The Greengrass core device must be authorized to perform the `iotsitewise:BatchPutAssetPropertyValue` action\. For more information, see [Authorize core devices to interact with AWS services](https://docs.aws.amazon.com/greengrass/v2/developerguide/device-service-role.html)\.  
**Example permissions policy**  

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": "iotsitewise:BatchPutAssetPropertyValue",
              "Resource": "*"
          }
      ]
  }
  ```

### Endpoints and ports<a name="iotsitewise-publisher-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `data.iotsitewise.region.amazonaws.com`  | 443 | Yes |  Publish data to AWS IoT SiteWise\.  | 

## Dependencies<a name="iotsitewise-publisher-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#iotsitewise-publisher-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for versions 2\.0\.x to 2\.1\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.3\.0<3\.0\.0 | Hard | 
| [Stream manager](stream-manager-component.md) | >=2\.0\.10<3\.0\.0 | Hard | 

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="iotsitewise-publisher-component-configuration"></a>

This component doesn't have any configuration parameters\.

You can use the AWS IoT SiteWise console or API to configure the IoT SiteWise publisher component\. For more information, see [Step 3: Configure publisher \- optional](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/create-gateway-ggv2.html#configure-publisher) in the *AWS IoT SiteWise User Guide*\.

## Input data<a name="iotsitewise-publisher-component-input-data"></a>



This component reads `BatchPutAssetPropertyValue` messages from AWS IoT Greengrass stream manager\. For more information, see [BatchPutAssetPropertyValue](https://docs.aws.amazon.com/iot-sitewise/latest/APIReference/API_BatchPutAssetPropertyValue.html) in the *AWS IoT SiteWise API Reference*\.

## Local log file<a name="iotsitewise-publisher-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/aws.iot.SiteWiseEdgePublisher.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.iot.SiteWiseEdgePublisher.log
  ```

## Licenses<a name="iotsitewise-publisher-component-licenses"></a>

<a name="component-core-software-license"></a>This component is released under the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

## Changelog<a name="iotsitewise-publisher-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.1\.4  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/iotsitewise-publisher-component.html)  | 
|  2\.1\.3  |   This version is no longer available, except in the US East \(Ohio\), Canada \(Central\), and AWS GovCloud \(US\-East\) Regions\. This component version requires Java version 11 or greater to run\. The improvements in this version are available in later versions of this component\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/iotsitewise-publisher-component.html)  | 
|  2\.1\.2  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/iotsitewise-publisher-component.html)  | 
|  2\.1\.1  |  Bug fixes and improvements\.  | 
|  2\.1\.0  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/iotsitewise-publisher-component.html)  | 
|  2\.0\.2  |  Bug fixes and improvements\.  | 
|  2\.0\.1  |  Initial version\.  | 

## See also<a name="iotsitewise-publisher-component-see-also"></a>
+ [What is AWS IoT SiteWise?](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/what-is-sitewise.html) in the *AWS IoT SiteWise User Guide*\.