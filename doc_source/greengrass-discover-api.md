# Greengrass discovery RESTful API<a name="greengrass-discover-api"></a>

AWS IoT Greengrass provides the `Discover` API operation that client devices can use to identify Greengrass core devices where they can connect\. Client devices use this data plane operation to retrieve information required to connect to Greengrass core devices where you associate them with the [BatchAssociateClientDeviceWithCoreDevice](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_BatchAssociateClientDeviceWithCoreDevice.html) API operation\. When a client device comes online, it can connect to the AWS IoT Greengrass cloud service and use the discovery API to find:
+ The IP address and port for each associated Greengrass core device\.
+ The core device CA certificate, which client devices can use to authenticate the Greengrass core device\.

**Note**  
Client devices can also use the discovery client in the AWS IoT Device SDK to discover connectivity information for Greengrass core devices\. The discovery client uses the discovery API\. For more information, see the following:  
[Test client device communications](test-client-device-communications.md)
[Greengrass Discovery RESTful API](https://docs.aws.amazon.com/greengrass/v1/developerguide/gg-discover-api.html) in the *AWS IoT Greengrass Version 1 Developer Guide*\.

To use this API operation, send HTTP requests to the discovery API on the Greengrass data plane endpoint\. This API endpoint has the following format\.

```
https://greengrass-ats.iot.region.amazonaws.com:port/greengrass/discover/thing/thing-name
```

For a list of supported AWS Regions and endpoints for the AWS IoT Greengrass discovery API, see [AWS IoT Greengrass V2 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/greengrassv2.html) in the *AWS General Reference*\. This API operation is available only on the Greengrass data plane endpoint\. The control plane endpoint that you use to manage components and deployments is different from the data plane endpoint\.

**Note**  
The discovery API is the same for AWS IoT Greengrass V1 and AWS IoT Greengrass V2\. If you have client devices that connect to an AWS IoT Greengrass V1 core, you can connect them to AWS IoT Greengrass V2 core devices without changing the code on the client devices\. For more information, see [Greengrass Discovery RESTful API](https://docs.aws.amazon.com/greengrass/v1/developerguide/gg-discover-api.html) in the *AWS IoT Greengrass Version 1 Developer Guide*\.

**Topics**
+ [Discovery authentication and authorization](#greengrass-discover-auth)
+ [Request](#greengrass-discover-request)
+ [Response](#greengrass-discover-response)
+ [Test the discovery API with cURL](#greengrass-discover-test-request)

## Discovery authentication and authorization<a name="greengrass-discover-auth"></a>

To use the discovery API to retrieve connectivity information, a client device must use TLS mutual authentication with an X\.509 client certificate to authenticate\. For more information, see [X\.509 client certificates](https://docs.aws.amazon.com/iot/latest/developerguide/x509-client-certs.html) in the *AWS IoT Core Developer Guide*\.

A client device must also have permission to perform the `greengrass:Discover` action\. The following example AWS IoT policy allows an AWS IoT thing named `MyClientDevice1` to perform `Discover` for itself\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "greengrass:Discover",
      "Resource": [
        "arn:aws:iot:us-west-2:123456789012:thing/MyClientDevice1"
      ]
    }
  ]
}
```

**Important**  
<a name="thing-policy-variable-not-supported"></a>[Thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) aren't supported for in AWS IoT policies for core devices or Greengrass data plane operations\. Instead, you can use a wildcard that matches multiple devices that have similar names\. For example, you can specify `MyGreengrassDevice*` to match `MyGreengrassDevice1`, `MyGreengrassDevice2`, and so on\. 

For more information, see [AWS IoT Core policies](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policies.html) in the *AWS IoT Core Developer Guide*\.

## Request<a name="greengrass-discover-request"></a>

The request contains the standard HTTP headers and is sent to the Greengrass discovery endpoint, as shown in the following examples\.

The port number depends on whether the core device is configured to send HTTPS traffic over port 8443 or port 443\. For more information, see [Connect on port 443 or through a network proxy](configure-greengrass-core-v2.md#configure-alpn-network-proxy)\.

**Note**  
These examples use the Amazon Trust Services \(ATS\) endpoint, which works with the recommended ATS root CA certificates\. Endpoints must match the root CA certificate type\.

Port 8443  

```
HTTP GET https://greengrass-ats.iot.region.amazonaws.com:8443/greengrass/discover/thing/thing-name
```

Port 443  

```
HTTP GET https://greengrass-ats.iot.region.amazonaws.com:443/greengrass/discover/thing/thing-name
```
Clients that connect on port 443 must implement the [Application Layer Protocol Negotiation \(ALPN\)](https://tools.ietf.org/html/rfc7301) TLS extension and pass `x-amzn-http-ca` as the `ProtocolName` in the `ProtocolNameList`\. For more information, see [Protocols](https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html) in the *AWS IoT Developer Guide*\.

## Response<a name="greengrass-discover-response"></a>

Upon success, the response header includes the HTTP 200 status code and the response body contains the discover response document\.

**Note**  
Because AWS IoT Greengrass V2 uses the same discovery API as AWS IoT Greengrass V1, the response organizes information according to AWS IoT Greengrass V1 concepts, such as Greengrass groups\. The response contains a list of Greengrass groups\. In AWS IoT Greengrass V2, each core device is in its own group, where the group contains only that core device and its connectivity information\.

### Example discover response documents<a name="greengrass-discover-response-examples"></a>

The following document shows the response for a client device that is associated to one Greengrass core device\. The core device has one endpoint and one CA certificate\.

```
{
  "GGGroups": [
    {
      "GGGroupId": "greengrassV2-coreDevice-core-device-01-thing-name",
      "Cores": [
        {
          "thingArn": "core-device-01-thing-arn",
          "Connectivity": [
            {
              "id": "core-device-01-connection-id",
              "hostAddress": "core-device-01-address",
              "portNumber": core-device-01-port,
              "metadata": "core-device-01-description"
            }
          ]
        }
      ],
      "CAs": [
        "-----BEGIN CERTIFICATE-----cert-contents-----END CERTIFICATE-----"
      ]
    }
  ]
}
```

The following document shows the response for a client device that is associated to two core devices\. The core devices have multiple endpoints and multiple group CA certificates\.

```
{
  "GGGroups": [
    {
      "GGGroupId": "greengrassV2-coreDevice-core-device-01-thing-name",
      "Cores": [
        {
          "thingArn": "core-device-01-thing-arn",
          "Connectivity": [
            {
              "id": "core-device-01-connection-id",
              "hostAddress": "core-device-01-address",
              "portNumber": core-device-01-port,
              "metadata": "core-device-01-connection-1-description"
            },
            {
              "id": "core-device-01-connection-id-2",
              "hostAddress": "core-device-01-address-2",
              "portNumber": core-device-01-port-2,
              "metadata": "core-device-01-connection-2-description"
            }
          ]
        }
      ],
      "CAs": [
        "-----BEGIN CERTIFICATE-----cert-contents-----END CERTIFICATE-----",
        "-----BEGIN CERTIFICATE-----cert-contents-----END CERTIFICATE-----",
        "-----BEGIN CERTIFICATE-----cert-contents-----END CERTIFICATE-----"
      ]
    },
    {
      "GGGroupId": "greengrassV2-coreDevice-core-device-02-thing-name",
      "Cores": [
        {
          "thingArn":"core-device-02-thing-arn",
          "Connectivity" : [
            {
              "id": "core-device-02-connection-id",
              "hostAddress": "core-device-02-address",
              "portNumber": core-device-02-port,
              "metadata": "core-device-02-connection-1-description"
            }
          ]
        }
      ],
      "CAs": [
        "-----BEGIN CERTIFICATE-----cert-contents-----END CERTIFICATE-----",
        "-----BEGIN CERTIFICATE-----cert-contents-----END CERTIFICATE-----",
        "-----BEGIN CERTIFICATE-----cert-contents-----END CERTIFICATE-----"
      ]
    }
  ]
}
```

## Test the discovery API with cURL<a name="greengrass-discover-test-request"></a>

If you have `cURL` installed, you can test the discovery API\. The following example specifies a client device's certificates to authenticate a request to the Greengrass discovery API endpoint\.

```
curl -i \
  --cert 1a23bc4d56.cert.pem \
  --key 1a23bc4d56.private.key \
  https://greengrass-ats.iot.us-west-2.amazonaws.com:8443/greengrass/discover/thing/MyClientDevice1
```

**Note**  
The `-i` argument specifies to output HTTP response headers\. You can use this option to help identify errors\.

If the request succeeds, this command outputs a response similar to the following example\.

```
{
  "GGGroups": [
    {
      "GGGroupId": "greengrassV2-coreDevice-MyGreengrassCore",
      "Cores": [
        {
          "thingArn": "arn:aws:iot:us-west-2:123456789012:thing/MyGreengrassCore",
          "Connectivity": [
            {
              "Id": "AUTOIP_192.168.1.4_1",
              "HostAddress": "192.168.1.5",
              "PortNumber": 8883,
              "Metadata": ""
            }
          ]
        }
      ],
      "CAs": [
        "-----BEGIN CERTIFICATE-----\ncert-contents\n-----END CERTIFICATE-----\n"
      ]
    }
  ]
}
```

If the command outputs an error, see [Troubleshooting Greengrass discovery issues](troubleshooting-client-devices.md#greengrass-discovery-issues)\.