# Log manager<a name="log-manager-component"></a>

The log manager component \(`aws.greengrass.LogManager`\) collects and optionally uploads logs from AWS IoT Greengrass core devices to Amazon CloudWatch Logs\. You can configure system logs and logs for each component\.

If you enable the log manager component to upload to CloudWatch Logs, it uses the following log groups and log streams:
+ **Log group name**: `/aws/greengrass/componentType/region/componentName`

  The log group name uses the following variables:
  + `componentType` – The type of the component\.
  + `region` – The AWS Region that the core device uses\.
  + `componentName` – The name of the component\.
+ **Log stream name**: `/date/groups/thingName`

  The log stream name uses the following variables:
  + `date` – The date of the log, such as `2020/12/15`\. The log manager component uses the `yyyy/MM/dd` format\.
  + `groups` – The thing groups whose deployments include the component\. The log manager component separates each thing group name by a colon \(`:`\)\. If the component runs on the core devie as the result of a local deployment or a deployment that targets the core device, this value is `LOCAL_DEPLOYMENT`\.
  + `thingName` – The name of the core device\.

This component has the following versions:
+ 2\.0\.x

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
\(Optional\) The minimum level of information to log\. Choose from the following log levels, listed here in level order:  
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
\(Optional\) A list of logs configurations for components on the core device\. Each configuration in this list defines the log configuration for a component\. Each object contains the following information:    
`componentName`  
The name of the component for this logs configuration\.  
`minimumLogLevel`  
\(Optional\) The minimum level of information to log\. Choose from the following log levels, listed here in level order:  
+ `DEBUG`
+ `INFO`
+ `WARN`
+ `ERROR`
Default: `INFO`  
`logFileDirectoryPath`  
The path to the directory to use for this component's log files\.  
`logFileRegex`  
A regular expression that specifies the log file name format to use\. Specify a regex that matches the rotated log file names\. For example, you might specify `radio_unit\w*.log`\. The log manager creates the following log files, for example:  
+ `radio_unit.log` – The most recent log file for this component\.
+ `radio_unit_2020_12_15_17_0.log` – An older log file for this component\. The log component adds a timestamp to the log files that it rotates\.  
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