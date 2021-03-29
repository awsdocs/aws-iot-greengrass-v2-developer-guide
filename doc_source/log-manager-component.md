# Log manager<a name="log-manager-component"></a>

The log manager component \(`aws.greengrass.LogManager`\) uploads logs from AWS IoT Greengrass core devices to Amazon CloudWatch Logs\. You can upload logs from the Greengrass nucleus, other Greengrass components, and other applications and services that aren't Greengrass components\.

The log manager component uploads to the following log groups and log streams:
+ **Log group name**: `/aws/greengrass/componentType/region/componentName`

  The log group name uses the following variables:
  + `componentType` – The type of the component, which can be one of the following:
    + `GreengrassSystemComponent` – The component is part of the [Greengrass nucleus](greengrass-nucleus-component.md)\.
    + `UserComponent` – The component isn't part of the Greengrass nucleus\. The log manager uses this type for Greengrass components and other applications on the device\.
  + `region` – The AWS Region that the core device uses\.
  + `componentName` – The name of the component\. For system logs, this value is `System`\.
+ **Log stream name**: `/date/deploymentTargets/thingName`

  The log stream name uses the following variables:
  + `date` – The date of the log, such as `2020/12/15`\. The log manager component uses the `yyyy/MM/dd` format\.
  + `deploymentTargets` – The things and thing groups whose deployments include the component\. The log manager component separates each target by a colon \(`:`\)\. If the component runs on the core device as the result of a local deployment, this value is `LOCAL_DEPLOYMENT`\.
  + `thingName` – The name of the core device\.

This component has the following versions:
+ 2\.0\.x

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
        "Resource": "*"
      }
    ]
  }
  ```
**Note**  
The [Greengrass device role](device-service-role.md) that you create when you install the AWS IoT Greengrass Core software includes the permissions in this example policy by default\.

  For more information, see [Using identity\-based policies \(IAM policies\) for CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/iam-identity-based-access-control-cwl.html) in the *Amazon CloudWatch Logs User Guide*\.

## Configuration<a name="log-manager-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`logsUploaderConfiguration`  
\(Optional\) The configuration for logs that the log manager component uploads\. This object contains the following information:    
`systemLogsConfiguration`  
\(Optional\) The configuration for AWS IoT Greengrass Core software system logs\. This object contains the following information:    
`uploadToCloudWatch`  
\(Optional\) You can upload system logs to CloudWatch Logs\.  
Default: `true`  
`minimumLogLevel`  
\(Optional\) The minimum level of information to upload\. Choose from the following log levels, listed here in level order:  
+ `DEBUG`
+ `INFO`
+ `WARN`
+ `ERROR`
Default: `INFO`  
  `diskSpaceLimit`   
\(Optional\) The maximum total size of all system log files, in the unit you specify in `diskSpaceLimitUnit`\. After the total size of all log files exceeds this maximum total size, the AWS IoT Greengrass Core software deletes the oldest log files\.  
This parameter is equivalent to the [system logs disk space limit](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-system-logs-limit) parameter of the [Greengrass nucleus component](greengrass-nucleus-component.md)\. If you specify this parameter on both components, the AWS IoT Greengrass Core software uses the minimum of the two values as the maximum total log size\.  
`diskSpaceLimitUnit`  <a name="log-manager-component-configuration-disk-space-limit-unit"></a>
\(Optional\) The unit for the `diskSpaceLimit`\. Choose from the following options:  
+ `KB` – kilobytes
+ `MB` – megabytes
+ `GB` – gigabytes
Default: `MB`  
`deleteLogFileAfterCloudUpload`  <a name="log-manager-component-configuration-delete-log-file-after-cloud-upload"></a>
\(Optional\) You can delete a log file after the log manager component uploads the logs to CloudWatch Logs\.  
Default: `true`  
`componentLogsConfiguration`  
\(Optional\) A list of log configurations for components on the core device\. Each configuration in this list defines the log configuration for a component or application\. The log manager component uploads these component logs to CloudWatch Logs  
Each object contains the following information:    
`componentName`  
The name of the component for this logs configuration\. You can specify the name of a Greengrass component or another value to identify this log group\.  
`minimumLogLevel`  
\(Optional\) The minimum level of information to upload\. Choose from the following log levels, listed here in level order:  
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
`diskSpaceLimit`  
\(Optional\) The maximum total size of all log files for this component, in the unit you specify in `diskSpaceLimitUnit`\. After the total size of this component's log files exceeds this maximum total size, the AWS IoT Greengrass Core software deletes the oldest log files\.  
`diskSpaceLimitUnit`  <a name="log-manager-component-configuration-disk-space-limit-unit"></a>
\(Optional\) The unit for the `diskSpaceLimit`\. Choose from the following options:  
+ `KB` – kilobytes
+ `MB` – megabytes
+ `GB` – gigabytes
Default: `MB`  
`deleteLogFileAfterCloudUpload`  <a name="log-manager-component-configuration-delete-log-file-after-cloud-upload"></a>
\(Optional\) You can delete a log file after the log manager component uploads the logs to CloudWatch Logs\.  
Default: `true`

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
  "periodicUploadIntervalSec": "60"
}
```