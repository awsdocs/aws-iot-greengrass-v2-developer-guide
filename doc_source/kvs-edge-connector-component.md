# Edge connector for Kinesis Video Streams<a name="kvs-edge-connector-component"></a>

The edge connector for Kinesis Video Streams component \(`aws.iot.EdgeConnectorForKVS`\) reads video feeds from local cameras and publishes the streams to Kinesis Video Streams\. You can configure this component to read video feeds from Internet Protocol \(IP\) cameras using Real Time Streaming Protocol \(RTSP\)\. Then, you can set up dashboards in [Amazon Managed Grafana](https://docs.aws.amazon.com/grafana/latest/userguide/what-is-Amazon-Managed-Service-Grafana.html) or local Grafana servers to monitor and interact with the video streams\.

You can integrate this component with AWS IoT TwinMaker to display and control video streams in Grafana dashboards\. AWS IoT TwinMaker is an AWS service that enables you to build operational digital twins of physical systems\. You can use AWS IoT TwinMaker to visualize data from sensors, cameras, and enterprise applications for you to track your physical factories, buildings, or industrial plants\. You can also use this data to monitor operations, diagnose errors, and repair errors\. For more information, see [What is AWS IoT TwinMaker?](https://docs.aws.amazon.com/iot-twinmaker/latest/guide/what-is-twinmaker.html) in the *AWS IoT TwinMaker User Guide*\.

This component stores its configuration in AWS IoT SiteWise, which is an AWS service that models and stores industrial data\. In AWS IoT SiteWise, *assets* represent objects such as devices, equipment, or groups of other objects\. To configure and use this component, you create an AWS IoT SiteWise asset for each Greengrass core device and for each IP camera connected to each core device\. Each asset has properties that you configure to control features, such as live streaming, on\-demand upload, and local caching\. To specify the URL for each camera, you create a secret in AWS Secrets Manager that contains the URL of the camera\. If the camera requires authentication, you also specify a user name and password in the URL\. Then, you specify that secret in an asset property for the IP camera\.

This component uploads each camera's video stream to a Kinesis video stream\. You specify the name of the destination Kinesis video stream in the AWS IoT SiteWise asset configuration for each camera\. If the Kinesis video stream doesn't exist, this component creates it for you\.

AWS IoT TwinMaker provides a script that you can run to create these AWS IoT SiteWise assets and Secrets Manager secrets\. For more information about how to create these resources, and how to install, configure, and use this component, see [AWS IoT TwinMaker video integration](https://docs.aws.amazon.com/iot-twinmaker/latest/guide/video-integration.html) in the *AWS IoT TwinMaker User Guide*\.

**Note**  
The edge connector for Kinesis Video Streams component is available only in the following AWS Regions:  
US East \(N\. Virginia\)
US West \(Oregon\)
Europe \(Ireland\)
Asia Pacific \(Singapore\)

**Topics**
+ [Versions](#kvs-edge-connector-component-versions)
+ [Type](#kvs-edge-connector-component-type)
+ [Operating system](#kvs-edge-connector-component-os-support)
+ [Requirements](#kvs-edge-connector-component-requirements)
+ [Dependencies](#kvs-edge-connector-component-dependencies)
+ [Configuration](#kvs-edge-connector-component-configuration)
+ [Licenses](#kvs-edge-connector-component-licenses)
+ [Usage](#kvs-edge-connector-component-usage)
+ [Local log file](#kvs-edge-connector-component-log-file)
+ [Changelog](#kvs-edge-connector-component-changelog)
+ [See also](#kvs-edge-connector-component-see-also)

## Versions<a name="kvs-edge-connector-component-versions"></a>

This component has the following versions:
+ 1\.0\.x

## Type<a name="kvs-edge-connector-component-type"></a>

<a name="public-component-type-generic"></a>This <a name="public-component-type-generic-phrase"></a>component is a generic component \(`aws.greengrass.generic`\)\. The [Greengrass nucleus](greengrass-nucleus-component.md) runs the component's lifecycle scripts\.

<a name="public-component-type-more-information"></a>For more information, see [Component types](develop-greengrass-components.md#component-types)\.

## Operating system<a name="kvs-edge-connector-component-os-support"></a>

This component can be installed on Linux core devices only\.

## Requirements<a name="kvs-edge-connector-component-requirements"></a>

This component has the following requirements:
+ You can deploy this component to only single core devices, because the component configuration must be unique for each core device\. You can't deploy this component to groups of core devices\.
+ [GStreamer](https://gstreamer.freedesktop.org) 1\.8\.4 or later installed on the core device\. For more information, see [Installing GStreamer](https://gstreamer.freedesktop.org/documentation/installing/index.html?gi-language=c)\.

  On a device with `apt`, you can run the following commands to install GStreamer\.

  ```
  sudo apt install -y libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev gstreamer1.0-plugins-base-apps
  sudo apt install -y gstreamer1.0-libav
  sudo apt install -y gstreamer1.0-plugins-bad gstreamer1.0-plugins-good gstreamer1.0-plugins-ugly gstreamer1.0-tools
  ```
+ An AWS IoT SiteWise asset for each core device\. This AWS IoT SiteWise asset represents the core device\. For more information about how to create this asset, see [AWS IoT TwinMaker video integration](https://docs.aws.amazon.com/iot-twinmaker/latest/guide/video-integration.html) in the *AWS IoT TwinMaker User Guide*\.
+ An AWS IoT SiteWise asset for each IP camera that you connect to each core device\. These AWS IoT SiteWise assets represent the cameras that stream video to each core device\. Each camera's asset must be associated to the asset for the core device that connects to the camera\. Camera assets have properties that you can configure to specify a Kinesis video stream, an authentication secret, and video streaming parameters\. For more information about how to create and configure camera assets, see [AWS IoT TwinMaker video integration](https://docs.aws.amazon.com/iot-twinmaker/latest/guide/video-integration.html) in the *AWS IoT TwinMaker User Guide*\.
+ An AWS Secrets Manager secret for each IP camera\. This secret must define a key\-value pair, where the key is `RTSPStreamUrl`, and the value is the URL for the camera\. If the camera requires authentication, include the user name and password in this URL\. You can use a script to create a secret when you create the resources that this component requires\. For more information, see [AWS IoT TwinMaker video integration](https://docs.aws.amazon.com/iot-twinmaker/latest/guide/video-integration.html) in the *AWS IoT TwinMaker User Guide*\.

  You can also use the Secrets Manager console and API to create additional secrets\. For more information, see [Create a secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) in the *AWS Secrets Manager User Guide*\.
+ The [Greengrass token exchange role](device-service-role.md) must allow the following AWS Secrets Manager, AWS IoT SiteWise, and Kinesis Video Streams actions, as shown in the following example IAM policy\.
**Note**  
This example policy allows the device to get the value of secrets named **IPCamera1Url** and **IPCamera2Url**\. When you configure each IP camera, you specify a secret that contains the URL for that camera\. If the camera requires authentication, you also specify a user name and password in the URL\. The core device's token exchange role must allow access to the secret for each IP camera to connect\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": [
          "secretsmanager:GetSecretValue"
        ],
        "Effect": "Allow",
        "Resource": [
          "arn:aws:secretsmanager:region:account-id:secret:IPCamera1Url",
          "arn:aws:secretsmanager:region:account-id:secret:IPCamera2Url"
        ]
      },
      {
        "Action": [
          "iotsitewise:BatchPutAssetPropertyValue",
          "iotsitewise:DescribeAsset",
          "iotsitewise:DescribeAssetModel",
          "iotsitewise:DescribeAssetProperty",
          "iotsitewise:GetAssetPropertyValue",
          "iotsitewise:ListAssetRelationships",
          "iotsitewise:ListAssets",
          "iotsitewise:ListAssociatedAssets",
          "kinesisvideo:CreateStream",
          "kinesisvideo:DescribeStream",
          "kinesisvideo:GetDataEndpoint",
          "kinesisvideo:PutMedia",
          "kinesisvideo:TagStream"
        ],
        "Effect": "Allow",
        "Resource": [
          "*"
        ]
      }
    ]
  }
  ```
**Note**  
If you use a customer managed AWS Key Management Service key to encrypt secrets, the device role must also allow the `kms:Decrypt` action\.

### Endpoints and ports<a name="kvs-edge-connector-component-endpoints"></a>

This component must be able to perform outbound requests to the following endpoints and ports, in addition to endpoints and ports required for basic operation\. For more information, see [Allow device traffic through a proxy or firewall](allow-device-traffic.md)\.


| Endpoint | Port | Required | Description | 
| --- | --- | --- | --- | 
|  `kinesisvideo.region.amazonaws.com`  | 443 | Yes |  Upload data to Kinesis Video Streams\.  | 
|  `data.iotsitewise.region.amazonaws.com`  | 443 | Yes |  Publish video stream metadata to AWS IoT SiteWise\.  | 
|  `secretsmanager.region.amazonaws.com`  | 443 | Yes |  Download camera URL secrets to the core device\.  | 

## Dependencies<a name="kvs-edge-connector-component-dependencies"></a>

When you deploy a component, AWS IoT Greengrass also deploys compatible versions of its dependencies\. This means that you must meet the requirements for the component and all of its dependencies to successfully deploy the component\. This section lists the dependencies for the [released versions](#kvs-edge-connector-component-changelog) of this component and the semantic version constraints that define the component versions for each dependency\. You can also view the dependencies for each version of the component in the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrass)\. On the component details page, look for the **Dependencies** list\.

The following table lists the dependencies for version 1\.0\.0 of this component\.


| Dependency | Compatible versions | Dependency type | 
| --- | --- | --- | 
| [Greengrass nucleus](greengrass-nucleus-component.md) | >=2\.0\.0 <2\.6\.0 | Hard | 
| [Token exchange service](token-exchange-service-component.md) | ^2\.0\.0 | Hard | 
| [Stream manager](stream-manager-component.md) | ^2\.0\.0 | Hard | 

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Configuration<a name="kvs-edge-connector-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\.

`SiteWiseAssetIdForHub`  
The ID of the AWS IoT SiteWise asset that represents this core device\. For more information about how to create this asset and use it to interact with this component, see [AWS IoT TwinMaker video integration](https://docs.aws.amazon.com/iot-twinmaker/latest/guide/video-integration.html) in the *AWS IoT TwinMaker User Guide*\.

**Example: Configuration merge update**  

```
{
  "SiteWiseAssetIdForHub": "a1b2c3d4-5678-90ab-cdef-EXAMPLE11111"
}
```

## Licenses<a name="kvs-edge-connector-component-licenses"></a>

This component includes the following third\-party software/licensing:
+ [Quartz Job Scheduler](http://www.quartz-scheduler.org/) / Apache License 2\.0
+ [Java bindings for GStreamer 1\.x](https://github.com/gstreamer-java/gst1-java-core) / GNU Lesser General Public License v3\.0

## Usage<a name="kvs-edge-connector-component-usage"></a>

To configure and interact with this component, you can set properties on the AWS IoT SiteWise assets that represent the core device and the IP cameras where it connects\. You can also visualize and interact with video streams in Grafana dashboards through AWS IoT TwinMaker\. For more information, see [AWS IoT TwinMaker video integration](https://docs.aws.amazon.com/iot-twinmaker/latest/guide/video-integration.html) in the *AWS IoT TwinMaker User Guide*\.

## Local log file<a name="kvs-edge-connector-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/aws.iot.EdgeConnectorForKVS.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/aws.iot.EdgeConnectorForKVS.log
  ```

## Changelog<a name="kvs-edge-connector-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  1\.0\.0  |  Initial version\.  | 

## See also<a name="kvs-edge-connector-component-see-also"></a>
+ [What is AWS IoT TwinMaker?](https://docs.aws.amazon.com/iot-twinmaker/latest/guide/what-is-twinmaker.html) in the *AWS IoT TwinMaker User Guide*
+ [AWS IoT TwinMaker video integration](https://docs.aws.amazon.com/iot-twinmaker/latest/guide/video-integration.html) in the *AWS IoT TwinMaker User Guide*
+ [What is AWS IoT SiteWise?](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/what-is-sitewise.html) in the *AWS IoT SiteWise User Guide*
+ [Updating attribute values](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/update-attribute-values.html) in the *AWS IoT SiteWise User Guide*
+ [What is AWS Secrets Manager?](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) in the *AWS Secrets Manager User Guide*
+ [Create and manage secrets](https://docs.aws.amazon.com/secretsmanager/latest/userguide/managing-secrets.html) in the *AWS Secrets Manager User Guide*