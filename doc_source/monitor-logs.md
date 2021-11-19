# Monitor AWS IoT Greengrass logs<a name="monitor-logs"></a>

AWS IoT Greengrass consists of the cloud service and the AWS IoT Greengrass Core software\. The AWS IoT Greengrass Core software can write logs to Amazon CloudWatch Logs and to the core device's local file system\. Greengrass components that run on the core device can also write logs to CloudWatch Logs and the local file system\. You can use logs to monitor events and troubleshoot issues\. All AWS IoT Greengrass log entries include a timestamp, log level, and information about the event\.

By default, the AWS IoT Greengrass Core software writes logs to only the local file system\. You can view file system logs in real time, so you can debug Greengrass components that you develop and deploy\. You can also configure a core device to write logs to CloudWatch Logs, so you can troubleshoot the core device without access to the local file system\. For more information, see [Enable logging to CloudWatch Logs](#enable-cloudwatch-logs)\.

**Topics**
+ [Access file system logs](#access-local-logs)
+ [Access CloudWatch Logs](#access-cloudwatch-logs)
+ [Access system service logs](#access-system-service-logs)
+ [Enable logging to CloudWatch Logs](#enable-cloudwatch-logs)
+ [Configure logging for AWS IoT Greengrass](#configure-logging)
+ [AWS CloudTrail logs](#cloudtrail-integration)

## Access file system logs<a name="access-local-logs"></a>

The AWS IoT Greengrass Core software stores logs in the `/greengrass/v2/logs` folder on a core device, where */greengrass/v2* is the path to the AWS IoT Greengrass root folder\. The logs folder has the following structure\.

```
/greengrass/v2
└── logs
    ├── greengrass.log
    ├── greengrass_2021_09_14_15_0.log
    ├── ComponentName.log
    ├── ComponentName_2021_09_14_15_0.log
    └── main.log
```
+ `greengrass.log` – The AWS IoT Greengrass Core software log file\. Use this log file to view real\-time information about components and deployments\. This log file includes logs for the Greengrass nucleus, which is the core of the AWS IoT Greengrass Core software, and plugin components, such as [log manager](log-manager-component.md) and [secret manager](secret-manager-component.md)\.
+ `ComponentName.log` – Greengrass component log files\. Use component log files to view real\-time information about a Greengrass component that runs on the core device\. Generic components and Lambda components write standard output \(stdout\) and standard error \(stderr\) to these log files\.
+ `main.log` – The log file for the `main` service that handles component lifecycles\. This log file will always be empty\.

For more information about the differences between plugin, generic, and Lambda components, see [Component types](develop-greengrass-components.md#component-types)\.

The following considerations apply when you use file system logs:
+ **Root user permissions**

  You must have root permissions to read AWS IoT Greengrass logs on the file system\.
+ **Log file rotation**

  The AWS IoT Greengrass Core software rotates log files every hour or when they exceed a file size limit\. Rotated log files contain a timestamp in their file name\. For example, a rotated AWS IoT Greengrass Core software log file might be named `greengrass_2021_09_14_15_0.log`\. The default file size limit is 1,024 KB \(1 MB\)\. You can configure the file size limit on the [Greengrass nucleus component](greengrass-nucleus-component.md)\.
+ **Log file deletion**

  The AWS IoT Greengrass Core software cleans up earlier log files when the size of AWS IoT Greengrass Core software log files or Greengrass component log files, including rotated log files, exceeds a disk space limit\. The default disk space limit for the AWS IoT Greengrass Core software log and each component log is 10,240 KB \(10 MB\)\. You can configure the AWS IoT Greengrass Core software log disk space limit on the [Greengrass nucleus component](greengrass-nucleus-component.md) or the [log manager component](log-manager-component.md)\. You can configure each component's log disk space limit on the [log manager component](log-manager-component.md)\.

**To view the AWS IoT Greengrass Core software log file**
+ Run the following command to view the log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux or Unix ]

  ```
  sudo tail -f /greengrass/v2/logs/greengrass.log
  ```

------
#### [ PowerShell ]

  ```
  gc C:\greengrass\v2\logs\greengrass.log -Tail 10 -Wait
  ```

------

**To view the log file for a component**
+ Run the following command to view the log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder, and replace *com\.example\.HelloWorld* with the name of the component\.

------
#### [ Linux or Unix ]

  ```
  sudo tail -f /greengrass/v2/logs/com.example.HelloWorld.log
  ```

------
#### [ PowerShell ]

  ```
  gc C:\greengrass\v2\logs\com.example.HelloWorld.log -Tail 10 -Wait
  ```

------

You can also use the `logs` command of the [Greengrass CLI](greengrass-cli-component.md) to analyze Greengrass logs on a core device\. To use the `logs` command, you must configure the [Greengrass nucleus](greengrass-nucleus-component.md) to output JSON format log files\. For more information, see [Greengrass Command Line Interface](gg-cli.md) and [logs](gg-cli-logs.md)\.

## Access CloudWatch Logs<a name="access-cloudwatch-logs"></a>

You can deploy the [log manager component](log-manager-component.md) to configure the core device to write to CloudWatch Logs\. For more information, see [Enable logging to CloudWatch Logs](#enable-cloudwatch-logs)\. Then, you can view logs on the **Logs** page of the Amazon CloudWatch console or using the CloudWatch Logs API\.<a name="log-manager-log-group-stream-format"></a>

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

<a name="log-manager-considerations-intro"></a>The following considerations apply when you use the log manager component to write to CloudWatch Logs:<a name="log-manager-considerations"></a>
+ **Log delays**

  The log manager component writes logs from only rotated log files\. By default, the AWS IoT Greengrass Core software rotates log files every hour or after they are 1,024 KB\. As a result, the log manager component uploads logs only after the AWS IoT Greengrass Core software or a Greengrass component writes over 1,024 KB worth of logs\. You can configure a lower log file size limit to cause log files to rotate more often\. This causes the log manager component to upload logs to CloudWatch Logs more frequently\.

  The log manager component also uploads new logs periodically\. By default, the log manager component uploads new logs every 5 minutes\. You can configure a lower upload interval, so the log manager component uploads logs to CloudWatch Logs more frequently\.

  If you need to observe logs in real time, consider using [file system logs](#access-local-logs)\.
+ **Clock skew**

  The log manager component uses the standard Signature Version 4 signing process to create API requests to CloudWatch Logs\. If the system time on a core device is out of sync by more than 15 minutes, then CloudWatch Logs rejects the requests\. For more information, see [Signature Version 4 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\.

## Access system service logs<a name="access-system-service-logs"></a>

If you [configure the AWS IoT Greengrass Core software as a system service](configure-greengrass-core-v2.md#configure-system-service), you can view system service logs to troubleshoot issues, such as the software failing to start\.

**To view system service logs \(CLI\)**
+ Run the following command to view AWS IoT Greengrass Core software system service logs\.

------
#### [ Linux or Unix \(systemd\) ]

  ```
  sudo journalctl -u greengrass.service
  ```

------
#### [ Windows Command Prompt \(CMD\) ]

  ```
  type C:\greengrass\v2\logs\greengrass.wrapper.log
  ```

------
#### [ PowerShell ]

  ```
  gc C:\greengrass\v2\logs\greengrass.wrapper.log
  ```

------

On Windows devices, you can also use the **Event Viewer** application to view system service logs\.

**To view Windows service logs \(Event Viewer\)**

1. Open the **Event Viewer** application\.

1. Select **Windows Logs** to expand it\.

1. Choose **Application** to view application service logs\.

1. Find and open event logs whose **Source** is **greengrass**\.

## Enable logging to CloudWatch Logs<a name="enable-cloudwatch-logs"></a>

You can deploy the [log manager component](log-manager-component.md) to configure a core device to write logs to CloudWatch Logs\. You can enable CloudWatch Logs for AWS IoT Greengrass Core software logs, and you can enable CloudWatch Logs for specific Greengrass components\.

**Note**  
The Greengrass core device's token exchange role must allow the core device to write to CloudWatch Logs, as shown in the following example IAM policy\. If you [installed the AWS IoT Greengrass Core software with automatic resource provisioning](quick-installation.md), your core device has these permissions\.  

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

To configure a core device to write AWS IoT Greengrass Core software logs to CloudWatch Logs, [create a deployment](create-deployments.md) that specifies a configuration update that sets `uploadToCloudWatch` to `true` for the `aws.greengrass.LogManager` component\. AWS IoT Greengrass Core software logs include logs for the [Greengrass nucleus](greengrass-nucleus-component.md) and [plugin components](develop-greengrass-components.md#component-types)\.

```
{
  "logsUploaderConfiguration": {
    "systemLogsConfiguration": {
      "uploadToCloudWatch": "true"
    }
  }
}
```

To configure a core device to write a Greengrass component's logs to CloudWatch Logs, [create a deployment](create-deployments.md) that specifies a configuration update that adds the component to the list of component logging configurations\. When you add a component to this list, the log manager component writes its logs to CloudWatch Logs\. Component logs include logs for [generic components and Lambda components](develop-greengrass-components.md#component-types)\.

```
{
  "logsUploaderConfiguration": {
    "componentLogsConfiguration": [
      {
        "componentName": "com.example.HelloWorld"
      }
    ]
  }
}
```

When you deploy the log manager component, you can also configure disk space limits and whether the core device deletes log files after writing them to CloudWatch Logs\. For more information, see [Configure logging for AWS IoT Greengrass](#configure-logging)\.

## Configure logging for AWS IoT Greengrass<a name="configure-logging"></a>

You can configure the following options to customize logging for Greengrass core devices\. To configure these options, [create a deployment](create-deployments.md) that specifies a configuration update to the Greengrass nucleus or log manager components\.
+ **Writing logs to CloudWatch Logs**

  To remotely troubleshoot core devices, you can configure core devices to write AWS IoT Greengrass Core software and component logs to CloudWatch Logs\. To do so, deploy and configure the [log manager component](log-manager-component.md)\. For more information, see [Enable logging to CloudWatch Logs](#enable-cloudwatch-logs)\.
+ **Deleting uploaded log files**

  To reduce disk space usage, you can configure core devices to delete log files after writing the log files to CloudWatch Logs\. For more information, see the log manager component's `deleteLogFileAfterCloudUpload` parameter, which you can specify for [AWS IoT Greengrass Core software logs](log-manager-component.md#log-manager-component-configuration-system-logs-configuration) and [component logs](log-manager-component.md#log-manager-component-configuration-component-logs-configuration)\.
+ **Log disk space limits**

  To limit disk space usage, you can configure the maximum disk space for each log, including its rotated log files, on a core device\. For example, you can configure the maximum combined disk space for `greengrass.log` and rotated `greengrass.log` files\. For more information, see the Greengrass nucleus component's `logging.totalLogsSizeKB` parameter and the log manager component's `diskSpaceLimit` parameter, which you can specify for [AWS IoT Greengrass Core software logs](log-manager-component.md#log-manager-component-configuration-system-logs-configuration) and [component logs](log-manager-component.md#log-manager-component-configuration-component-logs-configuration)\.
+ **Log file size limit**

  You can configure the maximum file size for each log file\. After a log file exceeds this file size limit, the AWS IoT Greengrass Core software creates a new log file\. The [log manager component](log-manager-component.md) writes only rotated log files to CloudWatch Logs, so you can specify a lower file size limit to write logs to CloudWatch Logs more frequently\. For more information, see the Greengrass nucleus component's [log file size limit parameter](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-log-file-size) \(`logging.fileSizeKB`\)\.
+ **Minimum log levels**

  You can configure the minimum log level that the Greengrass nucleus component writes to file system logs\. For example, you might specify `DEBUG` level logs to help with troubleshooting, or you might specify `ERROR` level logs to reduce the amount of logs that a core device creates\. For more information, see the Greengrass nucleus component's [log level parameter](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-log-level) \(`logging.level`\)\.

  You can also configure the minimum log level that the log manager component writes to CloudWatch Logs\. For example, you might specify a higher log level to reduce [logging costs](http://aws.amazon.com/cloudwatch/pricing/)\. For more information, see the log manager component's `minimumLogLevel` parameter, which you can specify for [AWS IoT Greengrass Core software logs](log-manager-component.md#log-manager-component-configuration-system-logs-configuration) and [component logs](log-manager-component.md#log-manager-component-configuration-component-logs-configuration)\.
+ **Interval to check for logs to write to CloudWatch Logs**

  To increase or decrease how frequently the log manager component writes logs to CloudWatch Logs, you can configure the interval where it checks for new log files to write\. For example, you might specify a lower interval to view logs in CloudWatch Logs sooner than you would with the default 5\-minute interval\. You might specify a higher interval to reduce costs, because the log manager component batches log files into fewer requests\. For more information, see the log manager component's [upload interval parameter](log-manager-component.md#log-manager-component-configuration-periodic-upload-interval-sec) \(`periodicUploadIntervalSec`\)\.
+ **Log format**

  You can choose whether the AWS IoT Greengrass Core software writes logs in text or JSON format\. Choose text format if you read logs, or choose JSON format if you use an application to read or parse logs\. For more information, see the Greengrass nucleus component's [log format parameter](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-logging-format) \(`logging.format`\)\.
+ **Local file system logs folder**

  You can change the logs folder from `/greengrass/v2/logs` to another folder on the core device\. For more information, see the Greengrass nucleus component's [output directory parameter](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration-log-level) \(`logging.outputDirectory`\)\.

## AWS CloudTrail logs<a name="cloudtrail-integration"></a>

AWS IoT Greengrass integrates with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or AWS service in AWS IoT Greengrass\. For more information, see [Log AWS IoT Greengrass V2 API calls with AWS CloudTrail](logging-using-cloudtrail.md)\.