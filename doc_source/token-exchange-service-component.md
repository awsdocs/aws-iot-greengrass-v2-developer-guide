# Token exchange service<a name="token-exchange-service-component"></a>

The token exchange service component \(`aws.greengrass.TokenExchangeService`\) provides AWS credentials that you can use to interact with AWS services in your custom components\.

The token exchange service runs an Amazon Elastic Container Service \(Amazon ECS\) container instance as a local server\. This local server connects to the AWS IoT credentials provider using the AWS IoT role alias that you configure in the [Greengrass core nucleus component](greengrass-nucleus-component.md)\. The component provides an environment variable, `AWS_CONTAINER_CREDENTIALS_FULL_URI`, that defines the URI to this local server\. When a component creates an AWS SDK client, the client recognizes this URI environment variable to connect to the token exchange service and retrieve AWS credentials\. This allows Greengrass core devices to call AWS service operations\. For more information about how to use this component in custom components, see [Interact with AWS services](interact-with-aws-services.md)\.

**Important**  <a name="token-exchange-service-aws-sdk-requirement"></a>
Support to acquire AWS credentials in this way was added to the AWS SDKs on July 13th, 2016\. Your component must use an AWS SDK version that was created on or after that date\. For more information, see [Using a supported AWS SDK](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html#task-iam-roles-minimum-sdk) in the *Amazon Elastic Container Service Developer Guide*\.

**Topics**
+ [Versions](#token-exchange-service-component-versions)
+ [Type](#token-exchange-service-component-type)
+ [Operating system](#token-exchange-service-component-os-support)
+ [Dependencies](#token-exchange-service-component-dependencies)
+ [Configuration](#token-exchange-service-component-configuration)
+ [Local log file](#token-exchange-service-component-log-file)
+ [Changelog](#token-exchange-service-component-changelog)

## Versions<a name="token-exchange-service-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

## Type<a name="token-exchange-service-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="token-exchange-service-component-os-support"></a>

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

## Dependencies<a name="token-exchange-service-component-dependencies"></a>

This component doesn't have any dependencies\.

## Configuration<a name="token-exchange-service-component-configuration"></a>

This component doesn't have any configuration parameters\.

## Local log file<a name="token-exchange-service-component-log-file"></a>

This component uses the same log file as the [Greengrass nucleus](greengrass-nucleus-component.md) component\.

------
#### [ Linux ]

```
/greengrass/v2/logs/greengrass.log
```

------
#### [ Windows ]

```
C:\greengrass\v2\logs\greengrass.log
```

------

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux ]

  ```
  sudo tail -f /greengrass/v2/logs/greengrass.log
  ```

------
#### [ Windows \(PowerShell\) ]

  ```
  Get-Content C:\greengrass\v2\logs\greengrass.log -Tail 10 -Wait
  ```

------

## Changelog<a name="token-exchange-service-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.3  |  Initial version\.  | 