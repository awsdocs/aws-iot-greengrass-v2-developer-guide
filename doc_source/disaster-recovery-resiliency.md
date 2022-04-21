# Resilience in AWS IoT Greengrass<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around Amazon Web Services Regions and Availability Zones\. Each AWS Region provides multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information, see [AWS Global Infrastructure](http://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, AWS IoT Greengrass offers several features to help support your data resiliency and backup needs\.
+ You can configure a Greengrass core device to write logs to the local file system and to CloudWatch Logs\. If the core device loses connectivity, it can continue to log messages on the file system\. When it reconnects, it writes the log messages to CloudWatch Logs\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.
+ If a core device loses power during a deployment, it resumes the deployment after the AWS IoT Greengrass Core software starts again\.
+ If a core device loses internet connectivity, Greengrass client devices can continue to communicate over the local network\.
+ You can author Greengrass components that read [stream manager](manage-data-streams.md) streams and send the data to local storage destinations\.