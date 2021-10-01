# IoT SiteWise OPC\-UA collector<a name="iotsitewise-opcua-collector-component"></a>

The IoT SiteWise OPC\-UA collector component \(`aws.iot.SiteWiseEdgeCollectorOpcua`\) enables AWS IoT SiteWise gateways to collect data from local OPC\-UA servers\.

With this component, AWS IoT SiteWise gateways can connect to multiple OPC\-UA servers\. For more information about AWS IoT SiteWise gateways, see [Using AWS IoT SiteWise at the edge](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/gateways-ggv2.html) in the *AWS IoT SiteWise User Guide*\.

**Topics**
+ [Versions](#iotsitewise-opcua-collector-component-versions)
+ [Type](#iotsitewise-opcua-collector-component-type)
+ [Requirements](#iotsitewise-opcua-collector-component-requirements)
+ [Dependencies](#iotsitewise-opcua-collector-component-dependencies)
+ [Configuration](#iotsitewise-opcua-collector-component-configuration)
+ [Output data](#iotsitewise-opcua-collector-component-output-data)
+ [Local log file](#iotsitewise-opcua-collector-component-log-file)
+ [Licenses](#iotsitewise-opcua-collector-component-licenses)
+ [Changelog](#iotsitewise-opcua-collector-component-changelog)
+ [See also](#iotsitewise-opcua-collector-component-see-also)

## Versions<a name="iotsitewise-opcua-collector-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="iotsitewise-opcua-collector-component-type"></a>

<a name="public-component-type-generic"></a>This component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Requirements<a name="iotsitewise-opcua-collector-component-requirements"></a>

This component has the following requirements:
+ The Greengrass core device must allow outbound network connectivity to OPC\-UA servers\.

## Dependencies<a name="iotsitewise-opcua-collector-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#iotsitewise-opcua-collector-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for version 2\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.3\.0 <3\.0\.0 | Hard | 
| [Stream manager](stream-manager-component.md) | >2\.0\.10<3\.0\.0 | Hard | 
| [Secret manager](secret-manager-component.md) | >=2\.0\.8 <3\.0\.0 | Hard | 

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="iotsitewise-opcua-collector-component-configuration"></a>

This component doesn't have any configuration parameters\.

## Output data<a name="iotsitewise-opcua-collector-component-output-data"></a>



This component writes `BatchPutAssetPropertyValue` messages to AWS IoT Greengrass stream manager\. For more information, see [BatchPutAssetPropertyValue](https://docs.aws.amazon.com/iot-sitewise/latest/APIReference/API_BatchPutAssetPropertyValue.html) in the *AWS IoT SiteWise API Reference*\.

## Local log file<a name="iotsitewise-opcua-collector-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/aws.iot.SiteWiseEdgeCollectorOpcua.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.iot.SiteWiseEdgeCollectorOpcua.log
  ```

## Licenses<a name="iotsitewise-opcua-collector-component-licenses"></a>

<a name="component-core-software-license"></a>This component is released under the [Greengrass Core Software License Agreement](https://greengrass-release-license.s3.us-west-2.amazonaws.com/greengrass-license-v1.pdf)\.

## Changelog<a name="iotsitewise-opcua-collector-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.1  |  Initial version\.  | 

## See also<a name="iotsitewise-opcua-collector-component-see-also"></a>
+ [What is AWS IoT SiteWise?](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/what-is-sitewise.html) in the *AWS IoT SiteWise User Guide*\.