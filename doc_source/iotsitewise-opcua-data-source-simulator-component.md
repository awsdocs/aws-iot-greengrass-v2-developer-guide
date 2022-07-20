# IoT SiteWise OPC\-UA data source simulator<a name="iotsitewise-opcua-data-source-simulator-component"></a>

The IoT SiteWise OPC\-UA data source simulator component \(`aws.iot.SiteWiseEdgeOpcuaDataSourceSimulator`\) starts a local OPC\-UA server that generates sample data\. Use this OPC\-UA server to simulate a data source read by the [IoT SiteWise OPC\-UA collector component](iotsitewise-opcua-collector-component.md) on an AWS IoT SiteWise gateway\. Then, you can explore AWS IoT SiteWise features using this sample data\. For more information about AWS IoT SiteWise gateways, see [Using AWS IoT SiteWise at the edge](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/gateways-ggv2.html) in the *AWS IoT SiteWise User Guide*\.

**Topics**
+ [Versions](#iotsitewise-opcua-data-source-simulator-component-versions)
+ [Type](#iotsitewise-opcua-data-source-simulator-component-type)
+ [Operating system](#iotsitewise-opcua-data-source-simulator-component-os-support)
+ [Requirements](#iotsitewise-opcua-data-source-simulator-component-requirements)
+ [Dependencies](#iotsitewise-opcua-data-source-simulator-component-dependencies)
+ [Configuration](#iotsitewise-opcua-data-source-simulator-component-configuration)
+ [Local log file](#iotsitewise-opcua-data-source-simulator-component-log-file)
+ [Changelog](#iotsitewise-opcua-data-source-simulator-component-changelog)
+ [See also](#iotsitewise-opcua-data-source-simulator-component-see-also)

## Versions<a name="iotsitewise-opcua-data-source-simulator-component-versions"></a>

This component has the following versions:
+ 1\.0\.x

## Type<a name="iotsitewise-opcua-data-source-simulator-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="iotsitewise-opcua-data-source-simulator-component-os-support"></a>

This component can be installed on Linux core devices only\.

## Requirements<a name="iotsitewise-opcua-data-source-simulator-component-requirements"></a>

This component has the following requirements:
+ The Greengrass core device must be able to use port 4840 on the local host\. This component's local OPC\-UA server runs at this port\.

## Dependencies<a name="iotsitewise-opcua-data-source-simulator-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#iotsitewise-opcua-data-source-simulator-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for all versions of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.3\.0 <3\.0\.0 | Hard | 

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="iotsitewise-opcua-data-source-simulator-component-configuration"></a>

This component doesn't have any configuration parameters\.

## Local log file<a name="iotsitewise-opcua-data-source-simulator-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/aws.iot.SiteWiseEdgeOpcuaDataSourceSimulator.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.iot.SiteWiseEdgeOpcuaDataSourceSimulator.log
  ```

## Changelog<a name="iotsitewise-opcua-data-source-simulator-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  1\.0\.0  |  Initial version\.  | 

## See also<a name="iotsitewise-opcua-data-source-simulator-component-see-also"></a>
+ [What is AWS IoT SiteWise?](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/what-is-sitewise.html) in the *AWS IoT SiteWise User Guide*\.