# Lambda launcher<a name="lambda-launcher-component"></a>

The Lambda launcher component \(`aws.greengrass.LambdaLauncher`\) starts and stops AWS Lambda functions on AWS IoT Greengrass core devices\. This component also sets up any containerization and runs processes as the users that you specify\.

**Note**  <a name="lambda-component-dependency-note"></a>
When you deploy a Lambda function component to a core device, the deployment also includes this component\. For more information, see [Run AWS Lambda functions](run-lambda-functions.md)\.

**Topics**
+ [Versions](#lambda-launcher-component-versions)
+ [Type](#lambda-launcher-component-type)
+ [Operating system](#lambda-launcher-component-os-support)
+ [Requirements](#lambda-launcher-component-requirements)
+ [Dependencies](#lambda-launcher-component-dependencies)
+ [Configuration](#lambda-launcher-component-configuration)
+ [Local log file](#lambda-launcher-component-log-file)
+ [Changelog](#lambda-launcher-component-changelog)

## Versions<a name="lambda-launcher-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="lambda-launcher-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="lambda-launcher-component-os-support"></a>

This component can be installed on Linux core devices only\.

## Requirements<a name="lambda-launcher-component-requirements"></a>

This component has the following requirements:
+ <a name="core-device-lambda-function-requirements"></a>Your core device must meet the requirements to run Lambda functions\. If you want the core device to run containerized Lambda functions, the device must meet the requirements to do so\. For more information, see [Lambda function requirements](setting-up.md#greengrass-v2-lambda-requirements)\.

## Dependencies<a name="lambda-launcher-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#lambda-launcher-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.0\.9 ]

The following table lists the dependencies for version 2\.0\.9 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Lambda manager](lambda-manager-component.md) | >=2\.0\.0 <2\.3\.0  | Hard | 

------
#### [ 2\.0\.4 \- 2\.0\.8 ]

The following table lists the dependencies for versions 2\.0\.4 to 2\.0\.8 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Lambda manager](lambda-manager-component.md) | >=2\.0\.0 <2\.2\.0  | Hard | 

------
#### [ 2\.0\.3 ]

The following table lists the dependencies for version 2\.0\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Lambda manager](lambda-manager-component.md) | >=2\.0\.3 <2\.1\.0 | Hard | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="lambda-launcher-component-configuration"></a>

This component doesn't have any configuration parameters\.

## Local log file<a name="lambda-launcher-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/lambdaFunctionComponentName.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder, and replace *lambdaFunctionComponentName* with the name of the Lambda function component that this component launches\.

  ```
  sudo tail -f /greengrass/v2/logs/lambdaFunctionComponentName.log
  ```

## Changelog<a name="lambda-launcher-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.9  |  Version updated for Greengrass nucleus version 2\.5\.0 release\.  | 
|  2\.0\.8  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.7  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.6  |  General performance improvements and bug fixes\.  | 
|  2\.0\.4  |  <a name="changelog-lambda-launcher-2.0.4"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/lambda-launcher-component.html)  | 
|  2\.0\.3  |  Initial version\.  | 