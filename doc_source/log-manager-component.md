# Log manager<a name="log-manager-component"></a>

The log manager component \(`aws.greengrass.LogManager`\) uploads logs from AWS IoT Greengrass core devices to Amazon CloudWatch Logs\. You can upload logs from the Greengrass nucleus, other Greengrass components, and other applications and services that aren't Greengrass components\.

For information about the log groups and log streams to which this component uploads logs, see [Usage](#log-manager-component-usage)\.

**Topics**
+ [Versions](#log-manager-component-versions)
+ [Type](#log-manager-component-type)
+ [Requirements](#log-manager-component-requirements)
+ [Dependencies](#log-manager-component-dependencies)
+ [Configuration](#log-manager-component-configuration)
+ [Usage](#log-manager-component-usage)
+ [Local log file](#log-manager-component-log-file)
+ [Changelog](#log-manager-component-changelog)

## Versions<a name="log-manager-component-versions"></a>

This component has the following versions:
+ 2\.1\.x
+ 2\.0\.x

## Type<a name="log-manager-component-type"></a>

<a name="public-component-type-plugin-para1"></a>This component is a plugin component \(`aws.greengrass.plugin`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs this component in the same Java Virtual Machine \(JVM\) as the nucleus\. The nucleus restarts when you change this component's version on the core device\.

<a name="public-component-type-plugin-para2"></a>This component uses the same log file as the Greengrass nucleus\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Requirements<a name="log-manager-component-requirements"></a>

This component has the following requirements:
+ The [Greengrass device role](device-service-role.md) must allow the `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`, and `logs:DescribeLogStreams` actions, as shown in the following example IAM policy\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "logs:CreateLogGroup",
          "logs:CreateLogStream",
          "logs:PutLogEvents",
          "logs:DescribeLogStreams"
        ],
        "Effect": "Allow",
        "Resource": "arn:aws:logs:*:*:*"
      }
    ]
  }
  ```
**Note**  
The [Greengrass device role](device-service-role.md) that you create when you install the AWS IoT Greengrass Core software includes the permissions in this example policy by default\.

  For more information, see [Using identity\-based policies \(IAM policies\) for CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/iam-identity-based-access-control-cwl.html) in the *Amazon CloudWatch Logs User Guide*\.

### Endpoints and ports<a name="log-manager-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `logs.region.amazonaws.com`  | 443 | No |  Required if you write logs to CloudWatch Logs\.  | 

## Dependencies<a name="log-manager-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#log-manager-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.1\.3 ]

The following table lists the dependencies for version 2\.1\.3 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.1\.0 <2\.5\.0 | Soft | 

------
#### [ 2\.1\.2 ]

The following table lists the dependencies for version 2\.1\.2 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.1\.0 <2\.4\.0 | Soft | 

------
#### [ 2\.1\.1 ]

The following table lists the dependencies for version 2\.1\.1 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.1\.0 <2\.3\.0 | Soft | 

------
#### [ 2\.1\.0 ]

The following table lists the dependencies for version 2\.1\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.1\.0 <2\.2\.0  | Soft | 

------
#### [ 2\.0\.x ]

The following table lists the dependencies for version 2\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.3 <2\.1\.0  | Soft | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="log-manager-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

------
#### [ v2\.1\.x ]

`logsUploaderConfiguration`  
\(Optional\) The configuration for logs that the log manager component uploads\. This object contains the following information:    
  `systemLogsConfiguration`   
\(Optional\) The configuration for AWS IoT Greengrass Core software system logs, which include logs from the [Greengrass nucleus](greengrass-nucleus-component.md) and [plugin components](develop-greengrass-components.md#component-types)\. Specify this configuration to enable the log manager component to manage system logs\. This object contains the following information:    
`uploadToCloudWatch`  <a name="log-manager-component-configuration-system-upload-to-cloud-watch"></a>
\(Optional\) You can upload system logs to CloudWatch Logs\.  
Default: `false`  
`minimumLogLevel`  <a name="log-manager-component-configuration-system-minimum-log-level"></a>
\(Optional\) The minimum level of log messages to upload\. This minimum level applies only if you configure the [Greengrass nucleus component](greengrass-nucleus-component.md) to output JSON format logs\. To enable JSON format logs, specify `JSON` for the [logging format](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-logging-format) parameter \(`logging.format`\)\.  
Choose from the following log levels, listed here in level order:  <a name="nucleus-log-levels"></a>
+ `DEBUG`
+ `INFO`
+ `WARN`
+ `ERROR`
Default: `INFO`  
`diskSpaceLimit`  <a name="log-manager-component-configuration-system-disk-space-limit"></a>
\(Optional\) The maximum total size of Greengrass system log files, in the unit you specify in `diskSpaceLimitUnit`\. After the total size of Greengrass system log files exceeds this maximum total size, the AWS IoT Greengrass Core software deletes the oldest Greengrass system log files\.  
This parameter is equivalent to the [log size limit](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-system-logs-limit) parameter \(`totalLogsSizeKB`\) of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. The AWS IoT Greengrass Core software uses the minimum of the two values as the maximum total Greengrass system log size\.  
`diskSpaceLimitUnit`  <a name="log-manager-component-configuration-disk-space-limit-unit"></a>
\(Optional\) The unit for the `diskSpaceLimit`\. Choose from the following options:  
+ `KB` – kilobytes
+ `MB` – megabytes
+ `GB` – gigabytes
Default: `KB`  
`deleteLogFileAfterCloudUpload`  <a name="log-manager-component-configuration-delete-log-file-after-cloud-upload"></a>
\(Optional\) You can delete a log file after the log manager component uploads the logs to CloudWatch Logs\.  
Default: `false`  
  `componentLogsConfiguration`   
\(Optional\) A list of log configurations for components on the core device\. Each configuration in this list defines the log configuration for a component or application\. The log manager component uploads these component logs to CloudWatch Logs  
Each object contains the following information:    
`componentName`  <a name="log-manager-component-configuration-component-component-name"></a>
The name of the component for this logs configuration\. You can specify the name of a Greengrass component or another value to identify this log group\.  
`minimumLogLevel`  <a name="log-manager-component-configuration-component-minimum-log-level"></a>
\(Optional\) The minimum level of log messages to upload\. This minimum level applies only if this component's logs use a specific JSON format, which you can find in the [AWS IoT Greengrass logging module](https://github.com/aws-greengrass/aws-greengrass-logging-java) repository on GitHub\.  
Choose from the following log levels, listed here in level order:  <a name="nucleus-log-levels"></a>
+ `DEBUG`
+ `INFO`
+ `WARN`
+ `ERROR`
Default: `INFO`  
`logFileDirectoryPath`  
\(Optional\) The path to the folder that contains this component's log files\.  
You don't need to specify this parameter for Greengrass components that print to standard output \(stdout\) and standard error \(stderr\)\.  
Default: `/greengrass/v2/logs`\.  
`logFileRegex`  
\(Optional\) A regular expression that specifies the log file name format that the component or application uses\. The log manager component uses this regular expression to identify log files in the folder at `logFileDirectoryPath`\.  
You don't need to specify this parameter for Greengrass components that print to standard output \(stdout\) and standard error \(stderr\)\.  
If your component or application rotates log files, specify a regex that matches the rotated log file names\. For example, you might specify **hello\_world\\\\w\*\.log** to upload logs for a Hello World application\. The `\\w*` pattern matches zero or more word characters, which includes alphanumeric characters and underscores\. This regex matches log files with and without timestamps in their name\. In this example, the log manager uploads the following log files:  
+ `hello_world.log` – The most recent log file for the Hello World application\.
+ `hello_world_2020_12_15_17_0.log` – An older log file for the Hello World application\.
Default: `componentName\\w*.log`, where *componentName* is the name of the component for this log configuration\.  
`diskSpaceLimit`  <a name="log-manager-component-configuration-component-disk-space-limit"></a>
\(Optional\) The maximum total size of all log files for this component, in the unit you specify in `diskSpaceLimitUnit`\. After the total size of this component's log files exceeds this maximum total size, the AWS IoT Greengrass Core software deletes this component's oldest log files\.  
This parameter is related to the [log size limit](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-system-logs-limit) parameter \(`totalLogsSizeKB`\) of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. The AWS IoT Greengrass Core software uses the minimum of the two values as the maximum total log size for this component\.  
`diskSpaceLimitUnit`  <a name="log-manager-component-configuration-disk-space-limit-unit"></a>
\(Optional\) The unit for the `diskSpaceLimit`\. Choose from the following options:  
+ `KB` – kilobytes
+ `MB` – megabytes
+ `GB` – gigabytes
Default: `KB`  
`deleteLogFileAfterCloudUpload`  <a name="log-manager-component-configuration-delete-log-file-after-cloud-upload"></a>
\(Optional\) You can delete a log file after the log manager component uploads the logs to CloudWatch Logs\.  
Default: `false`

  `periodicUploadIntervalSec`   
\(Optional\) The period in seconds at which the log manager component checks for new log files to upload\.  
Default: `300` \(5 minutes\)

**Example: Configuration merge update**  
The following example configuration specifies to upload system logs and `com.example.HelloWorld` component logs to CloudWatch Logs\.  

```
{
  "logsUploaderConfiguration": {
    "systemLogsConfiguration": {
      "uploadToCloudWatch": "true",
      "minimumLogLevel": "INFO",
      "diskSpaceLimit": "10",
      "diskSpaceLimitUnit": "MB",
      "deleteLogFileAfterCloudUpload": "false"
    },
    "componentLogsConfiguration": [
      {
        "componentName": "com.example.HelloWorld",
        "minimumLogLevel": "INFO",
        "diskSpaceLimit": "10",
        "diskSpaceLimitUnit": "KB",
        "deleteLogFileAfterCloudUpload": "false"
      }
    ]
  },
  "periodicUploadIntervalSec": "300"
}
```

------
#### [ v2\.0\.x ]

`logsUploaderConfiguration`  
\(Optional\) The configuration for logs that the log manager component uploads\. This object contains the following information:    
`systemLogsConfiguration`  
\(Optional\) The configuration for AWS IoT Greengrass Core software system logs\. Specify this configuration to enable the log manager component to manage system logs\. This object contains the following information:    
`uploadToCloudWatch`  <a name="log-manager-component-configuration-system-upload-to-cloud-watch"></a>
\(Optional\) You can upload system logs to CloudWatch Logs\.  
Default: `false`  
`minimumLogLevel`  <a name="log-manager-component-configuration-system-minimum-log-level"></a>
\(Optional\) The minimum level of log messages to upload\. This minimum level applies only if you configure the [Greengrass nucleus component](greengrass-nucleus-component.md) to output JSON format logs\. To enable JSON format logs, specify `JSON` for the [logging format](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-logging-format) parameter \(`logging.format`\)\.  
Choose from the following log levels, listed here in level order:  <a name="nucleus-log-levels"></a>
+ `DEBUG`
+ `INFO`
+ `WARN`
+ `ERROR`
Default: `INFO`  
`diskSpaceLimit`  <a name="log-manager-component-configuration-system-disk-space-limit"></a>
\(Optional\) The maximum total size of Greengrass system log files, in the unit you specify in `diskSpaceLimitUnit`\. After the total size of Greengrass system log files exceeds this maximum total size, the AWS IoT Greengrass Core software deletes the oldest Greengrass system log files\.  
This parameter is equivalent to the [log size limit](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-system-logs-limit) parameter \(`totalLogsSizeKB`\) of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. The AWS IoT Greengrass Core software uses the minimum of the two values as the maximum total Greengrass system log size\.  
`diskSpaceLimitUnit`  <a name="log-manager-component-configuration-disk-space-limit-unit"></a>
\(Optional\) The unit for the `diskSpaceLimit`\. Choose from the following options:  
+ `KB` – kilobytes
+ `MB` – megabytes
+ `GB` – gigabytes
Default: `KB`  
`deleteLogFileAfterCloudUpload`  <a name="log-manager-component-configuration-delete-log-file-after-cloud-upload"></a>
\(Optional\) You can delete a log file after the log manager component uploads the logs to CloudWatch Logs\.  
Default: `false`  
`componentLogsConfiguration`  
\(Optional\) A list of log configurations for components on the core device\. Each configuration in this list defines the log configuration for a component or application\. The log manager component uploads these component logs to CloudWatch Logs  
Each object contains the following information:    
`componentName`  <a name="log-manager-component-configuration-component-component-name"></a>
The name of the component for this logs configuration\. You can specify the name of a Greengrass component or another value to identify this log group\.  
`minimumLogLevel`  <a name="log-manager-component-configuration-component-minimum-log-level"></a>
\(Optional\) The minimum level of log messages to upload\. This minimum level applies only if this component's logs use a specific JSON format, which you can find in the [AWS IoT Greengrass logging module](https://github.com/aws-greengrass/aws-greengrass-logging-java) repository on GitHub\.  
Choose from the following log levels, listed here in level order:  <a name="nucleus-log-levels"></a>
+ `DEBUG`
+ `INFO`
+ `WARN`
+ `ERROR`
Default: `INFO`  
`logFileDirectoryPath`  
The path to the folder that contains this component's log files\.  
To upload a Greengrass component's logs, specify ***/greengrass/v2*/logs**, and replace */greengrass/v2* with your Greengrass root folder\.  
`logFileRegex`  
A regular expression that specifies the log file name format that the component or application uses\. The log manager component uses this regular expression to identify log files in the folder at `logFileDirectoryPath`\.  
To upload a Greengrass component's logs, specify a regex that matches the rotated log file names\. For example, you might specify **com\.example\.HelloWorld\\\\w\*\.log** to upload logs for a Hello World component\. The `\\w*` pattern matches zero or more word characters, which includes alphanumeric characters and underscores\. This regex matches log files with and without timestamps in their name\. In this example, the log manager uploads the following log files:  
+ `com.example.HelloWorld.log` – The most recent log file for the Hello World component\.
+ `com.example.HelloWorld_2020_12_15_17_0.log` – An older log file for the Hello World component\. The Greengrass nucleus adds a rotating timestamp to the log files\.  
`diskSpaceLimit`  <a name="log-manager-component-configuration-component-disk-space-limit"></a>
\(Optional\) The maximum total size of all log files for this component, in the unit you specify in `diskSpaceLimitUnit`\. After the total size of this component's log files exceeds this maximum total size, the AWS IoT Greengrass Core software deletes this component's oldest log files\.  
This parameter is related to the [log size limit](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-system-logs-limit) parameter \(`totalLogsSizeKB`\) of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. The AWS IoT Greengrass Core software uses the minimum of the two values as the maximum total log size for this component\.  
`diskSpaceLimitUnit`  <a name="log-manager-component-configuration-disk-space-limit-unit"></a>
\(Optional\) The unit for the `diskSpaceLimit`\. Choose from the following options:  
+ `KB` – kilobytes
+ `MB` – megabytes
+ `GB` – gigabytes
Default: `KB`  
`deleteLogFileAfterCloudUpload`  <a name="log-manager-component-configuration-delete-log-file-after-cloud-upload"></a>
\(Optional\) You can delete a log file after the log manager component uploads the logs to CloudWatch Logs\.  
Default: `false`

`periodicUploadIntervalSec`  
\(Optional\) The period in seconds at which the log manager component checks for new log files to upload\.  
Default: `300` \(5 minutes\)

**Example: Configuration merge update**  
The following example configuration specifies to upload system logs and `com.example.HelloWorld` component logs to CloudWatch Logs\.  

```
{
  "logsUploaderConfiguration": {
    "systemLogsConfiguration": {
      "uploadToCloudWatch": "true",
      "minimumLogLevel": "INFO",
      "diskSpaceLimit": "10",
      "diskSpaceLimitUnit": "MB",
      "deleteLogFileAfterCloudUpload": "false"
    },
    "componentLogsConfiguration": [
      {
        "componentName": "com.example.HelloWorld",
        "minimumLogLevel": "INFO",
        "logFileDirectoryPath": "/greengrass/v2/logs",
        "logFileRegex": "com.example.HelloWorld\\w*.log",
        "diskSpaceLimit": "10",
        "diskSpaceLimitUnit": "KB",
        "deleteLogFileAfterCloudUpload": "false"
      }
    ]
  },
  "periodicUploadIntervalSec": "300"
}
```

------

## Usage<a name="log-manager-component-usage"></a>

The log manager component uploads to the following log groups and log streams\.

------
#### [ 2\.1\.x ]<a name="log-manager-log-group-stream-format"></a>

**Log group name**  

```
/aws/greengrass/componentType/region/componentName
```
The log group name uses the following variables:  
+ `componentType` – The type of the component, which can be one of the following:
  + `GreengrassSystemComponent` – The component is part of the [Greengrass nucleus](greengrass-nucleus-component.md)\. This log group includes logs for plugin components, which run in the same JVM as the Greengrass nucleus\.
  + `UserComponent` – The component isn't part of the Greengrass nucleus\. This log group includes logs for generic components, Lambda components, and other applications on the device\.

  For more information, see [Component types](develop-greengrass-components.md#component-types)\.
+ `region` – The AWS Region that the core device uses\.
+ `componentName` – The name of the component\. For system logs, this value is `System`\.

**Log stream name**  

```
/date/thing/thingName
```
The log stream name uses the following variables:  
+ `date` – The date of the log, such as `2020/12/15`\. The log manager component uses the `yyyy/MM/dd` format\.
+ `thingName` – The name of the core device\.
If a thing name contains a colon \(`:`\), the log manager replaces the colon with a plus \(`+`\)\.

------
#### [ 2\.0\.x ]

**Log group name**  

```
/aws/greengrass/componentType/region/componentName
```
The log group name uses the following variables:  
+ `componentType` – The type of the component, which can be one of the following:
  + `GreengrassSystemComponent` – The component is part of the [Greengrass nucleus](greengrass-nucleus-component.md)\.
  + `UserComponent` – The component isn't part of the Greengrass nucleus\. The log manager uses this type for Greengrass components and other applications on the device\.
+ `region` – The AWS Region that the core device uses\.
+ `componentName` – The name of the component\. For system logs, this value is `System`\.

**Log stream name**  

```
/date/deploymentTargets/thingName
```
The log stream name uses the following variables:  
+ `date` – The date of the log, such as `2020/12/15`\. The log manager component uses the `yyyy/MM/dd` format\.
+ `deploymentTargets` – The things whose deployments include the component\. The log manager component separates each target by a slash\. If the component runs on the core device as the result of a local deployment, this value is `LOCAL_DEPLOYMENT`\.

  Consider an example where you have a core device named `MyGreengrassCore`, and the core device has two deployments:
  + A deployment that targets the core device, `MyGreengrassCore`\.
  + A deployment that targets a thing group named `MyGreengrassCoreGroup`, which contains the core device\.

  The `deploymentTargets` for this core device are `thing/MyGreengrassCore/thinggroup/MyGreengrassCoreGroup`\.
+ `thingName` – The name of the core device\.

------

## Local log file<a name="log-manager-component-log-file"></a>

This component uses the same log file as the [Greengrass nucleus](greengrass-nucleus-component.md) component\.

```
/greengrass/v2/logs/greengrass.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/greengrass.log
  ```

## Changelog<a name="log-manager-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.1\.3  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.1\.2  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.1\.1  |  <a name="changelog-log-manager-2.1.1"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/log-manager-component.html)  | 
|  2\.1\.0  |  <a name="changelog-log-manager-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/log-manager-component.html)  | 
|  2\.0\.x  |  Initial version\.  | 