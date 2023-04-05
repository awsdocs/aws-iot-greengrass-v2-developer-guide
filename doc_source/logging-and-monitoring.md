# Logging and monitoring in AWS IoT Greengrass<a name="logging-and-monitoring"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of AWS IoT Greengrass and your AWS solutions\. You should collect monitoring data from all parts of your AWS solution so that you can more easily debug a multi\-point failure, if one occurs\. Before you start monitoring AWS IoT Greengrass, you should create a monitoring plan that includes answers to the following questions:
+ What are your monitoring goals?
+ Which resources will you monitor?
+ How often will you monitor these resources?
+ Which monitoring tools will you use?
+ Who will perform the monitoring tasks?
+ Who should be notified when something goes wrong?

**Topics**
+ [Monitoring tools](#monitoring-tools)
+ [Monitor AWS IoT Greengrass logs](monitor-logs.md)
+ [Log AWS IoT Greengrass V2 API calls with AWS CloudTrail](logging-using-cloudtrail.md)
+ [Gather system health telemetry data from AWS IoT Greengrass core devices](telemetry.md)
+ [Get deployment and component health status notifications](deployment-health-notifications.md)
+ [Check Greengrass core device status](device-status.md)

## Monitoring tools<a name="monitoring-tools"></a>

AWS provides tools that you can use to monitor AWS IoT Greengrass\. You can configure some of these tools to do the monitoring for you\. Some of the tools require manual intervention\. We recommend that you automate monitoring tasks as much as possible\.

You can use the following automated monitoring tools to monitor AWS IoT Greengrass and report issues:
+ **Amazon CloudWatch Logs** – Monitor, store, and access your log files from AWS CloudTrail or other sources\. For more information, see [Monitoring log files](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchLogs.html) in the *Amazon CloudWatch User Guide*\.
+ **AWS CloudTrail Log Monitoring** – Share log files between accounts, monitor CloudTrail log files in real time by sending them to CloudWatch Logs, write log processing applications in Java, and validate that your log files have not changed after delivery by CloudTrail\. For more information, see [Working with CloudTrail log files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-working-with-log-files.html) in the *AWS CloudTrail User Guide*\.
+ **Greengrass system health telemetry** – Subscribe to receive telemetry data sent from the Greengrass core\. For more information, see [Gather system health telemetry data from AWS IoT Greengrass core devices](telemetry.md)\.
+ **Device health notifications** Create events using Amazon EventBridge to receive status updates regarding deployments and components\. For more information, see [Get deployment and component health status notifications](deployment-health-notifications.md)\.
+ **Fleet status service** – Use the fleet status API operations to check the status of core devices and their Greengrass components\. You can also view fleet status information in the AWS IoT Greengrass console\. For more information, see [Check Greengrass core device status](device-status.md)\.