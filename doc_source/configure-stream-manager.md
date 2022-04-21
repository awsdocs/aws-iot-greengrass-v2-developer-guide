# Configure AWS IoT Greengrass stream manager<a name="configure-stream-manager"></a>

On Greengrass core devices, stream manager can store, process, and export IoT device data\. Stream manager provides parameters that you use to configure runtime settings\. These settings apply to all streams on the Greengrass core device\. You can use the AWS IoT Greengrass console or API to configure stream manager settings when you deploy the component\. Changes take effect after the deployment completes\.

## Stream manager parameters<a name="stream-manager-parameters"></a>

Stream manager provides the following parameters that you can configure when you deploy the component to your core devices\. All parameters are optional\.

**Storage directory**  <a name="STREAM_MANAGER_STORE_ROOT_DIR"></a>
Parameter name: `STREAM_MANAGER_STORE_ROOT_DIR`  
The absolute path of the local folder used to store streams\. This value must start with a forward slash \(for example, `/data`\)\.  
<a name="stream-manager-store-root-dir-parameter-folder-requirements"></a>You must specify an existing folder, and the [system user who runs the stream manager component](configure-greengrass-core-v2.md#configure-component-user) must have permissions to read and write to this folder\. For example, you can run the following commands to create and configure a folder, `/var/greengrass/streams`, which you specify as the stream manager root folder\. These commands allow the default system user, `ggc_user`, to read and write to this folder\.  

```
sudo mkdir /var/greengrass/streams
sudo chown ggc_user /var/greengrass/streams
sudo chmod 700 /var/greengrass/streams
```
For information about securing stream data, see [Local data security](manage-data-streams.md#stream-manager-security-stream-data)\.  
Default: `/greengrass/v2/work/aws.greengrass.StreamManager`

**Server port**  
Parameter name: `STREAM_MANAGER_SERVER_PORT`  
The local port number used to communicate with stream manager\. The default is `8088`\.  
You can specify `0` to use a random available port\.

**Authenticate client**  <a name="STREAM_MANAGER_AUTHENTICATE_CLIENT"></a>
Parameter name: `STREAM_MANAGER_AUTHENTICATE_CLIENT`  
Indicates whether clients must be authenticated to interact with stream manager\. All interaction between clients and stream manager is controlled by the Stream Manager SDK\. This parameter determines which clients can call the Stream Manager SDK to work with streams\. For more information, see [Client authentication](manage-data-streams.md#stream-manager-security-client-authentication)\.  
Valid values are `true` or `false`\. The default is `true` \(recommended\)\.  
+ `true`\. Allows only Greengrass components as clients\. Components use internal AWS IoT Greengrass Core protocols to authenticate with the Stream Manager SDK\.
+ `false`\. Allows any process that runs on the AWS IoT Greengrass Core to be a client\. Do not set the value to `false` unless your business case requires it\. For example, use `false` only if non\-component processes on the core device must communicate directly with stream manager\.

**Maximum bandwidth**  
Parameter name: `STREAM_MANAGER_EXPORTER_MAX_BANDWIDTH`  
The average maximum bandwidth \(in kilobits per second\) that can be used to export data\. The default allows unlimited use of available bandwidth\.

**Thread pool size**  
Parameter name: `STREAM_MANAGER_EXPORTER_THREAD_POOL_SIZE`  
The maximum number of active threads that can be used to export data\. The default is `5`\.  
The optimal size depends on your hardware, stream volume, and planned number of export streams\. If your export speed is slow, you can adjust this setting to find the optimal size for your hardware and business case\. The CPU and memory of your core device hardware are limiting factors\. To start, you might try setting this value equal to the number of processor cores on the device\.  
Be careful not to set a size that's higher than your hardware can support\. Each stream consumes hardware resources, so try to limit the number of export streams on constrained devices\.

**JVM arguments**  
Parameter name: `JVM_ARGS`  
Custom Java Virtual Machine arguments to pass to stream manager at startup\. Multiple arguments should be separated by spaces\.  
Use this parameter only when you must override the default settings used by the JVM\. For example, you might need to increase the default heap size if you plan to export a large number of streams\.

**Logging level**  
Parameter name: `LOG_LEVEL`  
The logging configuration for the component\. Choose from the following log levels, listed here in level order:  
+ `DEBUG`
+ `INFO`
+ `WARN`
+ `ERROR`
Default: `INFO`

**Minimum size for multipart upload**  <a name="stream-manager-minimum-part-size"></a>
Parameter name: `STREAM_MANAGER_EXPORTER_S3_DESTINATION_MULTIPART_UPLOAD_MIN_PART_SIZE_BYTES`  
The minimum size \(in bytes\) of a part in a multipart upload to Amazon S3\. Stream manager uses this setting and the size of the input file to determine how to batch data in a multipart PUT request\. The default and minimum value is `5242880` bytes \(5 MB\)\.  
Stream manager uses the stream's `sizeThresholdForMultipartUploadBytes` property to determine whether to export to Amazon S3 as a single or multipart upload\. User\-defined Greengrass components set this threshold when they create a stream that exports to Amazon S3\. The default threshold is 5 MB\.

## See also<a name="configure-stream-manager-see-also"></a>
+ [Manage data streams on the AWS IoT Greengrass Core](manage-data-streams.md)
+ [Use StreamManagerClient to work with streams](work-with-streams.md)
+ [Export configurations for supported AWS Cloud destinations](stream-export-configurations.md)