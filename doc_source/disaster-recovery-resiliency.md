# Resilience in AWS IoT Greengrass<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around Amazon Web Services Regions and Availability Zones\. Each AWS Region provides multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information, see [AWS Global Infrastructure](http://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, AWS IoT Greengrass offers several features to help support your data resiliency and backup needs\.
+ If the core loses internet connectivity, Greengrass devices can continue to communicate over the local network\.
+ You can configure a Greengrass core device to write logs to the local file system and to CloudWatch Logs\. If the core loses connectivity, local logging can continue, but CloudWatch logs are sent with a limited number of retries\. After the retries are exhausted, the event is dropped\.  For more information, see the [log manager component](log-manager-component.md)\.
+ You can author Greengrass components that read [stream manager](manage-data-streams.md) streams and send the data to local storage destinations\.