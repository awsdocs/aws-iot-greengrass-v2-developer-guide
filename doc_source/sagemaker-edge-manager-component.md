# SageMaker Edge Manager<a name="sagemaker-edge-manager-component"></a>

The Amazon SageMaker Edge Manager component \(`aws.greengrass.SageMakerEdgeManager`\) installs the SageMaker Edge Manager agent binary\. 

SageMaker Edge Manager provides model management for edge devices so you can optimize, secure, monitor, and maintain machine learning models on fleets of edge devices\. The SageMaker Edge Manager component installs and manages the lifecycle of the SageMaker Edge Manager agent on your core device\. You can also use SageMaker Edge Manager to package and use SageMaker Neo\-compiled models as model components on Greengrass core devices\. For more information about using SageMaker Edge Manager agent on your core device, see [Use Amazon SageMaker Edge Manager on Greengrass core devices](use-sagemaker-edge-manager.md)\.

SageMaker Edge Manager component v1\.0\.0 installs Edge Manager agent binary v1\.20210512\.96da6cc\. For more information about Edge Manager agent binary versions, see [Edge Manager Agent](https://docs.aws.amazon.com/sagemaker/latest/dg/edge-device-fleet-about)\.

**Note**  
The SageMaker Edge Manager component is available only in the following AWS Regions:  
US East \(Ohio\)
US East \(N\. Virginia\)
US West \(Oregon\)
EU \(Frankfurt\)
EU \(Ireland\)
Asia Pacific \(Tokyo\)

## Versions<a name="sagemaker-edge-manager-component-versions"></a>

This component has the following versions:
+ 1\.0\.x

## Requirements<a name="sagemaker-edge-manager-component-requirements"></a>

To deploy a component, you must meet the requirements for the component and its [dependencies](#sagemaker-edge-manager-component-dependencies)\. This component has the following requirements:
+ A SageMaker Edge device fleet that uses the same AWS IoT role alias as your Greengrass core device\.
+ The [Greengrass device role](device-service-role.md) configured with the following: 
  + The [AmazonSageMakerEdgeDeviceFleetPolicy](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AmazonSageMakerEdgeDeviceFleetPolicy) IAM managed policy\.
  + The `s3:PutObject` action, as shown in the following IAM policy example\.

    ```
    {
      "Version": "2012-10-17",
      "Statement": [
      {
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "*"
        ],
        "Effect": "Allow"
      }
      ]
    }
    ```
  + A trust relationship that allows `credentials.iot.amazonaws.com` and `sagemaker.amazonaws.com` to assume the role, as shown in the following IAM policy example\.

    ```
    { 
      "Version": "2012-10-17",
      "Statement": [ 
      { 
        "Effect": "Allow", 
        "Principal": {
          "Service": "credentials.iot.amazonaws.com"
         }, 
        "Action": "sts:AssumeRole" 
      }, 
      { 
        "Effect": "Allow", 
        "Principal": {
          "Service": "sagemaker.amazonaws.com"
        }, 
        "Action": "sts:AssumeRole" 
      } 
      ] 
    }
    ```

## Dependencies<a name="sagemaker-edge-manager-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. You must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#sagemaker-edge-manager-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for version 1\.0\.x of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.3\.0 | Soft | 
| [Token exchange service](token-exchange-service-component.md) | >=0\.0\.0 | Hard | 

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="sagemaker-edge-manager-component-config"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

**Note**  
This section describes the configuration parameters that you set in the component\. For more information about the corresponding SageMaker Edge Manager configuration, see [Edge Manager Agent](https://docs.aws.amazon.com/sagemaker/latest/dg/edge-device-fleet-about.html#edge-device-fleet-running-agent) in the *Amazon SageMaker Developer Guide*\.

`DeviceFleetName`  
The name of the SageMaker Edge Manager device fleet that contains your Greengrass core device\.   
You must specify a value for this parameter in the configuration update when you deploy this component\.

`BucketName`  
The name of the S3 bucket to which you upload captured inference data\. The bucket name must contain the string `sagemaker`\.   
If you set `CaptureDataDestination` to `Cloud`, or if you set `CaptureDataPeriodicUpload` to `true`, then you must specify a value for this parameter in the configuration update when you deploy this component\.  
Capture data is an SageMaker feature that you use to upload inference input, inference results, and additional inference data to an S3 bucket or a local directory for future analysis\. For more information about using capture data with SageMaker Edge Manager, see [Manage Model](https://docs.aws.amazon.com/sagemaker/latest/dg/edge-manage-model.html#edge-manage-model-capturedata) in the *Amazon SageMaker Developer Guide*\.

`CaptureDataBatchSize`  
\(Optional\) The size of a batch of capture data requests that the agent handles\. This value must be less than the buffer size that you specify in `CaptureDataBufferSize`\. We recommend that you don't exceed half the buffer size\.  
The agent handles a request batch when the number of requests in the buffer meets the `CaptureDataBatchSize` number, or when the `CaptureDataPushPeriodSeconds` interval elapses, whichever occurs first\.  
Default: `10`

`CaptureDataBufferSize`  
\(Optional\) The maximum number of capture data requests stored in the buffer\.  
Default: `30`

`CaptureDataDestination`  
\(Optional\) The destination where you store captured data\. This parameter can have the following values:  
+ `Disk`—Writes captured data to the component's work directory\. 
+ `Cloud`—Uploads captured data to the S3 bucket that you specify in `BucketName`\.
If you specify `Disk`, you can also choose to periodically upload the captured data to your S3 bucket by setting `CaptureDataPeriodicUpload` to `true`\.  
Default: `Cloud`

`CaptureDataPeriodicUpload`  
\(Optional\) Boolean value that specifies whether to periodically upload captured data\. Set this parameter to `true` if you set `CaptureDataDestination` to `Disk`, and you also want the agent to periodically upload the captured data your S3 bucket\.  
Default: `false`

`CaptureDataPeriodicUploadPeriodSeconds`  
\(Optional\) The interval in seconds at which SageMaker Edge Manager agent uploads captured data to the S3 bucket\. Use this parameter if you set `CaptureDataPeriodicUpload` to `true`\.  
Default: `8`

`CaptureDataPushPeriodSeconds`  
\(Optional\) The interval in seconds at which SageMaker Edge Manager agent handles a batch of capture data requests from the buffer\.   
The agent handles a request batch when the number of requests in the buffer meets the `CaptureDataBatchSize` number, or when the `CaptureDataPushPeriodSeconds` interval elapses, whichever occurs first\.  
Default: `4`

`CaptureDataBase64EmbedLimit`  
\(Optional\) The maximum size in bytes of captured data that SageMaker Edge Manager agent uploads\.  
Default: `3072`

`FolderPrefix`  
\(Optional\) The name of the folder to which the agent writes the captured data\. If you set `CaptureDataDestination` to `Disk`, the agent creates the folder in the component's work directory\. If you set `CaptureDataDestination` to `Cloud`, the agent creates the folder in your S3 bucket\.   
Default: `sme-capture`

`SagemakerEdgeLogVerbose`  
\(Optional\) Boolean value that specifies whether to enable debug logging\.   
Default: `false`

`UnixSocketName`  
\(Optional\) The location of the SageMaker Edge Manager socket file descriptor on the core device\.  
Default: `/tmp/aws.greengrass.SageMakerEdgeManager.sock`

**Example Configuration merge update**  
The following example configuration specifies that the core device is part of the *MyEdgeDeviceFleet* and that the agent uploads capture data to the *DOC\-EXAMPLE\-BUCKET* S3 bucket\.   

```
{
    "DeviceFleetName": "MyEdgeDeviceFleet",
    "BucketName": "DOC-EXAMPLE-BUCKET"
}
```

## Changelog<a name="sagemaker-edge-manager-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  1\.0\.0  |  Initial version\.  | 