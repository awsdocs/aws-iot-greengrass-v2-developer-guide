# Stream manager<a name="stream-manager-component"></a>

The stream manager component \(`aws.greengrass.StreamManager`\) enables you to process data streams to transfer to the AWS Cloud from Greengrass core devices\.

For more information about how to configure and use stream manager in custom components, see [Manage data streams on the AWS IoT Greengrass Core](manage-data-streams.md)\.

This component has the following versions:
+ 2\.0\.x

## Configuration<a name="stream-mananger-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`STREAM_MANAGER_STORE_ROOT_DIR`  
\(Optional\) The absolute path of the local directory used to store streams\. This value must start with a forward slash \(for example, `/data`\)\.

`STREAM_MANAGER_SERVER_PORT`  
\(Optional\) The local port number to use to communicate with stream manager\.  
Default: `8088`

`STREAM_MANAGER_AUTHENTICATE_CLIENT`  
\(Optional\) You can make it mandatory for clients to authenticate before they can interact with stream manager\. The AWS IoT Greengrass Core SDK controls interaction between clients and stream manager\. This parameter determines which clients can call the AWS IoT Greengrass Core SDK to work with streams\. For more information, see [stream manager client authentication](manage-data-streams.md#stream-manager-security-client-authentication)\.  
If you specify `true`, the AWS IoT Greengrass Core SDK allows only Greengrass components as clients\.  
If you specify `false`, the AWS IoT Greengrass Core SDK allows all processes on the core device to be clients\.  
Default: `true`

`STREAM_MANAGER_EXPORTER_MAX_BANDWIDTH`  
\(Optional\) The average maximum bandwidth \(in kilobits per second\) that stream manager can use to export data\.  
Default: No limit

`STREAM_MANAGER_THREAD_POOL_SIZE`  
\(Optional\) The maximum number of active threads that stream manager can use to export data\.  
The optimal size depends on your hardware, stream volume, and planned number of export streams\. If your export speed is slow, you can adjust this setting to find the optimal size for your hardware and business case\. The CPU and memory of your core device hardware are limiting factors\. To start, you might try setting this value equal to the number of processor cores on the device\.  
Be careful not to set a size that's higher than your hardware can support\. Each stream consumes hardware resources, so try to limit the number of export streams on constrained devices\.  
Default: 5 threads

`STREAM_MANAGER_EXPORTER_S3_DESTINATION_MULTIPART_UPLOAD_MIN_PART_SIZE_BYTES`  
\(Optional\) The minimum size \(in bytes\) of a part in a multipart upload to Amazon S3\. Stream manager uses this setting and the size of the input file to determine how to batch data in a multipart PUT request\.  
Stream manager uses the streams `sizeThresholdForMultipartUploadBytes` property to determine whether to export to Amazon S3 as a single or multipart upload\. AWS IoT Greengrass components can set this threshold when they create a stream that exports to Amazon S3\.
Default: `5242880` \(5 MB\)\. This is also the minimum value\.

`JVM_ARGS`  
\(Optional\) The custom Java Virtual Machine arguments to pass to stream manager at startup\. Separate multiple arguments by spaces\.  
Use this parameter only when you must override the default settings used by the JVM\. For example, you might need to increase the default heap size if you plan to export a large number of streams\.