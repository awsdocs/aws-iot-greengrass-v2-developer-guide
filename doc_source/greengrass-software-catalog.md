# Community components<a name="greengrass-software-catalog"></a>

The Greengrass Software Catalog is an index of Greengrass components that are developed by the Greengrass community\. From this catalog, you can download, modify, and deploy components to create your Greengrass applications\. You can view the catalog at the following link: [https://github\.com/aws\-greengrass/aws\-greengrass\-software\-catalog](https://github.com/aws-greengrass/aws-greengrass-software-catalog)\.

Each component has a public GitHub repository that you can explore\. View the Greengrass Software Catalog on GitHub to find the full list of community components\. For example, this catalog includes the following components:
+ **[Amazon Kinesis Video Streams](https://github.com/awslabs/aws-greengrass-labs-kvs-stream-uploader)**

  This component ingests audio and video streams from local cameras that use [Real Time Streaming Protocol \(RTSP\)](https://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol)\. The component then uploads the audio and video streams to [Amazon Kinesis Video Streams](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/what-is-kinesis-video.html)\.
+ **[ Modbus TCP](https://github.com/awslabs/aws-greengrass-labs-modbus-tcp-protocol-adapter)**

  This component collects data from local devices using the ModbusTCP protocol and publishes it to selected data streams\.
+ **[Bluetooth IoT gateway](https://github.com/awslabs/aws-greengrass-labs-bluetooth-gateway)**

  This component uses the [BluePy](https://ianharvey.github.io/bluepy-doc/index.html) library that enables communication with Bluetooth Low Energy \(LE\) devices to create Bluetooth LE client interfaces\.
+ **[Home assistant](https://github.com/awslabs/aws-greengrass-labs-component-for-home-assistant)**

  This component enables the customer to use [Home Assistant](https://www.home-assistant.io/) to provide local control of smart home devices\. It provides integration with AWS services at the edge and in the cloud to deliver home automation solutions that extend Home Assistant\.
+ **[InfluxDBGrafana dashboard](https://github.com/awslabs/aws-greengrass-labs-dashboard-influxdb-grafana)**

  This component provides a one\-click experience to set up the InfluxDB and Grafana components\. It connects InfluxDB to Grafana and automates the setup of a local Grafana dashboard that renders AWS IoT Greengrass telemetry in real time\.
+ **[InfluxDB](https://github.com/awslabs/aws-greengrass-labs-database-influxdb)**

  This component provides an [InfluxDB](https://www.influxdata.com/products/influxdb/) time\-series database on a Greengrass core device\. You can use this component to process data from IoT sensors, analyze data in real time, and monitor operations at the edge\.
+ **[InfluxDB publisher](https://github.com/awslabs/aws-greengrass-labs-telemetry-influxdbpublisher)**

  This component relays AWS IoT Greengrass system health telemetry from the [Nucleus emitter plugin](https://github.com/aws-greengrass/aws-greengrass-telemetry-nucleus-emitter) to InfluxDB\. This component can also forward custom telemetry to InfluxDB\.
+ **[Grafana](https://github.com/awslabs/aws-greengrass-labs-dashboard-grafana)**

  This component enables you to host a [Grafana](https://grafana.com/) server on a Greengrass core device\. You can use Grafana dashboards to visualize and manage data on the core device\.
+ **[IoT pubsub framework](https://github.com/aws-samples/aws-greengrass-application-framework)**

  This framework provides an application architecture, template code, and deployable examples that help improve code quality for distributed event\-driven IoT pubsub applications using AWS IoT Greengrass v2 custom components\. For more information, see [Create AWS IoT Greengrass components](create-components.md)\.
+ **[Jupyter Labs](https://github.com/awslabs/aws-greengrass-labs-jupyterlab)**

  This component deploys JupyterLab to an AWS IoT Greengrass core device\. The Jupyter environment has access to the process and environment variable resources set by AWS IoT Greengrass, simplifying the process of testing and developing components written in Python\.
+ **[Local web server](https://github.com/awslabs/aws-greengrass-labs-local-web-server)**

  This component enables you to create a local web user interface on a Greengrass core device\. You can create a local web user interface that enables you to configure device and application settings or monitor the device, for example\.
+ **[LoRaWaN protocol adapter](https://github.com/awslabs/aws-greengrass-labs-component-for-the-things-stack-lorawan)**

  This component ingests data from local wireless devices that use the LoRaWaN protocol, which is a low\-power wide\-area network \(LPWAN\) protocol\. The component enables you to analyze and act on data locally without communicating with the cloud\.
+ **[TES routing to container](https://github.com/awslabs/aws-greengrass-labs-tes-router)**

  This component configures nftables or iptables on an AWS IoT Greengrass device so that it can use the [Token exchange service](token-exchange-service-component.md) component with containers\.
+ **[WebRTC](https://github.com/awslabs/aws-greengrass-labs-webrtc)**

  This component ingests audio and video streams from RTSP cameras connected to the AWS IoT Greengrass core device\. And then the component turns the audio and video streams into peer\-to\-peer communication or relay through Amazon Kinesis Video Streams\.
+ **[GStreamer for Amazon Lookout for Vision](https://github.com/awslabs/aws-greengrass-labs-lookoutvision-gstreamer)**

  This component provides a GStreamer plugin so that you can perform Lookout for Vision anomaly detection in your custom GStreamer pipelines\.
+ **[PostgreSQL DB](https://github.com/awslabs/aws-greengrass-labs-database-postgresql)**

  This component provides support for [PostgreSQL](https://www.postgresql.org/) relational database at the edge\. Customers can use this component to provision and manage a local PostgreSQL instance inside a docker container\.
+ **[Secrets Manager client](https://github.com/awslabs/aws-greengrass-labs-secretsmanagerclient)**

  This component provides a CLI tool that can be used by other components needing to retrieve secrets from the Secrets Manager component in a recipe lifecycle script\.
+ **[Containerized secure tunneling](https://github.com/awslabs/aws-greengrass-labs-containerized-secure-tunneling)**

  This component provides a Docker container for secure tunneling with all dependencies and matching libraries in a reusable recipe that doesn't rely on a specific host operating system\.
+ **[S3 file uploader](https://github.com/awslabs/aws-greengrass-labs-s3-file-uploader)**

  This component monitors a directory for new files, uploads them to Amazon Simple Storage Service \(Amazon S3\), and then deletes them after a successful upload\.
+ **[Node\-RED](https://github.com/awslabs/aws-greengrass-labs-nodered)**

  This component installs Node\-RED on an AWS IoT Greengrass core device using NPM\. The component depends on the [Node\-RED Auth](https://github.com/awslabs/aws-greengrass-labs-nodered-auth) component which must be explicitly deployed and configured\. You can use the [Node\-RED CLI for Greengrass](https://github.com/awslabs/aws-greengrass-labs-node-red-app-cli) to deploy Node\-RED flows to AWS IoT Greengrass devices\.
+ **[Node\-RED Docker](https://github.com/awslabs/aws-greengrass-labs-nodered-docker)**

  This component installs Node\-RED on the AWS IoT Greengrass core device using the official Node\-RED Docker container\. The component depends on the [Node\-RED Auth](https://github.com/awslabs/aws-greengrass-labs-nodered-auth) component which must be explicitly deployed and configured\. You can use the [Node\-RED CLI for Greengrass](https://github.com/awslabs/aws-greengrass-labs-node-red-app-cli) to deploy Node\-RED flows to AWS IoT Greengrass devices\.
+ **[Node\-RED Auth](https://github.com/awslabs/aws-greengrass-labs-nodered-auth)**

  This component configures a user name and password to secure the Node\-RED instance running on an AWS IoT Greengrass core device\.

To request a feature or report a bug, open a GitHub issue on the repository for that component\. AWS doesn't provide support for community components\. For more information, see the **CONTRIBUTING\.md** file in each component's repository\.

Several AWS\-provided components are also open source\. For more information, see [Open source AWS IoT Greengrass Core software](open-source.md)\.