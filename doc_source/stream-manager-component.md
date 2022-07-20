# Stream manager<a name="stream-manager-component"></a>

The stream manager component \(`aws.greengrass.StreamManager`\) enables you to process data streams to transfer to the AWS Cloud from Greengrass core devices\.

For more information about how to configure and use stream manager in custom components, see [Manage data streams on Greengrass core devices](manage-data-streams.md)\.

**Topics**
+ [Versions](#stream-manager-component-versions)
+ [Type](#stream-manager-component-type)
+ [Operating system](#stream-manager-component-os-support)
+ [Requirements](#stream-manager-component-requirements)
+ [Dependencies](#stream-manager-component-dependencies)
+ [Configuration](#stream-manager-component-configuration)
+ [Local log file](#stream-manager-component-log-file)
+ [Changelog](#stream-manager-component-changelog)

## Versions<a name="stream-manager-component-versions"></a>

This component has the following versions:
+ 2\.0\.x

**Note**  <a name="stream-manager-upgrade-note"></a>
If you use stream manager to export data to the cloud, you can't upgrade version 2\.0\.7 of the stream manager component to a version between v2\.0\.8 and v2\.0\.11\. If you are deploying stream manager for the first time, we strongly recommend that you deploy the latest version of the stream manager component\.

## Type<a name="stream-manager-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="stream-manager-component-os-support"></a>

This component can be installed on core devices that run the following operating systems:
+ Linux
+ Windows

## Requirements<a name="stream-manager-component-requirements"></a>

This component has the following requirements:
+ The [token exchange role](device-service-role.md) must allow access to the AWS Cloud destinations that you use with stream manager\. For more information, see:<a name="export-destinations-links"></a>
  + [AWS IoT Analytics channels](stream-export-configurations.md#export-to-iot-analytics)
  + [Amazon Kinesis data streams](stream-export-configurations.md#export-to-kinesis)
  + [AWS IoT SiteWise asset properties](stream-export-configurations.md#export-to-iot-sitewise)
  + [Amazon S3 objects](stream-export-configurations.md#export-to-s3)

### Endpoints and ports<a name="stream-manager-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `iotanalytics.region.amazonaws.com`  | 443 | No |  Required if you publish data to AWS IoT Analytics\.  | 
|  `kinesis.region.amazonaws.com`  | 443 | No |  Required if you publish data to Kinesis Data Firehose\.  | 
|  `data.iotsitewise.region.amazonaws.com`  | 443 | No |  Required if you publish data to AWS IoT SiteWise\.  | 
|  `*.s3.amazonaws.com`  | 443 | No |  Required if you publish data to S3 buckets\. You can replace `*` with the name of each bucket where you publish data\.  | 

## Dependencies<a name="stream-manager-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#stream-manager-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

------
#### [ 2\.0\.15 ]

The following table lists the dependencies for version 2\.0\.15 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.0 <2\.7\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) |  >=0\.0\.0  | Hard | 

------
#### [ 2\.0\.13 and 2\.0\.14 ]

The following table lists the dependencies for versions 2\.0\.13 and 2\.0\.14 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.0 <2\.6\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) |  >=0\.0\.0  | Hard | 

------
#### [ 2\.0\.11 and 2\.0\.12 ]

The following table lists the dependencies for versions 2\.0\.11 and 2\.0\.12 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.0 <2\.5\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) |  >=0\.0\.0  | Hard | 

------
#### [ 2\.0\.10 ]

The following table lists the dependencies for version 2\.0\.10 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.0 <2\.4\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) |  >=0\.0\.0  | Hard | 

------
#### [ 2\.0\.9 ]

The following table lists the dependencies for version 2\.0\.9 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.0 <2\.3\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) |  >=0\.0\.0  | Hard | 

------
#### [ 2\.0\.8 ]

The following table lists the dependencies for version 2\.0\.8 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.0 <2\.2\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) |  >=0\.0\.0  | Hard | 

------
#### [ 2\.0\.7 ]

The following table lists the dependencies for version 2\.0\.7 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) |  >=2\.0\.3 <2\.1\.0  | Soft | 
| [Token exchange service](token-exchange-service-component.md) |  >=0\.0\.0  | Hard | 

------

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="stream-manager-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`STREAM_MANAGER_STORE_ROOT_DIR`  
\(Optional\) The absolute path of the local directory used to store streams\. This value must start with a forward slash \(for example, `/data`\)\.  
<a name="stream-manager-store-root-dir-parameter-folder-requirements"></a>You must specify an existing folder, and the [system user who runs the stream manager component](configure-greengrass-core-v2.md#configure-component-user) must have permissions to read and write to this folder\. For example, you can run the following commands to create and configure a folder, `/var/greengrass/streams`, which you specify as the stream manager root folder\. These commands allow the default system user, `ggc_user`, to read and write to this folder\.  

```
sudo mkdir /var/greengrass/streams
sudo chown ggc_user /var/greengrass/streams
sudo chmod 700 /var/greengrass/streams
```
Default: `/greengrass/v2/work/aws.greengrass.StreamManager`

`STREAM_MANAGER_SERVER_PORT`  
\(Optional\) The local port number to use to communicate with stream manager\.  
You can specify `0` to use a random available port\.  
Default: `8088`

`STREAM_MANAGER_AUTHENTICATE_CLIENT`  
\(Optional\) You can make it mandatory for clients to authenticate before they can interact with stream manager\. The Stream Manager SDK controls interaction between clients and stream manager\. This parameter determines which clients can call the Stream Manager SDK to work with streams\. For more information, see [stream manager client authentication](manage-data-streams.md#stream-manager-security-client-authentication)\.  
If you specify `true`, the Stream Manager SDK allows only Greengrass components as clients\.  
If you specify `false`, the Stream Manager SDK allows all processes on the core device to be clients\.  
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

`LOG_LEVEL`  
\(Optional\) The logging level for the component\. Choose from the following log levels, listed here in level order:  
+ `TRACE`
+ `DEBUG`
+ `INFO`
+ `WARN`
+ `ERROR`
Default: `INFO`

`JVM_ARGS`  
\(Optional\) The custom Java Virtual Machine arguments to pass to stream manager at startup\. Separate multiple arguments by spaces\.  
Use this parameter only when you must override the default settings used by the JVM\. For example, you might need to increase the default heap size if you plan to export a large number of streams\.

**Example: Configuration merge update**  
The following example configuration specifies to use a non\-default port\.  

```
{
  "STREAM_MANAGER_SERVER_PORT": "18088"
}
```

## Local log file<a name="stream-manager-component-log-file"></a>

This component uses the following log file\.

------
#### [ Linux ]

```
/greengrass/v2/logs/aws.greengrass.StreamManager.log
```

------
#### [ Windows ]

```
C:\greengrass\v2\logs\aws.greengrass.StreamManager.log
```

------

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* or *C:\\greengrass\\v2* with the path to the AWS IoT Greengrass root folder\.

------
#### [ Linux ]

  ```
  sudo tail -f /greengrass/v2/logs/aws.greengrass.StreamManager.log
  ```

------
#### [ Windows \(PowerShell\) ]

  ```
  Get-Content C:\greengrass\v2\logs\aws.greengrass.StreamManager.log -Tail 10 -Wait
  ```

------

## Changelog<a name="stream-manager-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.0\.15  |  Version updated for Greengrass nucleus version 2\.6\.0 release\.  | 
|  2\.0\.14  |  This version contains bug fixes and improvements\.  | 
|  2\.0\.13  |  Version updated for Greengrass nucleus version 2\.5\.0 release\.  | 
| 2\.0\.12 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/stream-manager-component.html)  | 
|  2\.0\.11  |  Version updated for Greengrass nucleus version 2\.4\.0 release\.  | 
|  2\.0\.10  |  Version updated for Greengrass nucleus version 2\.3\.0 release\.  | 
|  2\.0\.9  |  Version updated for Greengrass nucleus version 2\.2\.0 release\.  | 
|  2\.0\.8  |  Version updated for Greengrass nucleus version 2\.1\.0 release\.  | 
|  2\.0\.7  |  Initial version\.  | 