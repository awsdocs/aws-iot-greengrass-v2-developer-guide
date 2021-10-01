# Greengrass nucleus<a name="greengrass-nucleus-component"></a>

The Greengrass nucleus component \(`aws.greengrass.Nucleus`\) is a mandatory component and the minimum requirement to run the AWS IoT Greengrass Core software on a device\. You can configure this component to customize and update your AWS IoT Greengrass Core software remotely\. Deploy this component to configure settings such as proxy, device role, and AWS IoT thing configuration on your core devices\.

**Important**  
When the version of the nucleus component changes, or when you change certain configuration parameters, the AWS IoT Greengrass Core software—which includes the nucleus and all other components on your device—restarts to apply the changes\.   
<a name="component-patch-update"></a>When you deploy a component, AWS IoT Greengrass installs the latest supported versions of all component dependencies for that component\. Because of this, new patch versions of AWS\-provided public components might be automatically deployed to your core devices if you add new devices to a thing group, or you update the deployment that targets those devices\. Some automatic updates, such as a nucleus update, can cause your devices to restart unexpectedly\.   
<a name="component-version-pinning"></a>To prevent unintended updates for a component that is running on your device, we recommend that you directly include your preferred version of that component when you [create a deployment](create-deployments.md)\. For more information about update behavior for AWS IoT Greengrass Core software, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.

