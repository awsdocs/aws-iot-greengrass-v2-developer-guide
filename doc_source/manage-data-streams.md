# Manage data streams on the AWS IoT Greengrass Core<a name="manage-data-streams"></a>

AWS IoT Greengrass stream manager makes it more efficient and reliable to transfer high\-volume IoT data to the AWS Cloud\. Stream manager processes data streams on the AWS IoT Greengrass Core before it exports them to the AWS Cloud\. Stream manager integrates with common edge scenarios, such as machine learning \(ML\) inference, where the AWS IoT Greengrass Core device processes and analyzes data before it exports the data to the AWS Cloud or local storage destinations\.

Stream manager provides a common interface to simplify custom component development so that you don't need to build custom stream management functionality\. Your components can use a standardized mechanism to process high\-volume streams and manage local data retention policies\. You can define policies for storage type, size, and data retention for each stream to control how stream manager processes and exports data\.

Stream manager works in environments with intermittent or limited connectivity\. You can define bandwidth use, timeout behavior, and how the AWS IoT Greengrass Core handles stream data when it is connected or disconnected\. You can also set priorities to control the order in which the AWS IoT Greengrass Core exports streams to the AWS Cloud\. This makes it possible for you to handle critical data sooner than other data\.

You can configure stream manager to automatically export data to the AWS Cloud for storage or further processing and analysis\. Stream manager supports exports to the following AWS Cloud destinations:
+ Channels in AWS IoT Analytics\. AWS IoT Analytics lets you perform advanced analysis on your data to help make business decisions and improve machine learning models\. For more information, see [What is AWS IoT Analytics?](https://docs.aws.amazon.com/iotanalytics/latest/userguide/welcome.html) in the *AWS IoT Analytics User Guide*\.
+ Streams in Amazon Kinesis Data Streams\. You can use Kinesis Data Streams to aggregate high\-volume data and load it into a data warehouse or MapReduce cluster\. For more information, see [What is Amazon Kinesis Data Streams?](https://docs.aws.amazon.com/streams/latest/dev/what-is-this-service.html) in the *Amazon Kinesis Data Streams Developer Guide*\.
+ Asset properties in AWS IoT SiteWise\. AWS IoT SiteWise lets you collect, organize, and analyze data from industrial equipment at scale\. For more information, see [What is AWS IoT SiteWise?](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/what-is-sitewise.html) in the *AWS IoT SiteWise User Guide*\.
+ Objects in Amazon Simple Storage Service Amazon S3\. You can use Amazon S3 to store and retrieve large amounts of data\. For more information, see [What is Amazon S3?](https://docs.aws.amazon.com/AmazonS3/latest/dev/Welcome.html) in the *Amazon Simple Storage Service Developer Guide*\.

## Stream management workflow<a name="stream-manager-workflow"></a>

Your IoT applications interact with stream manager through the Stream Manager SDK\.

In a simple workflow, a component on the AWS IoT Greengrass core consumes IoT data, such as time\-series temperature and pressure metrics\. The component might filter or compress the data, and then call the Stream Manager SDK to write the data to a stream in stream manager\. Stream manager can export the stream to the AWS Cloud automatically based on the policies that you define for the stream\. Components can also send data directly to local databases or storage repositories\.

Your IoT applications can include multiple custom components that read or write to streams\. These components can read and write to streams to filter, aggregate, and analyze data on the AWS IoT Greengrass core device\. This makes it possible to respond quickly to local events and extract valuable information before the data transfers from the core to the AWS Cloud or local destinations\.

To get started, deploy the stream manager component to your AWS IoT Greengrass core device\. In the deployment, configure the stream manager component parameters to define settings that apply to all streams on the Greengrass core device\. Use these parameters to control how stream manager stores, processes, and exports streams based on your business needs and environment constraints\. 

After you configure stream manager, you can create and deploy your IoT applications\. These are typically custom components that use `StreamManagerClient` in the Stream Manager SDK to create and interact with streams\. When you create a stream, you can define per\-stream policies, such as export destinations, priority, and persistence\. 

## Requirements<a name="stream-manager-requirements"></a>

The following requirements apply for using stream manager:
+ Stream manager requires a minimum of 70 MB RAM in addition to the AWS IoT Greengrass Core software\. Your total memory requirement depends on your workload\.
+ AWS IoT Greengrass components must use the Stream Manager SDK to interact with stream manager\. The Stream Manager SDK is available in the following languages :<a name="stream-manager-sdk-download-list"></a>
  + [Stream Manager SDK for Java](https://github.com/aws-greengrass/aws-greengrass-stream-manager-sdk-java/) \(v1\.1\.0 or later\)
  + [Stream Manager SDK for Node\.js](https://github.com/aws-greengrass/aws-greengrass-stream-manager-sdk-js/) \(v1\.1\.0 or later\)
  + [Stream Manager SDK for Python](https://github.com/aws-greengrass/aws-greengrass-stream-manager-sdk-python/) \(v1\.1\.0 or later\)
+ AWS IoT Greengrass components must specify the stream manager component \(`aws.greengrass.StreamManager`\) as a dependency in their recipe to use stream manager\.
**Note**  <a name="stream-manager-upgrade-note"></a>
If you use stream manager to export data to the cloud, you can't upgrade version 2\.0\.7 of the stream manager component to a version between v2\.0\.8 and v2\.0\.11\. If you are deploying stream manager for the first time, we strongly recommend that you deploy the latest version of the stream manager component\.
+ If you define AWS Cloud export destinations for a stream, you must create your export targets and grant access permissions in the [Greengrass device role](device-service-role.md)\. Depending on the destination, other requirements might also apply\. For more information, see:<a name="export-destinations-links"></a>
  + [AWS IoT Analytics channels](stream-export-configurations.md#export-to-iot-analytics)
  + [Amazon Kinesis data streams](stream-export-configurations.md#export-to-kinesis)
  + [AWS IoT SiteWise asset properties](stream-export-configurations.md#export-to-iot-sitewise)
  + [Amazon S3 objects](stream-export-configurations.md#export-to-s3)

  You are responsible for maintaining these AWS Cloud resources\.

## Data security<a name="stream-manager-security"></a>

When you use stream manager, be aware of the following security considerations\.

### Local data security<a name="stream-manager-security-stream-data"></a>

AWS IoT Greengrass does not encrypt stream data at rest or in transit between local components on the core device\.
+ **Data at rest**\. Stream data is stored locally in a storage directory\. For data security, AWS IoT Greengrass relies on file permissions and full\-disk encryption, if enabled\. You can use the optional [STREAM\_MANAGER\_STORE\_ROOT\_DIR](configure-stream-manager.md#STREAM_MANAGER_STORE_ROOT_DIR) parameter to specify the storage directory\. If you change this parameter later to use a different storage directory, AWS IoT Greengrass does not delete the previous storage directory or its contents\.
+ **Data in transit locally**\. AWS IoT Greengrass does not encrypt stream data in local transit between data sources, AWS IoT Greengrass components, the Stream Manager SDK, and stream manager\.
+ **Data in transit to the AWS Cloud**\. Data streams exported by stream manager to the AWS Cloud use standard AWS service client encryption with Transport Layer Security \(TLS\)\.

### Client authentication<a name="stream-manager-security-client-authentication"></a>

Stream manager clients use the Stream Manager SDK to communicate with stream manager\. When client authentication is enabled, only Greengrass components can interact with streams in stream manager\. When client authentication is disabled, any process running on the Greengrass core device  can interact with streams in stream manager\. You should disable authentication only if your business case requires it\.

You use the [STREAM\_MANAGER\_AUTHENTICATE\_CLIENT](configure-stream-manager.md#STREAM_MANAGER_AUTHENTICATE_CLIENT) parameter to set the client authentication mode\. You can configure this parameter when you deploy the stream manager component to core devices\.


****  

|   | Enabled | Disabled | 
| --- | --- | --- | 
| Parameter value | `true` \(default and recommended\) | `false` | 
| Allowed clients | Greengrass components on the core device | Greengrass components on the core device Other processes running on the Greengrass core device | 

## See also<a name="stream-manager-see-also"></a>
+ [Configure AWS IoT Greengrass stream manager](configure-stream-manager.md)
+ [Use StreamManagerClient to work with streams](work-with-streams.md)
+ [Export configurations for supported AWS Cloud destinations](stream-export-configurations.md)