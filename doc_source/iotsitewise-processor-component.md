# IoT SiteWise processor<a name="iotsitewise-processor-component"></a>

The IoT SiteWise processor component \(`aws.iot.SiteWiseEdgeProcessor`\) enables AWS IoT SiteWise gateways to process data at the edge\.

With this component, AWS IoT SiteWise gateways can use asset models and assets to process data on gateway devices\. For more information about AWS IoT SiteWise gateways, see [Using AWS IoT SiteWise at the edge](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/gateways-ggv2.html) in the *AWS IoT SiteWise User Guide*\.

**Topics**
+ [Versions](#iotsitewise-processor-component-versions)
+ [Type](#iotsitewise-processor-component-type)
+ [Requirements](#iotsitewise-processor-component-requirements)
+ [Dependencies](#iotsitewise-processor-component-dependencies)
+ [Configuration](#iotsitewise-processor-component-configuration)
+ [Local log file](#iotsitewise-processor-component-log-file)
+ [Licenses](#iotsitewise-processor-component-licenses)
+ [Changelog](#iotsitewise-processor-component-changelog)
+ [See also](#iotsitewise-processor-component-see-also)

## Versions<a name="iotsitewise-processor-component-versions"></a>

This component has the following versions:
+ 2\.0\.2

## Type<a name="iotsitewise-processor-component-type"></a>

<a name="public-component-type-generic"></a>This component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](manage-components.md#component-types)\.

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
+ The [Greengrass device role](https://docs.aws.amazon.com/greengrass/v2/developerguide/device-service-role.html) must use the `AWSIoTSiteWiseEdgeAccess` policy\. For more information, see [AWS managed policy: AWSIoTSiteWiseEdgeAccess](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/security-iam-awsmanpol.html#security-iam-awsmanpol-AWSIoTSiteWiseEdgeAccess) in the *AWS IoT SiteWise User Guide*\.

## Dependencies<a name="iotsitewise-processor-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#iotsitewise-processor-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for version 2\.0\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.x\.0 <2\.x\.0 | Hard \| Soft | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0 | Hard | 
| [Stream manager](stream-manager-component.md) | ^2\.0\.0 | Hard | 
| [Greengrass CLI](greengrass-cli-component.md) | ^2\.0\.0 | Hard | 

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

### <a name="w591aac14c23c61c21b5b1b1"></a>
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