**Topics**
+ [Versions](#greengrass-nucleus-component-versions)
+ [Requirements](#greengrass-nucleus-component-requirements)
+ [Dependencies](#greengrass-nucleus-component-dependencies)
+ [Installation](#greengrass-nucleus-component-install)
+ [Configuration](#greengrass-nucleus-component-configuration)
+ [Local log file](#greengrass-nucleus-component-log-file)
+ [Changelog](#greengrass-nucleus-component-changelog)

## Versions<a name="greengrass-nucleus-component-versions"></a>

This component has the following versions:
+ 2\.4\.x
+ 2\.3\.x
+ 2\.2\.x
+ 2\.1\.x
+ 2\.0\.x

## Requirements<a name="greengrass-nucleus-component-requirements"></a>

Devices must meet certain requirements to install and run the Greengrass nucleus and the AWS IoT Greengrass Core software\. For more information, see [AWS IoT Greengrass Core software requirements](setting-up.md#greengrass-v2-requirements)\.

## Dependencies<a name="greengrass-nucleus-component-dependencies"></a>

The Greengrass nucleus does not include any component dependencies\. However, several AWS\-provided components include the nucleus as a dependency\. For more information, see [AWS\-provided components](public-components.md)\.

For more information about component dependencies, see the [component recipe reference](component-recipe-reference.md#recipe-reference-component-dependencies)\.

## Installation<a name="greengrass-nucleus-component-install"></a>

You can download an installer that sets up the Greengrass nucleus component on your device\. This installer sets up your device as a Greengrass core device\. There are two types of installations that you can perform: a quick installation that creates required AWS resources for you, or a manual installation where you create the AWS resources yourself\. For more information, see [Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\.

You can also follow a tutorial to install the Greengrass nucleus and explore Greengrass component development\. For more information, see [Getting started with AWS IoT Greengrass V2](getting-started.md)\.

## Configuration<a name="greengrass-nucleus-component-configuration"></a>

This component provides the following configuration parameters that you can customize when you deploy the component\. Some parameters require that the AWS IoT Greengrass Core software restarts to take effect\. For more information about why and how to configure this component, see [Configure the AWS IoT Greengrass Core software](configure-greengrass-core-v2.md)\.

`iotRoleAlias`  
<a name="nucleus-component-configuration-iot-role-alias"></a>The AWS IoT role alias that points to a token exchange IAM role\. The AWS IoT credentials provider assumes this role to allow the Greengrass core device to interact with AWS services\. For more information, see [Authorize core devices to interact with AWS services](device-service-role.md)\.  
When you run the AWS IoT Greengrass Core software with the `--provision true` option, the software provisions a role alias and sets its value in the nucleus component\.

`networkProxy`  
\(Optional\) The network proxy to use for all connections\. For more information, see [Connect on port 443 or through a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy)\.  
<a name="nucleus-component-parameter-restart-para"></a>When you deploy a change to this configuration parameter, the AWS IoT Greengrass Core software restarts for the change to take effect\.
This object contains the following information:    
`noProxyAddresses`  
\(Optional\) A comma\-separated list of IP addresses or hostnames that are exempt from the proxy\.  
`proxy`  
The proxy to which to connect\. This object contains the following information:    
`url`  
The URL of the proxy server in the format `scheme://userinfo@host:port`\.  <a name="nucleus-component-configuration-proxy-url-segments"></a>
+ `scheme` – The scheme, which must be `http`\.
+ `userinfo` – \(Optional\) The user name and password information\. If you specify this information in the `url`, the Greengrass core device ignores the `username` and `password` fields\.
+ `host` – The host name or IP address of the proxy server\.
+ `port` – \(Optional\) The port number\. If you don't specify the port, then the Greengrass core device uses the following default value:
  + `http` – 80  
`username`  
\(Optional\) The user name that authenticates the proxy server\.  
`password`  
\(Optional\) The password that authenticates the proxy server\.

`mqtt`  
\(Optional\) The MQTT configuration for the Greengrass core device\. For more information, see [Connect on port 443 or through a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy)\.  
<a name="nucleus-component-parameter-restart-para"></a>When you deploy a change to this configuration parameter, the AWS IoT Greengrass Core software restarts for the change to take effect\.
This object contains the following information:    
`port`  
\(Optional\) The port to use for MQTT connections\.  
Default: `8883`  
`keepAliveTimeoutMs`  
\(Optional\) The amount of time in milliseconds between each `PING` message that the client sends to keep the MQTT connection alive\.  
Default: `60000` \(60 seconds\)  
`pingTimeoutMs`  
\(Optional\) The amount of time in milliseconds that the client waits to receive a `PINGACK` message from the server\. If the wait exceeds the timeout, the core device closes and reopens the MQTT connection\.  
Default: `30000` \(30 seconds\)  
`maxInFlightPublishes`  
\(Optional\) The maximum number of unacknowledged MQTT QoS 1 messages that can be in flight at the same time\.  
This feature is available for v2\.1\.0 and later of this component\.  
Default: `5`  
Valid range: Maximum value of 100  
`maxMessageSizeInBytes`  
\(Optional\) The maximum size of an MQTT message\. If a message exceeds this size, the Greengrass nucleus rejects the message with an error\.  
This feature is available for v2\.1\.0 and later of this component\.  
Default: `131072` \(128 KB\)  
Valid range: Maximum value of `2621440` \(2\.5 MB\)  
`maxPublishRetry`  
\(Optional\) The maximum number of times to retry a message that fails to publish\. You can specify `-1` to retry unlimited times\.  
This feature is available for v2\.1\.0 and later of this component\.  
Default: `100`  
`spooler`  
\(Optional\) The MQTT spooler configuration for the Greengrass core device\. This object contains the following information:    
`maxSizeInBytes`  
\(Optional\) The maximum size of the cache where the core device stores unprocessed MQTT messages in memory\. If the cache is full, the core device discards the oldest messages to add new messages\.  
Default: `2621440` \(2\.5 MB\)  
`keepQos0WhenOffline`  
\(Optional\) You can spool MQTT QoS 0 messages that the core device receives while its offline\. If you set this option to `true`, the core device spools QoS 0 messages that it can't send while it's offline\. If you set this option to `false`, the core device discards these messages\. The core device always spools QoS 1 messages unless the spool is full\.  
Default: `false`

  `jvmOptions`  
\(Optional\) The JVM options to use to run the AWS IoT Greengrass Core software\. For information about recommended JVM options for running AWS IoT Greengrass Core software, see [Control memory allocation with JVM options](configure-greengrass-core-v2.md#jvm-tuning)\.  
<a name="nucleus-component-parameter-restart-para"></a>When you deploy a change to this configuration parameter, the AWS IoT Greengrass Core software restarts for the change to take effect\.

`iotDataEndpoint`  
<a name="nucleus-component-configuration-iot-data-endpoint"></a>The AWS IoT data endpoint for your AWS account\.  
<a name="nucleus-component-set-iot-endpoints"></a>When you run the AWS IoT Greengrass Core software with the `--provision true` option, the software gets your data and credentials endpoints from AWS IoT and sets them in the nucleus component\.

`iotCredEndpoint`  
<a name="nucleus-component-configuration-iot-cred-endpoint"></a>The AWS IoT credentials endpoint for your AWS account\.  
<a name="nucleus-component-set-iot-endpoints"></a>When you run the AWS IoT Greengrass Core software with the `--provision true` option, the software gets your data and credentials endpoints from AWS IoT and sets them in the nucleus component\.

`greengrassDataPlanePort`  
This feature is available in v2\.0\.4 and later of this component\.  
\(Optional\) The port to use for data plane connections\. For more information, see [Connect on port 443 or through a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy)\.  
You must specify a port where the device can make outbound connections\. If you specify a port that is blocked, the device won't be able to connect to AWS IoT Greengrass to receive deployments\.
Choose from the following options:  
+ `443`
+ `8443`
Default: `8443`

`awsRegion`  
The AWS Region to use\.

`runWithDefault`  
The system user and group to use to run components\.  
<a name="nucleus-component-parameter-restart-para"></a>When you deploy a change to this configuration parameter, the AWS IoT Greengrass Core software restarts for the change to take effect\.
This object contains the following information:    
`posixUser`  
The name or ID of the system user and system group that the core device uses to run generic and Lambda components\. Specify the user and group separated by a colon \(`:`\), where the group is optional\. If you omit the group, the AWS IoT Greengrass Core software defaults to the primary group of the user that you specify\. For example, you can specify `ggc_user` or `ggc_user:ggc_group`\. For more information, see [Configure the user and group that run components](configure-greengrass-core-v2.md#configure-component-user)\.  
When you run the AWS IoT Greengrass Core software with the `--component-default-user ggc_user:ggc_group` option, the software sets this parameter in the nucleus component\.  
`systemResourceLimits`  
This feature is available in v2\.4\.0 and later of this component\.  
The system resource limits to apply to generic and non\-containerized Lambda component processes by default\. You can override system resource limits for individual components when you create a deployment\. For more information, see [Configure system resource limits for components](configure-greengrass-core-v2.md#configure-component-system-resource-limits)\.  
This object contains the following information:    
`cpus`  
<a name="system-resource-limits-cpu-definition-each"></a>The maximum amount of CPU time that each component's processes can use on the core device\. A core device's total CPU time is equivalent to the device's number of CPU cores\. For example, on a core device with 4 CPU cores, you can set this value to `2` to limit each component's processes to 50 percent usage of each CPU core\. On a device with 1 CPU core, you can set this value to `0.25` to limit each component's processes to 25 percent usage of the CPU\. If you set this value to a number greater than the number of CPU cores, the AWS IoT Greengrass Core software doesn't limit the components' CPU usage\.  
`memory`  
<a name="system-resource-limits-memory-definition-each"></a>The maximum amount of RAM \(in kilobytes\) that each component's processes can use on the core device\.

`logging`  
\(Optional\) The logging configuration for the core device\. For more information about how to configure and use Greengrass logs, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.  
This object contains the following information:    
  `level`   
\(Optional\) The minimum level of log messages to output\.  
Choose from the following log levels, listed here in level order:  <a name="nucleus-log-levels"></a>
+ `DEBUG`
+ `INFO`
+ `WARN`
+ `ERROR`
Default: `INFO`  
  `format`   
\(Optional\) The data format of the logs\. Choose from the following options:  
+ `TEXT` – Choose this option if you want to view logs in text form\.
+ `JSON` – Choose this option if you want to view logs with the [Greengrass CLI logs command](gg-cli-logs.md) or interact with logs programmatically\.
Default: `TEXT`  
`outputType`  
\(Optional\) The output type for logs\. Choose from the following options:  
+ `FILE` – The AWS IoT Greengrass Core software outputs logs to files in the directory that you specify in `outputDirectory`\.
+ `CONSOLE` – The AWS IoT Greengrass Core software prints logs to `stdout`\. Choose this option to view logs as the core device prints them\.
Default: `FILE`  
  `fileSizeKB`   
\(Optional\) The maximum size of each log file \(in kilobytes\)\. After a log file exceeds this maximum file size, the AWS IoT Greengrass Core software creates a new log file\.  
<a name="nucleus-component-logging-parameter-file-only"></a>This parameter applies only when you specify `FILE` for `outputType`\.  
Default: `1024`  
  `totalLogsSizeKB`   
\(Optional\) The maximum total size of log files \(in kilobytes\) for each component, including the Greengrass nucleus\. The Greengrass nucleus' log files also include logs from [plugin components](develop-greengrass-components.md#component-types)\. After a component's total size of log files exceeds this maximum size, the AWS IoT Greengrass Core software deletes that component's oldest log files\.  
This parameter is equivalent to the [log manager component's](log-manager-component.md) [disk space limit](log-manager-component.md#log-manager-component-configuration) parameter \(`diskSpaceLimit`\), which you can specify for the Greengrass nucleus \(system\) and each component\. The AWS IoT Greengrass Core software uses the minimum of the two values as the maximum total log size for the Greengrass nucleus and each component\.  
<a name="nucleus-component-logging-parameter-file-only"></a>This parameter applies only when you specify `FILE` for `outputType`\.  
Default: `10240`  
  `outputDirectory`   
\(Optional\) The output directory for log files\.  
<a name="nucleus-component-logging-parameter-file-only"></a>This parameter applies only when you specify `FILE` for `outputType`\.  
Default: `/greengrass/v2/logs`, where */greengrass/v2* is the AWS IoT Greengrass root folder\.

  `fleetstatus`   
This parameter is available in v2\.1\.0 and later of this component\.  
\(Optional\) The fleet status configuration for the core device\.  
This object contains the following information:    
`periodicStatusPublishIntervalSeconds`  
\(Optional\) The amount of time \(in seconds\) between which the core device publishes device status to the AWS Cloud\.  
Minimum: `86400`  
Default: `86400`

  `telemetry`   
\(Optional\) The system health telemetry configuration for the core device\. For more information about telemetry metrics and how to act on telemetry data, see [Gather system health telemetry data from AWS IoT Greengrass core devices](telemetry.md)\.  
This object contains the following information:    
`enabled`  
\(Optional\) You can enable or disable telemetry\.  
Default: `true`  
`periodicAggregateMetricsIntervalSeconds`  
\(Optional\) The interval \(in seconds\) over which the core device aggregates metrics\.  
If you set this value lower than the minimum supported value, the nucleus uses the default value instead\.  
Minimum: `3600`  
Default: `3600`  
`periodicPublishMetricsIntervalSeconds`  
\(Optional\) The amount of time \(in seconds\) between which the core device publishes telemetry metrics to the AWS Cloud\.  
If you set this value lower than the minimum supported value, the nucleus uses the default value instead\.  
Minimum: `86400`  
Default: `86400`

`deploymentPollingFrequencySeconds`  
\(Optional\) The period in seconds at which to poll for deployment notifications\.  
Default: `15`

`componentStoreMaxSizeBytes`  
\(Optional\) The maximum size on disk of the component store, which comprises component recipes and artifacts\.  
Default: `10000000000` \(10 GB\)

  `platformOverride`   
\(Optional\) A dictionary of attributes that identify the core device's platform\. Use this to define custom platform attributes that component recipes can use to identify the correct lifecycle and artifacts for the component\. For example, you might define a hardware capability attribute to deploy only the minimal set of artifacts for a component to run\. For more information, see the [manifest platform parameter](component-recipe-reference.md#component-platform-definition) in the component recipe\.  
You can also use this parameter to override the `os` and `architecture` platform attributes of the core device\.

**Example: Configuration merge update**  

```
{
  "iotRoleAlias": "GreengrassCoreTokenExchangeRoleAlias",
  "networkProxy": {
    "noProxyAddresses": "http://192.168.0.1,www.example.com",
    "proxy": {
      "url": "http://my-proxy-server:1100",
      "username": "Mary_Major",
      "password": "pass@word1357"
    }
  },
  "mqtt": {
    "port": 443
  },
  "greengrassDataPlanePort": 443,
  "jvmOptions": "-Xmx64m",
  "runWithDefault": {
    "posixUser": "ggc_user:ggc_group"
  }
}
```

## Local log file<a name="greengrass-nucleus-component-log-file"></a>

This component uses the following log file\.

```
/greengrass/v2/logs/greengrass.log
```

**To view this component's logs**
+ Run the following command on the core device to view this component's log file in real time\. Replace */greengrass/v2* with the path to the AWS IoT Greengrass root folder\.

  ```
  sudo tail -f /greengrass/v2/logs/greengrass.log
  ```

## Changelog<a name="greengrass-nucleus-component-changelog"></a>

The following table describes the changes in each version of the component\.


|  **Version**  |  **Changes**  | 
| --- | --- | 
|  2\.4\.0  |  <a name="changelog-nucleus-2.4.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-nucleus-component.html)  | 
|  2\.3\.0  |  <a name="changelog-nucleus-2.3.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-nucleus-component.html)  | 
|  2\.2\.0  |  <a name="changelog-nucleus-2.2.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-nucleus-component.html)  | 
|  2\.1\.0  |  <a name="changelog-nucleus-2.1.0"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-nucleus-component.html)  | 
|  2\.0\.5  |  <a name="changelog-nucleus-2.0.5"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-nucleus-component.html)  | 
|  2\.0\.4  |  <a name="changelog-nucleus-2.0.4"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/greengrass/v2/developerguide/greengrass-nucleus-component.html)  | 
|  2\.0\.3  |  Initial version\.  | 