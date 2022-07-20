# Retrieve secret values<a name="ipc-secret-manager"></a>

Use the secret manager IPC service to retrieve secret values from secrets on the core device\. You use the [secret manager component](secret-manager-component.md) to deploy encrypted secrets to core devices\. Then, you can use an IPC operation to decrypt the secret and use its value in your custom components\.

**Topics**
+ [Minimum SDK versions](#ipc-secret-manager-sdk-versions)
+ [Authorization](#ipc-secret-manager-authorization)
+ [GetSecretValue](#ipc-operation-getsecretvalue)
+ [Examples](#ipc-secret-manager-examples)

## Minimum SDK versions<a name="ipc-secret-manager-sdk-versions"></a>

The following table lists the minimum versions of the AWS IoT Device SDK that you must use to retrieve secret values from secrets on the core device\.


| SDK | Minimum version | 
| --- | --- | 
|  [AWS IoT Device SDK for Java v2](https://github.com/aws/aws-iot-device-sdk-java-v2)  |  v1\.2\.10  | 
|  [AWS IoT Device SDK for Python v2](https://github.com/aws/aws-iot-device-sdk-python-v2)  |  v1\.5\.3  | 
|  [AWS IoT Device SDK for C\+\+ v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2)  |  Linux: v1\.13\.0; Windows: v1\.14\.6  | 

## Authorization<a name="ipc-secret-manager-authorization"></a>

To use secret manager in a custom component, you must define authorization policies that allow your component to get the value of secrets that you store on the core device\. For information about defining authorization policies, see [Authorize components to perform IPC operations](interprocess-communication.md#ipc-authorization-policies)\.

Authorization policies for secret manager have the following properties\.

**IPC service identifier:** `aws.greengrass.SecretManager`


| Operation | Description | Resources | 
| --- | --- | --- | 
|  `aws.greengrass#GetSecretValue` or `*`  |  Allows a component to get the value of secrets that are encrypted on the core device\.  |  A Secrets Manager secret ARN, or `*` to allow access to all secrets\.  | 

### Authorization policy examples<a name="ipc-secret-manager-authorization-policy-examples"></a>

You can reference the following authorization policy example to help you configure authorization policies for your components\.

**Example authorization policy**  
The following example authorization policy allows a component to get the value of any secret on the core device\.  
We recommend that in a production environment, you reduce the scope of the authorization policy, so that the component retrieves only the secrets that it uses\. You can change the `*` wildcard to a list of secret ARNs when you deploy the component\.

```
{
  "accessControl": {
    "aws.greengrass.SecretManager": {
      "com.example.MySecretComponent:secrets:1": {
        "policyDescription": "Allows access to a secret.",
        "operations": [
          "aws.greengrass#GetSecretValue"
        ],
        "resources": [
          "*"
        ]
      }
    }
  }
}
```

## GetSecretValue<a name="ipc-operation-getsecretvalue"></a>

Gets the value of a secret that you store on the core device\.

This operation is similar to the Secrets Manager operation that you can use to get the value of a secret in the AWS Cloud\. For more information, see [GetSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html) in the *AWS Secrets Manager API Reference*\.

### Request<a name="ipc-operation-getsecretvalue-request"></a>

This operation's request has the following parameters:

`secretId` \(Python: `secret_id`\)  
The name of the secret to get\. You can specify either the Amazon Resource Name \(ARN\) or the friendly name of the secret\.

`versionId` \(Python: `version_id`\)  
\(Optional\) The ID of the version to get\.  
You can specify either `versionId` or `versionStage`\.  
If you don't specify `versionId` or `versionStage`, this operation defaults to the version with the `AWSCURRENT` label\.

`versionStage` \(Python: `version_stage`\)  
\(Optional\) The staging label of the version to get\.  
You can specify either `versionId` or `versionStage`\.  
If you don't specify `versionId` or `versionStage`, this operation defaults to the version with the `AWSCURRENT` label\.

### Response<a name="ipc-operation-getsecretvalue-response"></a>

This operation's response has the following information:

`secretId` \(Python: `secret_id`\)  
The ID of the secret\.

`versionId` \(Python: `version_id`\)  
The ID of this version of the secret\.

`versionStage` \(Python: `version_stage`\)  
The list of staging labels attached to this version of the secret\.

`secretValue` \(Python: `secret_value`\)  
The value of this version of the secret\. This object, `SecretValue`, contains the following information\.    
`secretString` \(Python: `secret_string`\)  
The decrypted part of the protected secret information that you provided to Secrets Manager as a string\.  
`secretBinary` \(Python: `secret_binary`\)  
\(Optional\) The decrypted part of the protected secret information that you provided to Secrets Manager as binary data in the form of a byte array\. This property contains the binary data as a base64\-encoded string\.  
This property isn't used if you created the secret in the Secrets Manager console\.

### Examples<a name="ipc-operation-getsecretvalue-examples"></a>

The following examples demonstrate how to call this operation in custom component code\.

------
#### [ Java ]

**Example: Get a secret value**  
This example uses an `IPCUtils` class to create a connection to the AWS IoT Greengrass Core IPC service\. For more information, see [Connect to the AWS IoT Greengrass Core IPC service](interprocess-communication.md#ipc-service-connect)\.

```
package com.aws.greengrass.docs.samples.ipc;

import com.aws.greengrass.docs.samples.ipc.util.IPCUtils;
import software.amazon.awssdk.aws.greengrass.GetSecretValueResponseHandler;
import software.amazon.awssdk.aws.greengrass.GreengrassCoreIPCClient;
import software.amazon.awssdk.aws.greengrass.model.GetSecretValueRequest;
import software.amazon.awssdk.aws.greengrass.model.GetSecretValueResponse;
import software.amazon.awssdk.aws.greengrass.model.UnauthorizedError;
import software.amazon.awssdk.eventstreamrpc.EventStreamRPCConnection;

import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

public class GetSecretValue {

    public static final int TIMEOUT_SECONDS = 10;

    public static void main(String[] args) {
        String secretArn = args[0];
        String versionStage = args[1];
        try (EventStreamRPCConnection eventStreamRPCConnection =
                     IPCUtils.getEventStreamRpcConnection()) {
            GreengrassCoreIPCClient ipcClient =
                    new GreengrassCoreIPCClient(eventStreamRPCConnection);
            GetSecretValueResponseHandler responseHandler =
                    GetSecretValue.getSecretValue(ipcClient, secretArn, versionStage);
            CompletableFuture<GetSecretValueResponse> futureResponse =
                    responseHandler.getResponse();
            try {
                GetSecretValueResponse response = futureResponse.get(TIMEOUT_SECONDS, TimeUnit.SECONDS);
                response.getSecretValue().postFromJson();
                String secretString = response.getSecretValue().getSecretString();
                System.out.println("Successfully retrieved secret value: " + secretString);
            } catch (TimeoutException e) {
                System.err.println("Timeout occurred while retrieving secret: " + secretArn);
            } catch (ExecutionException e) {
                if (e.getCause() instanceof UnauthorizedError) {
                    System.err.println("Unauthorized error while retrieving secret: " + secretArn);
                } else {
                    throw e;
                }
            }
        } catch (InterruptedException e) {
            System.out.println("IPC interrupted.");
        } catch (ExecutionException e) {
            System.err.println("Exception occurred when using IPC.");
            e.printStackTrace();
            System.exit(1);
        }
    }

    public static GetSecretValueResponseHandler getSecretValue(GreengrassCoreIPCClient greengrassCoreIPCClient, String secretArn, String versionStage) {
        GetSecretValueRequest getSecretValueRequest = new GetSecretValueRequest();
        getSecretValueRequest.setSecretId(secretArn);
        getSecretValueRequest.setVersionStage(versionStage);
        return greengrassCoreIPCClient.getSecretValue(getSecretValueRequest, Optional.empty());
    }
}
```

------
#### [ Python ]

**Example: Get a secret value**  
This example assumes that you are using version 1\.5\.4 or later of the AWS IoT Device SDK for Python v2\. If you are using version 1\.5\.3 of the SDK, see [Use AWS IoT Device SDK for Python v2](interprocess-communication.md#ipc-python) for information about connecting to the AWS IoT Greengrass Core IPC service\. 

```
import json

import awsiot.greengrasscoreipc
from awsiot.greengrasscoreipc.model import (
    GetSecretValueRequest,
    GetSecretValueResponse,
    UnauthorizedError
)

secret_id = 'arn:aws:secretsmanager:us-west-2:123456789012:secret:MyGreengrassSecret-abcdef'
TIMEOUT = 10

ipc_client = awsiot.greengrasscoreipc.connect()

request = GetSecretValueRequest()
request.secret_id = secret_id
request.version_stage = 'AWSCURRENT'
operation = ipc_client.new_get_secret_value()
operation.activate(request)
future_response = operation.get_response()
response = future_response.result(TIMEOUT)
secret_json = json.loads(response.secret_value.secret_string)
# Handle secret value.
```

------

## Examples<a name="ipc-secret-manager-examples"></a>

Use the following examples to learn how to use the secret manager IPC service in your components\.

### Example: Print secret \(Python\)<a name="ipc-secret-manager-example-print-secret-python"></a>

This example component prints the value of a secret that you deploy to the core device\.

**Important**  
This example component prints the value of a secret, so use it only with secrets that store test data\. Don't use this component to print the value of a secret that stores important information\.

**Topics**
+ [Recipe](#ipc-secret-manager-example-print-secret-python-recipe)
+ [Artifacts](#ipc-secret-manager-example-print-secret-python-artifacts)
+ [Usage](#ipc-secret-manager-example-print-secret-python-usage)

#### Recipe<a name="ipc-secret-manager-example-print-secret-python-recipe"></a>

The following example recipe defines a secret ARN configuration parameter and allows the component to get the value of any secret on the core device\.

**Note**  <a name="ipc-secret-manager-authorization-policy-resource-wildcard"></a>
We recommend that in a production environment, you reduce the scope of the authorization policy, so that the component retrieves only the secrets that it uses\. You can change the `*` wildcard to a list of secret ARNs when you deploy the component\.

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.PrintSecret",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "Prints the value of an AWS Secrets Manager secret.",
  "ComponentPublisher": "Amazon",
  "ComponentDependencies": {
    "aws.greengrass.SecretManager": {
      "VersionRequirement": "^2.0.0",
      "DependencyType": "HARD"
    }
  },
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "SecretArn": "",
      "accessControl": {
        "aws.greengrass.SecretManager": {
          "com.example.PrintSecret:secrets:1": {
            "policyDescription": "Allows access to a secret.",
            "operations": [
              "aws.greengrass#GetSecretValue"
            ],
            "resources": [
              "*"
            ]
          }
        }
      }
    }
  },
  "Manifests": [
    {
      "Platform": {
        "os": "linux"
      },
      "Lifecycle": {
        "Install": "python3 -m pip install --user awsiotsdk",
        "Run": "python3 -u {artifacts:path}/print_secret.py \"{configuration:/SecretArn}\""
      }
    },
    {
      "Platform": {
        "os": "windows"
      },
      "Lifecycle": {
        "Install": "py -3 -m pip install --user awsiotsdk",
        "Run": "py -3 -u {artifacts:path}/print_secret.py \"{configuration:/SecretArn}\""
      }
    }
  ]
}
```

------
#### [ YAML ]

```
---
RecipeFormatVersion: '2020-01-25'
ComponentName: com.example.PrintSecret
ComponentVersion: 1.0.0
ComponentDescription: Prints the value of a Secrets Manager secret.
ComponentPublisher: Amazon
ComponentDependencies:
  aws.greengrass.SecretManager:
    VersionRequirement: "^2.0.0"
    DependencyType: HARD
ComponentConfiguration:
  DefaultConfiguration:
    SecretArn: ''
    accessControl:
      aws.greengrass.SecretManager:
        com.example.PrintSecret:secrets:1:
          policyDescription: Allows access to a secret.
          operations:
            - aws.greengrass#GetSecretValue
          resources:
            - "*"
Manifests:
  - Platform:
      os: linux
    Lifecycle:
      Install: python3 -m pip install --user awsiotsdk
      Run: python3 -u {artifacts:path}/print_secret.py "{configuration:/SecretArn}"
  - Platform:
      os: windows
    Lifecycle:
      Install: py -3 -m pip install --user awsiotsdk
      Run: py -3 -u {artifacts:path}/print_secret.py "{configuration:/SecretArn}"
```

------

#### Artifacts<a name="ipc-secret-manager-example-print-secret-python-artifacts"></a>

The following example Python application demonstrates how to use the secret manager IPC service to get the value of a secret on the core device\.

```
import concurrent.futures
import json
import sys
import traceback

import awsiot.greengrasscoreipc
from awsiot.greengrasscoreipc.model import (
    GetSecretValueRequest,
    GetSecretValueResponse,
    UnauthorizedError
)

TIMEOUT = 10

if len(sys.argv) == 1:
    print('Provide SecretArn in the component configuration.', file=sys.stdout)
    exit(1)

secret_id = sys.argv[1]

try:
    ipc_client = awsiot.greengrasscoreipc.connect()

    request = GetSecretValueRequest()
    request.secret_id = secret_id
    operation = ipc_client.new_get_secret_value()
    operation.activate(request)
    future_response = operation.get_response()

    try:
        response = future_response.result(TIMEOUT)
        secret_json = json.loads(response.secret_value.secret_string)
        print('Successfully got secret: ' + secret_id)
        print('Secret value: ' + str(secret_json))
    except concurrent.futures.TimeoutError:
        print('Timeout occurred while getting secret: ' + secret_id, file=sys.stderr)
    except UnauthorizedError as e:
        print('Unauthorized error while getting secret: ' + secret_id, file=sys.stderr)
        raise e
    except Exception as e:
        print('Exception while getting secret: ' + secret_id, file=sys.stderr)
        raise e
except Exception:
    print('Exception occurred when using IPC.', file=sys.stderr)
    traceback.print_exc()
    exit(1)
```

#### Usage<a name="ipc-secret-manager-example-print-secret-python-usage"></a>

You can use this example component with the [secret manager component](secret-manager-component.md) to deploy and print the value of a secret on your core device\.

**To create, deploy, and print a test secret**

1. Create a Secrets Manager secret with test data\.

------
#### [ Linux or Unix ]

   ```
   aws secretsmanager create-secret \
     --name MyTestGreengrassSecret \
     --secret-string '{"my-secret-key": "my-secret-value"}'
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   aws secretsmanager create-secret ^
     --name MyTestGreengrassSecret ^
     --secret-string '{"my-secret-key": "my-secret-value"}'
   ```

------
#### [ PowerShell ]

   ```
   aws secretsmanager create-secret `
     --name MyTestGreengrassSecret `
     --secret-string '{"my-secret-key": "my-secret-value"}'
   ```

------

   Save the ARN of the secret to use in the following steps\.

   For more information, see [Creating a secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) in the *AWS Secrets Manager User Guide*\.

1. Deploy the [secret manager component](secret-manager-component.md) \(`aws.greengrass.SecretManager`\) with the following configuration merge update\. Specify the ARN of the secret that you created earlier\.

   ```
   {
     "cloudSecrets": [
       {
         "arn": "arn:aws:secretsmanager:us-west-2:123456789012:secret:MyTestGreengrassSecret-abcdef"
       }
     ]
   }
   ```

   For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md) or the [Greengrass CLI deployment command](gg-cli-deployment.md)\.

1. Create and deploy the example component in this section with the following configuration merge update\. Specify the ARN of the secret that you created earlier\.

   ```
   {
     "SecretArn": "arn:aws:secretsmanager:us-west-2:123456789012:secret:MyTestGreengrassSecret",
     "accessControl": {
       "aws.greengrass.SecretManager": {
         "com.example.PrintSecret:secrets:1": {
           "policyDescription": "Allows access to a secret.",
           "operations": [
             "aws.greengrass#GetSecretValue"
           ],
           "resources": [
             "arn:aws:secretsmanager:us-west-2:123456789012:secret:MyTestGreengrassSecret-abcdef"
           ]
         }
       }
     }
   }
   ```

   For more information, see [Create AWS IoT Greengrass components](create-components.md)

1. View the AWS IoT Greengrass Core software logs to verify that the deployments succeed, and view the `com.example.PrintSecret` component log to see the secret value printed\. For more information, see [Monitor AWS IoT Greengrass logs](monitor-logs.md)\.