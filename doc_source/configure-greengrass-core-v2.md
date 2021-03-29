# Configure the AWS IoT Greengrass Core software<a name="configure-greengrass-core-v2"></a>

The AWS IoT Greengrass Core software provides options that you can use to configure the software\. You can create deployments  to configure the AWS IoT Greengrass Core software on each core device\.

**Topics**
+ [Deploy the Greengrass core nucleus component](#configure-nucleus-component)
+ [Use service endpoints that match the root CA certificate type](#certificate-endpoints)
+ [Control memory allocation with JVM options](#jvm-tuning)
+ [Configure the user and group that run components](#configure-component-user)
+ [Connect on port 443 or through a network proxy](#configure-alpn-network-proxy)
+ [Configure MQTT timeouts and cache settings](#configure-mqtt)
+ [Configure AWS IoT Greengrass as a system service](#configure-system-service)

## Deploy the Greengrass core nucleus component<a name="configure-nucleus-component"></a>

AWS IoT Greengrass provides the AWS IoT Greengrass Core software as a component that you can deploy to your Greengrass core devices\. You can create a deployment to apply the same configuration to multiple Greengrass core devices\. For more information, see [Greengrass nucleus](greengrass-nucleus-component.md) and [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.

## Use service endpoints that match the root CA certificate type<a name="certificate-endpoints"></a>

Your AWS IoT Core and AWS IoT Greengrass endpoints must correspond to the certificate type of the root CA certificate on your device\. If the endpoints and certificate type do not match, authentication attempts fail between the device and AWS IoT Core or AWS IoT Greengrass\. For more information, see [Server authentication](https://docs.aws.amazon.com/iot/latest/developerguide/server-authentication.html) in the *AWS IoT Developer Guide*\.

If your device uses an Amazon Trust Services \(ATS\) root CA certificate, which is the preferred method, it must also use ATS endpoints for device management and discovery data plane operations\. ATS endpoints include the `ats` segment, as shown in the following syntax for the AWS IoT Core endpoint\.

```
prefix-ats.iot.region.amazonaws.com
```

**Note**  
For backward compatibility, AWS IoT Greengrass currently supports legacy VeriSign root CA certificates and endpoints in some AWS Regions\. If you're using a legacy VeriSign root CA certificate, we recommend that you create an ATS endpoint and use an ATS root CA certificate instead\. Otherwise, make sure to use the corresponding legacy endpoints\. For more information, see [Supported legacy endpoints](https://docs.aws.amazon.com/general/latest/gr/greengrass.html#greengrass-legacy-endpoints) in the *Amazon Web Services General Reference*\.

## Control memory allocation with JVM options<a name="jvm-tuning"></a>

If you are running AWS IoT Greengrass on a device with limited memory, you can use Java virtual machine \(JVM\) heap size options to control the amount of memory that AWS IoT Greengrass Core software uses\. 

To control memory allocation, create a new deployment or revise an existing deployment that includes the nucleus component, and specify the heap size options in the `jvmOptions` configuration parameter in the [nucleus component configuration](greengrass-nucleus-component.md#greengrass-nucleus-component-configuration)\. The heap size in the JVM determines how many objects an application can create in memory before [garbage collection](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/introduction.html) occurs\. You can specify the *initial JVM heap size* \(the `-Xms` argument\) and a *maximum JVM heap size* \(the `-Xmx` argument\)\. A larger initial heap size allows more objects to be created before garbage collection occurs, but it also means that the garbage collector will take longer to compact the heap\. The maximum heap size specifies the maximum amount of memory the JVM can allocate when expanding the heap during heavy activity\. We recommend that you set the `jvmOptions` value to at least `-Xmx64m`\. 

For information about deploying components, see [Create deployments](create-deployments.md)\.

## Configure the user and group that run components<a name="configure-component-user"></a>

The AWS IoT Greengrass Core software can run component processes as a system user and group different from the one that runs the software\. This increases security, because you can run the AWS IoT Greengrass Core software as root without giving root permissions to components that run on the core device\.

When you configure the user and group, you specify them separated by a colon \(`:`\) in the following format: `user:group`\. The group is optional\. If you don't specify a group, the AWS IoT Greengrass Core software defaults to the primary group of the user\. You can use name or ID to identify the user and group\.

You can configure the user and group for each component and for each core device\.
+ **Configure per component**

  You can configure each component to run with a user and group specific to that component\. When you create a deployment, you can specify the user and group for each component in the deployment\. The AWS IoT Greengrass Core software runs components as this user and group if you specify them\. Otherwise, it defaults to run components as the default user and group that you configure for the core device\. For more information, see [Create deployments](create-deployments.md)\.

  You can also specify the default user and group for each component in the component's recipe\. For more information, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.
+ **Configure per core device**

  You can configure a default system user and group that the AWS IoT Greengrass Core software uses to run components\. When the AWS IoT Greengrass Core software runs a component, it uses the user and group that you specify for that component\. If that component doesn't specify a user and group, then the AWS IoT Greengrass Core software runs the component as the default user and group that you configure for the core device\. For more information, see [Configure the default user and group](#configure-default-component-user)\.

**Note**  
If you don't configure a user to run components and you run the AWS IoT Greengrass Core software as root, then the software won't run components\. You must specify a default user to run components if you run as root\.  
If you don't configure a user to run components and you run the AWS IoT Greengrass Core software as a non\-root user, then the software runs components as that user\.

You can also run components as a system user that doesn't exist, also called an unknown user, to increase security\. On Linux operating systems, a process can signal any other process that is run by the same user\. An unknown user doesn't run other processes, so you can run components as an unknown user to prevent components from signaling other components on the core device\. To run components as an unknown user, specify a user ID that doesn't exist on the core device\. You can also specify a group ID that doesn't exist to run as an unknown group\.

You can't configure the AWS IoT Greengrass Core software to run components as the root user \(UID 0\) or group \(GID 0\)\.

### Configure the default user and group<a name="configure-default-component-user"></a>

You can use a deployment to configure the default user and group on a core device\. In this deployment, you update the [nucleus component](greengrass-nucleus-component.md) configuration\.

**Note**  
You can also set the default user and group when you run the AWS IoT Greengrass Core software with the `--component-default-user` option\. For more information, see [Install the AWS IoT Greengrass Core software](install-greengrass-core-v2.md)\.

To configure the default user and group, [create a deployment](create-deployments.md) that specifies the following configuration update for the `aws.greengrass.Nucleus` component\.

```
{
  "runWithDefault": {
    "posixUser": "ggc_user:ggc_group"
  }
}
```

The following example defines a deployment that configures `ggc_user` as the default user and `ggc_group` as the default group\. The `merge` configuration update requires a serialized JSON object\.

```
{
  "components": {
    "aws.greengrass.Nucleus": {
      "version": "0.0.0",
      "configurationUpdate": {
        "merge": "{\"runWithDefault\":{\"posixUser\":\"ggc_user:ggc_group\"}}"
      }
    }
  }
}
```

## Connect on port 443 or through a network proxy<a name="configure-alpn-network-proxy"></a>

AWS IoT Greengrass core devices communicate with AWS IoT Core using the MQTT messaging protocol with TLS client authentication\. By convention, MQTT over TLS uses port 8883\. However, as a security measure, restrictive environments might limit inbound and outbound traffic to a small range of TCP ports\. For example, a corporate firewall might open port 443 for HTTPS traffic, but close other ports that are used for less common protocols, such as port 8883 for MQTT traffic\. Other restrictive environments might require all traffic to go through an HTTP proxy before connecting to the internet\.

**Note**  
Greengrass core devices that run [Greengrass nucleus component](greengrass-nucleus-component.md) v2\.0\.3 and earlier use port 8443 to connect to the AWS IoT Greengrass data plane endpoint\. These devices must be able to connect to this endpoint on port 8443\. For more information, see [Allowing endpoints](#allow-endpoints-proxy)\.

To enable communication in these scenarios, AWS IoT Greengrass provides the following configuration options:
+ **MQTT communication over port 443**\. If your network allows connections to port 443, you can configure the Greengrass core device to use port 443 for MQTT traffic instead of the default port 8883\. This can be a direct connection to port 443 or a connection through a network proxy server\. Unlike the default configuration, which uses certificate\-based client authentication, MQTT on port 443 uses the [device service role](device-service-role.md) for authentication\.

  For more information, see [Configure MQTT over port 443](#configure-mqtt-port-443)\.
+ **HTTPS communication over port 443**\. The AWS IoT Greengrass Core software sends HTTPS traffic over port 8443 by default, but you can configure it to use port 443\. AWS IoT Greengrass uses the [Application Layer Protocol Network](https://tools.ietf.org/html/rfc7301) \(ALPN\) TLS extension to enable this connection\. As with the default configuration, HTTPS on port 443 uses certificate\-based client authentication\.
**Important**  
To use ALPN and enable HTTPS communication over port 443, your core device must run Java 8 update 252 or later\. All updates of Java version 9 and later also support ALPN\.

  For more information, see [Configure HTTPS over port 443](#configure-https-port-443)\.
+ **Connection through a network proxy**\. You can configure a network proxy server to act as an intermediary for connecting to the Greengrass core device\. AWS IoT Greengrass supports only basic authentication and HTTP proxies\. AWS IoT Greengrass doesn't support HTTPS proxies\.

  The AWS IoT Greengrass Core software passes the proxy configuration to components through the `ALL_PROXY`, `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables\. Components must use these settings to connect through the proxy\. Components use common libraries \(such as boto3, cURL, and the python `requests` package\) that typically use these environment variables by default to make connections\. If a component also specifies these environment variables, AWS IoT Greengrass doesn't override them\.

  For more information, see [Configure a network proxy](#configure-network-proxy)\.

### Configure MQTT over port 443<a name="configure-mqtt-port-443"></a>

You can use a deployment to configure MQTT over port 443 on a single core device or a group of core devices\. In this deployment, you update the [nucleus component](greengrass-nucleus-component.md) configuration\. The nucleus restarts when you update its `mqtt` configuration\.

To configure MQTT over port 443, [create a deployment](create-deployments.md) that specifies the following configuration update for the `aws.greengrass.Nucleus` component\.

```
{
  "mqtt": {
    "port": 443
  }
}
```

The following example defines a deployment that configures MQTT over port 443\. The `merge` configuration update requires a serialized JSON object\.

```
{
  "components": {
    "aws.greengrass.Nucleus": {
      "version": "2.0.5",
      "configurationUpdate": {
        "merge": "{\"mqtt\":{\"port\":443}}"
      }
    }
  }
}
```

### Configure HTTPS over port 443<a name="configure-https-port-443"></a>

This feature requires [Greengrass nucleus](greengrass-nucleus-component.md) v2\.0\.4 or later\.

You can use a deployment to configure HTTPS over port 443 on a single core device or a group of core devices\. In this deployment, you update the [nucleus component](greengrass-nucleus-component.md) configuration\.

To configure HTTPS over port 443, [create a deployment](create-deployments.md) that specifies the following configuration update for the `aws.greengrass.Nucleus` component\.

```
{
  "greengrassDataPlanePort": 443
}
```

The following example defines a deployment that configures HTTPS over port 443\. The `merge` configuration update requires a serialized JSON object\.

```
{
  "components": {
    "aws.greengrass.Nucleus": {
      "version": "2.0.5",
      "configurationUpdate": {
        "merge": "{\"greengrassDataPlanePort\":443}"
      }
    }
  }
}
```

### Configure a network proxy<a name="configure-network-proxy"></a>

Follow the procedure in this section to configure Greengrass core devices to connect to the internet through an HTTP network proxy\.

**Important**  
Your device's role must allow the following permissions to use a network proxy:  
`iot:Connect`
`iot:Publish`
`iot:Receive`
`iot:Subscribe`
This is necessary because the device uses AWS credentials from the token exchange service to authenticate MQTT connections to AWS IoT\. The device uses MQTT to receive and install deployments from the AWS Cloud, so your device won't work unless you define these permissions on its role\. Devices typically use X\.509 certificates to authenticate MQTT connections, but devices can't do this to authenticate when they use a proxy\.  
For more information about how to configure the device role, see [Authorize core devices to interact with AWS services](device-service-role.md)\.

You can use a deployment to configure a network proxy on a single core device or a group of core devices\. In this deployment, you update the [nucleus component](greengrass-nucleus-component.md) configuration\. The nucleus restarts when you update its `networkProxy` configuration\.

To configure a network proxy, [create a deployment](create-deployments.md) for the `aws.greengrass.Nucleus` component that merges the following configuration update\. This configuration update contains the [networkProxy object](#network-proxy-object)\.

```
{
  "networkProxy": {
    "noProxyAddresses": "http://192.168.0.1,www.example.com",
    "proxy": {
      "url": "https://my-proxy-server:1100",
      "username": "Mary_Major",
      "password": "pass@word1357"
    }
  }
}
```

The following example defines a deployment that configures a network proxy\. The `merge` configuration update requires a serialized JSON object\.

```
{
  "components": {
    "aws.greengrass.Nucleus": {
      "version": "2.0.5",
      "configurationUpdate": {
        "merge": "{\"networkProxy\":{\"noProxyAddresses\":\"http://192.168.0.1,www.example.com\",\"proxy\":{\"url\":\"https://my-proxy-server:1100\",\"username\":\"Mary_Major\",\"password\":\"pass@word1357\"}}}"
      }
    }
  }
}
```

**The networkProxy object**

Use the `networkProxy` object to specify information about the network proxy\. This object contains the following information:

`noProxyAddresses`  
\(Optional\) A comma\-separated list of IP addresses or host names that are exempt from the proxy\.

`proxy`  
The proxy to which to connect\. This object contains the following information:    
`url`  
The URL of the proxy server in the format `scheme://userinfo@host:port`\.  
+ `scheme` – The scheme, which must be `http` or `https`\.
+ `userinfo` – \(Optional\) The user name and password information\. If you specify this in the `url`, the Greengrass core device ignores the `username` and `password` fields\.
+ `host` – The host name or IP address of the proxy server\.
+ `port` – \(Optional\) The port number\. If you don't specify the port, then the Greengrass core device uses the following default values:
  + `http` – 80
  + `https` – 443  
`username`  
\(Optional\) The user name to use to authenticate to the proxy server\.  
`password`  
\(Optional\) The password to use to authenticate to the proxy server\.

### Allowing endpoints<a name="allow-endpoints-proxy"></a>

Communication between Greengrass core devices and AWS IoT Core or AWS IoT Greengrass must be authenticated\. This authentication is based on registered X\.509 device certificates and cryptographic keys\. To allow authenticated requests to pass through proxies without additional encryption, allow the following endpoints\.


| Endpoint | Port | Description | 
| --- | --- | --- | 
|  `greengrass.region.amazonaws.com`  | 443 |  Used for control plane operations\.  | 
|  `prefix-ats.iot.region.amazonaws.com` or `prefix.iot.region.amazonaws.com`  |  MQTT: 8883 or 443 HTTPS: 8443 or 443  |  Used for data plane operations for device management, such as shadow sync\. Allow the use of one or both endpoints, depending on whether your core and connected devices use Amazon Trust Services \(preferred\) root CA certificates, legacy root CA certificates, or both\.  | 
|  `greengrass-ats.iot.region.amazonaws.com` or `greengrass.iot.region.amazonaws.com`  | 8443 or 443 |   Used for data plane operations\. Allow the use of one or both endpoints, depending on whether your core and connected devices use Amazon Trust Services \(preferred\) root CA certificates, legacy root CA certificates, or both\.\.  Clients that connect on port 443 must implement the [Application Layer Protocol Negotiation \(ALPN\)](https://tools.ietf.org/html/rfc7301) TLS extension and pass `x-amzn-http-ca` as the `ProtocolName` in the `ProtocolNameList`\. For more information, see [Protocols](https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html) in the *AWS IoT Developer Guide*\.   | 
|  `*.s3.amazonaws.com`  | 443 |  Used for deployments\. This format includes the `*` character, because endpoint prefixes are controlled internally and might change at any time\.  | 
|  `logs.region.amazonaws.com`  | 443 |  Required if the Greengrass core device is configured to write logs to CloudWatch\.  | 

## Configure MQTT timeouts and cache settings<a name="configure-mqtt"></a>

In the AWS IoT Greengrass environment, components can use MQTT to communicate with AWS IoT Core\. The AWS IoT Greengrass Core software manages MQTT messages for components\. When the core device loses connection to the AWS Cloud, the software caches MQTT messages to retry later when the connection restores\. You can configure settings such as message timeouts and the size of the cache\. For more information, see the `mqtt` and `mqtt.spooler` configuration parameters of the [Greengrass nucleus component](greengrass-nucleus-component.md)\.

## Configure AWS IoT Greengrass as a system service<a name="configure-system-service"></a>

You can configure the AWS IoT Greengrass Core software as a system service in your device's init system\. This enables you to do the following:
+ Start the AWS IoT Greengrass Core software when the device boots\. This is a good practice if you manage large fleets of devices\.
+ Apply over\-the\-air \(OTA\) updates to the core device's AWS IoT Greengrass Core software\. For more information, see [Update the AWS IoT Greengrass Core software \(OTA\)](update-greengrass-core-v2.md)\.
+ Enable components to restart the AWS IoT Greengrass Core software or the core device when a deployment updates the component to a new version or updates certain configuration parameters\. For more information, see the [bootstrap lifecycle step](component-recipe-reference.md#bootstrap-lifecycle-definition)\.

There are different init systems, such as initd, systemd, and SystemV\. To configure the AWS IoT Greengrass Core software as a system service on a device with systemd, run the nucleus with the `--setup-system-service true` argument\. This argument starts the nucleus as a system service and configures it to launch when the device boots\.

Then, you can use the following commands to configure starting the device on boot and to start or stop the AWS IoT Greengrass Core software\.

**To check the status of the service \(systemd\)**
+ Run the following command to check the status of the system service\.

  ```
  sudo systemctl status greengrass.service
  ```

**To enable service start on device boot \(systemd\)**
+ Run the following command to enable the nucleus to start when the device boots\.

  ```
  sudo systemctl enable greengrass.service
  ```

**To disable service start on device boot \(systemd\)**
+ Run the following command to stop the nucleus from starting when the device boots\.

  ```
  sudo systemctl disable greengrass.service
  ```

**To start the nucleus as a system service \(systemd\)**
+ Run the following command to start the AWS IoT Greengrass Core software\.

  ```
  sudo systemctl start greengrass.service
  ```

**To stop the nucleus as a system service \(systemd\)**
+ Run the following command to stop the AWS IoT Greengrass Core software\.

  ```
  sudo systemctl stop greengrass.service
  ```