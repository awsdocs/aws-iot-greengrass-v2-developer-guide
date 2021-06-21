# Troubleshooting client devices<a name="troubleshooting-client-devices"></a>

Use the troubleshooting information and solutions in this section to help resolve issues with Greengrass client devices and client device components\.

**Topics**
+ [Greengrass discovery issues](#greengrass-discovery-issues)

## Greengrass discovery issues<a name="greengrass-discovery-issues"></a>

Use the following information to troubleshoot issues with Greengrass discovery\. These issues can occur when client devices use the [Greengrass discovery API](greengrass-discover-api.md) to identify a Greengrass core device to which they can connect\.

**Topics**
+ [Greengrass discovery issues \(HTTP API\)](#greengrass-discovery-http-issues)
+ [Greengrass discovery issues \(AWS IoT Device SDK v2 for Python\)](#greengrass-discovery-python-issues)
+ [Greengrass discovery issues \(AWS IoT Device SDK v2 for C\+\+\)](#greengrass-discovery-cpp-issues)
+ [Greengrass discovery issues \(AWS IoT Device SDK v2 for JavaScript\)](#greengrass-discovery-javascript-issues)
+ [Greengrass discovery issues \(AWS IoT Device SDK v2 for Java\)](#greengrass-discovery-java-issues)

### Greengrass discovery issues \(HTTP API\)<a name="greengrass-discovery-http-issues"></a>

Use the following information to troubleshoot issues with Greengrass discovery\. You might see these errors if you [test the discovery API with cURL](greengrass-discover-api.md#greengrass-discover-test-request)\.

**Topics**
+ [curl: \(52\) Empty reply from server](#greengrass-discovery-http-issue-no-response)
+ [HTTP 403: \{"message":null,"traceId":"a1b2c3d4\-5678\-90ab\-cdef\-11111EXAMPLE"\}](#greengrass-discovery-http-issue-forbidden)
+ [HTTP 404: \{"errorMessage":"The thing provided for discovery was not found"\}](#greengrass-discovery-http-issue-not-found)

#### curl: \(52\) Empty reply from server<a name="greengrass-discovery-http-issue-no-response"></a>

<a name="troubleshooting-greengrass-discovery-no-response-issue"></a>You might see this error if you specify an inactive AWS IoT certificate in the request\.

<a name="troubleshooting-greengrass-discovery-no-response-solution"></a>Check that the client device has an attached certificate, and that the certificate is active\. For more information, see [Attach a thing or policy to a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/attach-to-cert.html) and [Activate or deactivate a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/activate-or-deactivate-device-cert.html) in the *AWS IoT Core Developer Guide*\.

#### HTTP 403: \{"message":null,"traceId":"a1b2c3d4\-5678\-90ab\-cdef\-11111EXAMPLE"\}<a name="greengrass-discovery-http-issue-forbidden"></a>

<a name="troubleshooting-greengrass-discovery-forbidden-issue"></a>You might see this error if the client device doesn't have permission to call `greengrass:Discover` for itself\.

<a name="troubleshooting-greengrass-discovery-forbidden-solution"></a>Check that the client device's certificate has a policy that allows `greengrass:Discover`\. You can't use [thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) in the `Resource` section for this permission\. For more information, see [Discovery authentication and authorization](greengrass-discover-api.md#greengrass-discover-auth)\.

#### HTTP 404: \{"errorMessage":"The thing provided for discovery was not found"\}<a name="greengrass-discovery-http-issue-not-found"></a>

<a name="troubleshooting-greengrass-discovery-not-found-issue"></a>You might see this error if the client device isn't associated to any Greengrass core devices or AWS IoT Greengrass V1 groups\.

<a name="troubleshooting-greengrass-discovery-not-found-solution"></a>Check that the client device is associated to the core device to which you want it to connect\. For more information, see [Associate client devices](associate-client-devices.md)\.

### Greengrass discovery issues \(AWS IoT Device SDK v2 for Python\)<a name="greengrass-discovery-python-issues"></a>

Use the following information to troubleshoot issues with Greengrass discovery in the [AWS IoT Device SDK v2 for Python](https://github.com/aws/aws-iot-device-sdk-python-v2)\.

**Topics**
+ [awscrt\.exceptions\.AwsCrtError: AWS\_ERROR\_HTTP\_CONNECTION\_CLOSED: The connection has closed or is closing\.](#greengrass-discovery-python-issue-no-response)
+ [awsiot\.greengrass\_discovery\.DiscoveryException: \('Error during discover call: response\_code=403', 403\)](#greengrass-discovery-python-issue-forbidden)
+ [awsiot\.greengrass\_discovery\.DiscoveryException: \('Error during discover call: response\_code=404', 404\)](#greengrass-discovery-python-issue-not-found)

#### awscrt\.exceptions\.AwsCrtError: AWS\_ERROR\_HTTP\_CONNECTION\_CLOSED: The connection has closed or is closing\.<a name="greengrass-discovery-python-issue-no-response"></a>

<a name="troubleshooting-greengrass-discovery-no-response-issue"></a>You might see this error if you specify an inactive AWS IoT certificate in the request\.

<a name="troubleshooting-greengrass-discovery-no-response-solution"></a>Check that the client device has an attached certificate, and that the certificate is active\. For more information, see [Attach a thing or policy to a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/attach-to-cert.html) and [Activate or deactivate a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/activate-or-deactivate-device-cert.html) in the *AWS IoT Core Developer Guide*\.

#### awsiot\.greengrass\_discovery\.DiscoveryException: \('Error during discover call: response\_code=403', 403\)<a name="greengrass-discovery-python-issue-forbidden"></a>

<a name="troubleshooting-greengrass-discovery-forbidden-issue"></a>You might see this error if the client device doesn't have permission to call `greengrass:Discover` for itself\.

<a name="troubleshooting-greengrass-discovery-forbidden-solution"></a>Check that the client device's certificate has a policy that allows `greengrass:Discover`\. You can't use [thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) in the `Resource` section for this permission\. For more information, see [Discovery authentication and authorization](greengrass-discover-api.md#greengrass-discover-auth)\.

#### awsiot\.greengrass\_discovery\.DiscoveryException: \('Error during discover call: response\_code=404', 404\)<a name="greengrass-discovery-python-issue-not-found"></a>

<a name="troubleshooting-greengrass-discovery-not-found-issue"></a>You might see this error if the client device isn't associated to any Greengrass core devices or AWS IoT Greengrass V1 groups\.

<a name="troubleshooting-greengrass-discovery-not-found-solution"></a>Check that the client device is associated to the core device to which you want it to connect\. For more information, see [Associate client devices](associate-client-devices.md)\.

### Greengrass discovery issues \(AWS IoT Device SDK v2 for C\+\+\)<a name="greengrass-discovery-cpp-issues"></a>

Use the following information to troubleshoot issues with Greengrass discovery in the [AWS IoT Device SDK v2 for C\+\+](https://github.com/aws/aws-iot-device-sdk-cpp-v2)\.

**Topics**
+ [aws\-c\-http: AWS\_ERROR\_HTTP\_CONNECTION\_CLOSED, The connection has closed or is closing\.](#greengrass-discovery-cpp-issue-no-response)
+ [aws\-c\-common: AWS\_ERROR\_UNKNOWN, Unknown error\. \(HTTP 403\)](#greengrass-discovery-cpp-issue-forbidden)
+ [aws\-c\-common: AWS\_ERROR\_UNKNOWN, Unknown error\. \(HTTP 404\)](#greengrass-discovery-cpp-issue-not-found)

#### aws\-c\-http: AWS\_ERROR\_HTTP\_CONNECTION\_CLOSED, The connection has closed or is closing\.<a name="greengrass-discovery-cpp-issue-no-response"></a>

<a name="troubleshooting-greengrass-discovery-no-response-issue"></a>You might see this error if you specify an inactive AWS IoT certificate in the request\.

<a name="troubleshooting-greengrass-discovery-no-response-solution"></a>Check that the client device has an attached certificate, and that the certificate is active\. For more information, see [Attach a thing or policy to a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/attach-to-cert.html) and [Activate or deactivate a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/activate-or-deactivate-device-cert.html) in the *AWS IoT Core Developer Guide*\.

#### aws\-c\-common: AWS\_ERROR\_UNKNOWN, Unknown error\. \(HTTP 403\)<a name="greengrass-discovery-cpp-issue-forbidden"></a>

<a name="troubleshooting-greengrass-discovery-forbidden-issue"></a>You might see this error if the client device doesn't have permission to call `greengrass:Discover` for itself\.

<a name="troubleshooting-greengrass-discovery-forbidden-solution"></a>Check that the client device's certificate has a policy that allows `greengrass:Discover`\. You can't use [thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) in the `Resource` section for this permission\. For more information, see [Discovery authentication and authorization](greengrass-discover-api.md#greengrass-discover-auth)\.

#### aws\-c\-common: AWS\_ERROR\_UNKNOWN, Unknown error\. \(HTTP 404\)<a name="greengrass-discovery-cpp-issue-not-found"></a>

<a name="troubleshooting-greengrass-discovery-not-found-issue"></a>You might see this error if the client device isn't associated to any Greengrass core devices or AWS IoT Greengrass V1 groups\.

<a name="troubleshooting-greengrass-discovery-not-found-solution"></a>Check that the client device is associated to the core device to which you want it to connect\. For more information, see [Associate client devices](associate-client-devices.md)\.

### Greengrass discovery issues \(AWS IoT Device SDK v2 for JavaScript\)<a name="greengrass-discovery-javascript-issues"></a>

Use the following information to troubleshoot issues with Greengrass discovery in the [AWS IoT Device SDK v2 for JavaScript](https://github.com/aws/aws-iot-device-sdk-js-v2)\.

**Topics**
+ [Error: aws\-c\-http: AWS\_ERROR\_HTTP\_CONNECTION\_CLOSED, The connection has closed or is closing\.](#greengrass-discovery-javascript-issue-no-response)
+ [Error: Discovery failed \(headers: \[object Object\]\) \{ response\_code: 403 \}](#greengrass-discovery-javascript-issue-forbidden)
+ [Error: Discovery failed \(headers: \[object Object\]\) \{ response\_code: 404 \}](#greengrass-discovery-javascript-issue-not-found)
+ [Error: Discovery failed \(headers: \[object Object\]\)](#greengrass-discovery-javascript-issue-forbidden-not-found)

#### Error: aws\-c\-http: AWS\_ERROR\_HTTP\_CONNECTION\_CLOSED, The connection has closed or is closing\.<a name="greengrass-discovery-javascript-issue-no-response"></a>

<a name="troubleshooting-greengrass-discovery-no-response-issue"></a>You might see this error if you specify an inactive AWS IoT certificate in the request\.

<a name="troubleshooting-greengrass-discovery-no-response-solution"></a>Check that the client device has an attached certificate, and that the certificate is active\. For more information, see [Attach a thing or policy to a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/attach-to-cert.html) and [Activate or deactivate a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/activate-or-deactivate-device-cert.html) in the *AWS IoT Core Developer Guide*\.

#### Error: Discovery failed \(headers: \[object Object\]\) \{ response\_code: 403 \}<a name="greengrass-discovery-javascript-issue-forbidden"></a>

<a name="troubleshooting-greengrass-discovery-forbidden-issue"></a>You might see this error if the client device doesn't have permission to call `greengrass:Discover` for itself\.

<a name="troubleshooting-greengrass-discovery-forbidden-solution"></a>Check that the client device's certificate has a policy that allows `greengrass:Discover`\. You can't use [thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) in the `Resource` section for this permission\. For more information, see [Discovery authentication and authorization](greengrass-discover-api.md#greengrass-discover-auth)\.

#### Error: Discovery failed \(headers: \[object Object\]\) \{ response\_code: 404 \}<a name="greengrass-discovery-javascript-issue-not-found"></a>

<a name="troubleshooting-greengrass-discovery-not-found-issue"></a>You might see this error if the client device isn't associated to any Greengrass core devices or AWS IoT Greengrass V1 groups\.

<a name="troubleshooting-greengrass-discovery-not-found-solution"></a>Check that the client device is associated to the core device to which you want it to connect\. For more information, see [Associate client devices](associate-client-devices.md)\.

#### Error: Discovery failed \(headers: \[object Object\]\)<a name="greengrass-discovery-javascript-issue-forbidden-not-found"></a>

You might see this error \(without an HTTP response code\) when you run the Greengrass discovery sample\. This error can occur for multiple reasons\.
+ <a name="troubleshooting-greengrass-discovery-forbidden-issue"></a>You might see this error if the client device doesn't have permission to call `greengrass:Discover` for itself\.

  <a name="troubleshooting-greengrass-discovery-forbidden-solution"></a>Check that the client device's certificate has a policy that allows `greengrass:Discover`\. You can't use [thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) in the `Resource` section for this permission\. For more information, see [Discovery authentication and authorization](greengrass-discover-api.md#greengrass-discover-auth)\.
+ <a name="troubleshooting-greengrass-discovery-not-found-issue"></a>You might see this error if the client device isn't associated to any Greengrass core devices or AWS IoT Greengrass V1 groups\.

  <a name="troubleshooting-greengrass-discovery-not-found-solution"></a>Check that the client device is associated to the core device to which you want it to connect\. For more information, see [Associate client devices](associate-client-devices.md)\.

### Greengrass discovery issues \(AWS IoT Device SDK v2 for Java\)<a name="greengrass-discovery-java-issues"></a>

Use the following information to troubleshoot issues with Greengrass discovery in the [AWS IoT Device SDK v2 for Java](https://github.com/aws/aws-iot-device-sdk-java-v2)\.

**Topics**
+ [software\.amazon\.awssdk\.crt\.CrtRuntimeException: Error Getting Response Status Code from HttpStream\. \(aws\_last\_error: AWS\_ERROR\_HTTP\_DATA\_NOT\_AVAILABLE\(2062\), This data is not yet available\.\)](#greengrass-discovery-java-issue-no-response)
+ [java\.lang\.RuntimeException: Error x\-amzn\-ErrorType\(403\)](#greengrass-discovery-java-issue-forbidden)
+ [java\.lang\.RuntimeException: Error x\-amzn\-ErrorType\(404\)](#greengrass-discovery-java-issue-not-found)

#### software\.amazon\.awssdk\.crt\.CrtRuntimeException: Error Getting Response Status Code from HttpStream\. \(aws\_last\_error: AWS\_ERROR\_HTTP\_DATA\_NOT\_AVAILABLE\(2062\), This data is not yet available\.\)<a name="greengrass-discovery-java-issue-no-response"></a>

<a name="troubleshooting-greengrass-discovery-no-response-issue"></a>You might see this error if you specify an inactive AWS IoT certificate in the request\.

<a name="troubleshooting-greengrass-discovery-no-response-solution"></a>Check that the client device has an attached certificate, and that the certificate is active\. For more information, see [Attach a thing or policy to a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/attach-to-cert.html) and [Activate or deactivate a client certificate](https://docs.aws.amazon.com/iot/latest/developerguide/activate-or-deactivate-device-cert.html) in the *AWS IoT Core Developer Guide*\.

#### java\.lang\.RuntimeException: Error x\-amzn\-ErrorType\(403\)<a name="greengrass-discovery-java-issue-forbidden"></a>

<a name="troubleshooting-greengrass-discovery-forbidden-issue"></a>You might see this error if the client device doesn't have permission to call `greengrass:Discover` for itself\.

<a name="troubleshooting-greengrass-discovery-forbidden-solution"></a>Check that the client device's certificate has a policy that allows `greengrass:Discover`\. You can't use [thing policy variables](https://docs.aws.amazon.com/iot/latest/developerguide/thing-policy-variables.html) \(`iot:Connection.Thing.*`\) in the `Resource` section for this permission\. For more information, see [Discovery authentication and authorization](greengrass-discover-api.md#greengrass-discover-auth)\.

#### java\.lang\.RuntimeException: Error x\-amzn\-ErrorType\(404\)<a name="greengrass-discovery-java-issue-not-found"></a>

<a name="troubleshooting-greengrass-discovery-not-found-issue"></a>You might see this error if the client device isn't associated to any Greengrass core devices or AWS IoT Greengrass V1 groups\.

<a name="troubleshooting-greengrass-discovery-not-found-solution"></a>Check that the client device is associated to the core device to which you want it to connect\. For more information, see [Associate client devices](associate-client-devices.md)\.