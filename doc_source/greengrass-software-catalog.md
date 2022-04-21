# Community components<a name="greengrass-software-catalog"></a>

The Greengrass Software Catalog is an index of Greengrass components that are developed by the Greengrass community\. From this catalog, you can download, modify, and deploy components to create your Greengrass applications\. You can view the catalog at the following link: [https://github\.com/aws\-greengrass/aws\-greengrass\-software\-catalog](https://github.com/aws-greengrass/aws-greengrass-software-catalog)\.

Each component has a public GitHub repository that you can explore\. View the Greengrass Software Catalog on GitHub to find the full list of community components\. For example, this catalog includes the following components:
+ **InfluxDB**

  This component provides an [InfluxDB](https://www.influxdata.com/products/influxdb/) time\-series database on a Greengrass core device\. You can use this component to process data from IoT sensors, analyze data in real time, and monitor operations at the edge\.
+ **Local Grafana server**

  This component enables you to host a [Grafana](https://grafana.com/) server on a Greengrass core device\. You can use Grafana dashboards to visualize and manage data on the core device\.
+ **Amazon Kinesis Video Streams**

  This component ingests audio and video streams from local cameras that use [Real Time Streaming Protocol \(RTSP\)](https://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol)\. The component then uploads the audio and video streams to [Amazon Kinesis Video Streams](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/what-is-kinesis-video.html)\.
+ **Local web server**

  This component enables you to create a local web user interface on a Greengrass core device\. You can create a local web user interface that enables you to configure device and application settings or monitor the device, for example\.
+ **LoRaWaN protocol adapter**

  This component ingests data from local wireless devices that use the LoRaWaN protocol, which is a Low Power Wide Area Networking \(LPWAN\) protocol\. The component enables you to analyze and act on data locally without communicating with the cloud\.

To request feature or report a bug, open a GitHub issue on the repository for that component\. AWS doesn't provide support for community components\. For more information, see the **CONTRIBUTING\.md** file in each component's repository\.

Several AWS\-provided components are also open source\. For more information, see [Open source AWS IoT Greengrass Core software](open-source.md)\.