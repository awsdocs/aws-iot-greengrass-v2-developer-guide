# IoT SiteWise processor<a name="iotsitewise-processor-component"></a>

The IoT SiteWise processor component \(`aws.iot.SiteWiseEdgeProcessor`\) enables AWS IoT SiteWise gateways to process data at the edge\.

With this component, AWS IoT SiteWise gateways can use asset models and assets to process data on gateway devices\. For more information about AWS IoT SiteWise gateways, see [Using AWS IoT SiteWise at the edge](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/gateways-ggv2.html) in the *AWS IoT SiteWise User Guide*\.

**Topics**
+ [Versions](#iotsitewise-processor-component-versions)
+ [Type](#iotsitewise-processor-component-type)
+ [Operating system](#iotsitewise-processor-component-os-support)
+ [Requirements](#iotsitewise-processor-component-requirements)
+ [Dependencies](#iotsitewise-processor-component-dependencies)
+ [Configuration](#iotsitewise-processor-component-configuration)
+ [Local log file](#iotsitewise-processor-component-log-file)
+ [Licenses](#iotsitewise-processor-component-licenses)
+ [Changelog](#iotsitewise-processor-component-changelog)
+ [See also](#iotsitewise-processor-component-see-also)

## Versions<a name="iotsitewise-processor-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="iotsitewise-processor-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="iotsitewise-processor-component-os-support"></a>

This component can be installed on Linux core devices only\.

## Requirements<a name="iotsitewise-processor-component-requirements"></a>

This component has the following requirements:
+ The Greengrass core device must run on one of the following platforms:
  + OS: Ubuntu 20\.04 or 18\.04

    Architecture: x86\_64 \(AMD64\)
  + OS: Red Hat Enterprise Linux \(RHEL\) 8

    Architecture: x86\_64 \(AMD64\)
  + OS: Amazon Linux 2

    Architecture: x86\_64 \(AMD64\)
+ The Greengrass core device must allow inbound traffic on port 443\.
+ The Greengrass core device must allow outbound traffic on port 443 and 8883\.
+ The following ports are reserved for use by AWS IoT SiteWise: 80, 443, 3001, 8000, 8081, 8082, 8084, 8085, 8445, 8086, 9000, 9500, and 11080\. Using a reserved port for traffic can result in a terminated connection\.
+ The [Greengrass device role](https://docs.aws.amazon.com/greengrass/v2/developerguide/device-service-role.html) must have permissions that allow you to use AWS IoT SiteWise gateways on your AWS IoT Greengrass V2 devices\. For more information, see [Requirements](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/configure-gateway-ggv2.html#gateway-requirements) in the *AWS IoT SiteWise User Guide*\.

### Endpoints and ports<a name="iotsitewise-processor-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `model.iotsitewise.region.amazonaws.com`  | 443 | Yes |  Get information about your AWS IoT SiteWise assets and asset models\.  | 
|  `edge.iotsitewise.region.amazonaws.com`  | 443 | Yes |  Get information about the core device's AWS IoT SiteWise gateway configuration\.  | 
|  `ecr.region.amazonaws.com`  | 443 | Yes |  Download AWS IoT SiteWise Edge gateway Docker images from Amazon Elastic Container Registry\.  | 
|  `iot.region.amazonaws.com`  | 443 | Yes |  Get device endpoints for your AWS account\.  | 
|  `sts.region.amazonaws.com`  | 443 | Yes |  Get the ID of your AWS account\.  | 
|  `monitor.iotsitewise.region.amazonaws.com`  | 443 | No |  Required if you access AWS IoT SiteWise Monitor portals on the core device\.  | 

## Dependencies<a name="iotsitewise-processor-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#iotsitewise-processor-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for versions 2\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Token exchange service](token-exchange-service-component.md) | >=2\.0\.3 <3\.0\.0 | Hard | 
| [Stream manager](stream-manager-component.md) | >=2\.0\.10 <3\.0\.0 | Hard | 
| [Greengrass CLI](greengrass-cli-component.md) | >=2\.3\.0 <3\.0\.0 | Hard | 

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="iotsitewise-processor-component-configuration"></a>

This component doesn't have any configuration parameters\.

## Local log file<a name="iotsitewise-processor-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/aws.iot.SiteWiseEdgeProcessor.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.iot.SiteWiseEdgeProcessor.log
  ```

## Licenses<a name="iotsitewise-processor-component-licenses"></a>

This component includes the following third\-party software/licensing:

### <a name="w875aac11c69c23b5b1b1"></a>
+ Apache\-2\.0
+ MIT
+ BSD\-2\-Clause
+ BSD\-3\-Clause
+ CDDL\-1\.0
+ CDDL\-1\.1
+ ISC
+ Zlib
+ GPL\-3\.0\-with\-GCC\-exception
+ Public Domain
+ Python\-2\.0
+ Unicode\-DFS\-2015
+ BSD\-1\-Clause
+ OpenSSL
+ EPL\-1\.0
+ EPL\-2\.0
+ GPL\-2\.0\-with\-classpath\-exception
+ MPL\-2\.0
+ CC0\-1\.0
+ JSON

<a name="component-core-software-license"></a>This component is released under the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

## Changelog<a name="iotsitewise-processor-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.2  |  Initial version\.  | 

## See also<a name="iotsitewise-processor-component-see-also"></a>
+ [What is AWS IoT SiteWise?](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/what-is-sitewise.html) in the *AWS IoT SiteWise User Guide*\.