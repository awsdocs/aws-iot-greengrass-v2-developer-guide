# Create custom components that use stream manager<a name="use-stream-manager-in-custom-components"></a>

Use stream manager in custom Greengrass components to store, process, and export IoT device data\. Use the procedures and examples in this section to create component recipes, artifacts, and applications that work with stream manager\. For more information about how to develop and test components, see [Create AWS IoT Greengrass components](create-components.md)\.

**Topics**
+ [Define component recipes that use stream manager](#stream-manager-recipes)
+ [Connect to stream manager in application code](#connect-to-stream-manager)

## Define component recipes that use stream manager<a name="stream-manager-recipes"></a>

To use stream manager in a custom component, you must define the `aws.greengrass.StreamManager` component as a dependency\. You must also provide the Stream Manager SDK\. Complete the following tasks to download and use the Stream Manager SDK in the language of your choice\.

### Use the Stream Manager SDK for Java<a name="use-stream-manager-sdk-java"></a>

The Stream Manager SDK for Java is available as a JAR file that you can use to compile your component\. Then, you can create an application JAR that includes the Stream Manager SDK, define the application JAR as a component artifact, and run the application JAR in the component lifecycle\.

**To use the Stream Manager SDK for Java**

1. Download the [Stream Manager SDK for Java JAR file](https://github.com/aws-greengrass/aws-greengrass-stream-manager-sdk-java/blob/main/sdk/aws-greengrass-stream-manager-sdk-java.jar)\.

1. Do one of the following to create component artifacts from your Java application and the Stream Manager SDK JAR file:
   + Build your application as a JAR file that includes the Stream Manager SDK JAR, and run this JAR file in your component recipe\.
   + Define the Stream Manager SDK JAR as a component artifact\. Add that artifact to the classpath when you run your application in your component recipe\.

   Your component recipe might look like the following example\. This component runs a modified version of the [StreamManagerS3\.java](https://github.com/aws-greengrass/aws-greengrass-stream-manager-sdk-java/blob/main/samples/StreamManagerS3/src/main/java/com/amazonaws/greengrass/examples/StreamManagerS3.java) example, where `StreamManagerS3.jar` includes the Stream Manager SDK JAR\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.StreamManagerS3Java",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "Uses stream manager to upload a file to an S3 bucket.",
     "ComponentPublisher": "Amazon",
     "ComponentDependencies": {
       "aws.greengrass.StreamManager": {
         "VersionRequirement": "^2.0.0"
       }
     },
     "Manifests": [
       {
         "Lifecycle": {
           "Run": "java -jar {artifacts:path}/StreamManagerS3.jar"
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Java/1.0.0/StreamManagerS3.jar"
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.StreamManagerS3Java
   ComponentVersion: 1.0.0
   ComponentDescription: Uses stream manager to upload a file to an S3 bucket.
   ComponentPublisher: Amazon
   ComponentDependencies:
     aws.greengrass.StreamManager:
       VersionRequirement: "^2.0.0"
   Manifests:
     - Lifecycle:
         Run: java -jar {artifacts:path}/StreamManagerS3.jar
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Java/1.0.0/StreamManagerS3.jar
   ```

------

   For more information about how to develop and test components, see [Create AWS IoT Greengrass components](create-components.md)\.

### Use the Stream Manager SDK for Python<a name="use-stream-manager-sdk-python"></a>

The Stream Manager SDK for Python is available as source code that you can include in your component\. Create a ZIP file of the Stream Manager SDK, define the ZIP file as a component artifact, and install the SDK's requirements in the component lifecycle\.

**To use the Stream Manager SDK for Python**

1. Clone or download the [aws\-greengrass\-stream\-manager\-sdk\-python](https://github.com/aws-greengrass/aws-greengrass-stream-manager-sdk-python) repository\.

   ```
   git clone git@github.com:aws-greengrass/aws-greengrass-stream-manager-sdk-python.git
   ```

1. Create a ZIP file that contains the `stream_manager` folder, which contains the source code of the Stream Manager SDK for Python\. You can provide this ZIP file as a component artifact that the AWS IoT Greengrass Core software unzips when it installs your component\. Do the following:

   1. Open the folder that contains the repository that you cloned or downloaded in the previous step\.

      ```
      cd aws-greengrass-stream-manager-sdk-python
      ```

   1. Zip the `stream_manager` folder into a ZIP file named `stream_manager_sdk.zip`\.

------
#### [ Linux or Unix ]

      ```
      zip -rv stream_manager_sdk.zip stream_manager
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      tar -acvf stream_manager_sdk.zip stream_manager
      ```

------
#### [ PowerShell ]

      ```
      Compress-Archive stream_manager stream_manager_sdk.zip
      ```

------

   1. Verify that the `stream_manager_sdk.zip` file contains the `stream_manager` folder and its contents\. Run the following command to list the contents of the ZIP file\.

------
#### [ Linux or Unix ]

      ```
      unzip -l stream_manager_sdk.zip
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      tar -tf stream_manager_sdk.zip
      ```

------

      The output should look similar to the following\.

      ```
      Archive:  aws-greengrass-stream-manager-sdk-python/stream_manager.zip
        Length      Date    Time    Name
      ---------  ---------- -----   ----
              0  02-24-2021 20:45   stream_manager/
            913  02-24-2021 20:45   stream_manager/__init__.py
           9719  02-24-2021 20:45   stream_manager/utilinternal.py
           1412  02-24-2021 20:45   stream_manager/exceptions.py
           1004  02-24-2021 20:45   stream_manager/util.py
              0  02-24-2021 20:45   stream_manager/data/
         254463  02-24-2021 20:45   stream_manager/data/__init__.py
          26515  02-24-2021 20:45   stream_manager/streammanagerclient.py
      ---------                     -------
         294026                     8 files
      ```

1. Copy the Stream Manager SDK artifacts to your component's artifacts folder\. In addition to the Stream Manager SDK ZIP file, your component uses the SDK's `requirements.txt` file to install the dependencies of the Stream Manager SDK\. Replace *\~/greengrass\-components* with the path to the folder that you use for local development\.

------
#### [ Linux or Unix ]

   ```
   cp {stream_manager_sdk.zip,requirements.txt} ~/greengrass-components/artifacts/com.example.StreamManagerS3Python/1.0.0/
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   robocopy . %USERPROFILE%\greengrass-components\artifacts\com.example.StreamManagerS3Python\1.0.0 stream_manager_sdk.zip
   robocopy . %USERPROFILE%\greengrass-components\artifacts\com.example.StreamManagerS3Python\1.0.0 requirements.txt
   ```

------
#### [ PowerShell ]

   ```
   cp .\stream_manager_sdk.zip,.\requirements.txt ~\greengrass-components\artifacts\com.example.StreamManagerS3Python\1.0.0\
   ```

------

1. Create your component recipe\. In the recipe, do the following:

   1. Define `stream_manager_sdk.zip` and `requirements.txt` as artifacts\.

   1. Define your Python application as an artifact\.

   1. In the install lifecycle, install the Stream Manager SDK requirements from `requirements.txt`\.

   1. In the run lifecycle, append the Stream Manager SDK to `PYTHONPATH`, and run your Python application\.

   Your component recipe might look like the following example\. This component runs the [stream\_manager\_s3\.py](https://github.com/aws-greengrass/aws-greengrass-stream-manager-sdk-python/blob/main/samples/stream_manager_s3.py) example\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.StreamManagerS3Python",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "Uses stream manager to upload a file to an S3 bucket.",
     "ComponentPublisher": "Amazon",
     "ComponentDependencies": {
       "aws.greengrass.StreamManager": {
         "VersionRequirement": "^2.0.0"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Install": "pip3 install --user -r {artifacts:path}/requirements.txt",
           "Run": "export PYTHONPATH=$PYTHONPATH:{artifacts:decompressedPath}/stream_manager_sdk; python3 {artifacts:path}/stream_manager_s3.py"
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/stream_manager_sdk.zip",
             "Unarchive": "ZIP"
           },
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/stream_manager_s3.py"
           },
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/requirements.txt"
           }
         ]
       },
       {
         "Platform": {
           "os": "windows"
         },
         "Lifecycle": {
           "Install": "pip3 install --user -r {artifacts:path}/requirements.txt",
           "Run": "set \"PYTHONPATH=%PYTHONPATH%;{artifacts:decompressedPath}/stream_manager_sdk\" & py -3 {artifacts:path}/stream_manager_s3.py"
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/stream_manager_sdk.zip",
             "Unarchive": "ZIP"
           },
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/stream_manager_s3.py"
           },
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/requirements.txt"
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.StreamManagerS3Python
   ComponentVersion: 1.0.0
   ComponentDescription: Uses stream manager to upload a file to an S3 bucket.
   ComponentPublisher: Amazon
   ComponentDependencies:
     aws.greengrass.StreamManager:
       VersionRequirement: "^2.0.0"
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Install: pip3 install --user -r {artifacts:path}/requirements.txt
         Run: |
           export PYTHONPATH=$PYTHONPATH:{artifacts:decompressedPath}/stream_manager_sdk
           python3 {artifacts:path}/stream_manager_s3.py
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/stream_manager_sdk.zip
           Unarchive: ZIP
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/stream_manager_s3.py
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/requirements.txt
     - Platform:
         os: windows
       Lifecycle:
         Install: pip3 install --user -r {artifacts:path}/requirements.txt
         Run: |
           set "PYTHONPATH=%PYTHONPATH%;{artifacts:decompressedPath}/stream_manager_sdk"
           py -3 {artifacts:path}/stream_manager_s3.py
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/stream_manager_sdk.zip
           Unarchive: ZIP
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/stream_manager_s3.py
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3Python/1.0.0/requirements.txt
   ```

------

   For more information about how to develop and test components, see [Create AWS IoT Greengrass components](create-components.md)\.

### Use the Stream Manager SDK for JavaScript<a name="use-stream-manager-sdk-javascript"></a>

The Stream Manager SDK for JavaScript is available as source code that you can include in your component\. Create a ZIP file of the Stream Manager SDK, define the ZIP file as a component artifact, and install the SDK in the component lifecycle\.

**To use the Stream Manager SDK for JavaScript**

1. Clone or download the [aws\-greengrass\-stream\-manager\-sdk\-js](https://github.com/aws-greengrass/aws-greengrass-stream-manager-sdk-js) repository\.

   ```
   git clone git@github.com:aws-greengrass/aws-greengrass-stream-manager-sdk-js.git
   ```

1. Create a ZIP file that contains the `aws-greengrass-stream-manager-sdk` folder, which contains the source code of the Stream Manager SDK for JavaScript\. You can provide this ZIP file as a component artifact that the AWS IoT Greengrass Core software unzips when it installs your component\. Do the following:

   1. Open the folder that contains the repository that you cloned or downloaded in the previous step\.

      ```
      cd aws-greengrass-stream-manager-sdk-js
      ```

   1. Zip the `aws-greengrass-stream-manager-sdk` folder into a ZIP file named `stream-manager-sdk.zip`\.

------
#### [ Linux or Unix ]

      ```
      zip -rv stream-manager-sdk.zip aws-greengrass-stream-manager-sdk
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      tar -acvf stream-manager-sdk.zip aws-greengrass-stream-manager-sdk
      ```

------
#### [ PowerShell ]

      ```
      Compress-Archive aws-greengrass-stream-manager-sdk stream-manager-sdk.zip
      ```

------

   1. Verify that the `stream-manager-sdk.zip` file contains the `aws-greengrass-stream-manager-sdk` folder and its contents\. Run the following command to list the contents of the ZIP file\.

------
#### [ Linux or Unix ]

      ```
      unzip -l stream-manager-sdk.zip
      ```

------
#### [ Windows Command Prompt \(CMD\) ]

      ```
      tar -tf stream-manager-sdk.zip
      ```

------

      The output should look similar to the following\.

      ```
      Archive:  stream-manager-sdk.zip
        Length      Date    Time    Name
      ---------  ---------- -----   ----
              0  02-24-2021 22:36   aws-greengrass-stream-manager-sdk/
            369  02-24-2021 22:36   aws-greengrass-stream-manager-sdk/package.json
           1017  02-24-2021 22:36   aws-greengrass-stream-manager-sdk/util.js
           8374  02-24-2021 22:36   aws-greengrass-stream-manager-sdk/utilInternal.js
           1937  02-24-2021 22:36   aws-greengrass-stream-manager-sdk/exceptions.js
              0  02-24-2021 22:36   aws-greengrass-stream-manager-sdk/data/
         353343  02-24-2021 22:36   aws-greengrass-stream-manager-sdk/data/index.js
          22599  02-24-2021 22:36   aws-greengrass-stream-manager-sdk/client.js
            216  02-24-2021 22:36   aws-greengrass-stream-manager-sdk/index.js
      ---------                     -------
         387855                     9 files
      ```

1. Copy the Stream Manager SDK artifact to your component's artifacts folder\. Replace *\~/greengrass\-components* with the path to the folder that you use for local development\.

------
#### [ Linux or Unix ]

   ```
   cp stream-manager-sdk.zip ~/greengrass-components/artifacts/com.example.StreamManagerS3JS/1.0.0/
   ```

------
#### [ Windows Command Prompt \(CMD\) ]

   ```
   robocopy . %USERPROFILE%\greengrass-components\artifacts\com.example.StreamManagerS3JS\1.0.0 stream-manager-sdk.zip
   ```

------
#### [ PowerShell ]

   ```
   cp .\stream-manager-sdk.zip ~\greengrass-components\artifacts\com.example.StreamManagerS3JS\1.0.0\
   ```

------

1. Create your component recipe\. In the recipe, do the following:

   1. Define `stream-manager-sdk.zip` as an artifact\.

   1. Define your JavaScript application as an artifact\.

   1. In the install lifecycle, install the Stream Manager SDK from the `stream-manager-sdk.zip` artifact\. This `npm install` command creates a `node_modules` folder that contains the Stream Manager SDK and its dependencies\.

   1. In the run lifecycle, append the `node_modules` folder to `NODE_PATH`, and run your JavaScript application\.

   Your component recipe might look like the following example\. This component runs the [StreamManagerS3](https://github.com/aws-greengrass/aws-greengrass-stream-manager-sdk-js/blob/main/samples/StreamManagerS3/index.js) example\.

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.example.StreamManagerS3JS",
     "ComponentVersion": "1.0.0",
     "ComponentDescription": "Uses stream manager to upload a file to an S3 bucket.",
     "ComponentPublisher": "Amazon",
     "ComponentDependencies": {
       "aws.greengrass.StreamManager": {
         "VersionRequirement": "^2.0.0"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "linux"
         },
         "Lifecycle": {
           "Install": "npm install {artifacts:decompressedPath}/stream-manager-sdk/aws-greengrass-stream-manager-sdk",
           "Run": "export NODE_PATH=$NODE_PATH:{work:path}/node_modules; node {artifacts:path}/index.js"
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3JS/1.0.0/stream-manager-sdk.zip",
             "Unarchive": "ZIP"
           },
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3JS/1.0.0/index.js"
           }
         ]
       },
       {
         "Platform": {
           "os": "windows"
         },
         "Lifecycle": {
           "Install": "npm install {artifacts:decompressedPath}/stream-manager-sdk/aws-greengrass-stream-manager-sdk",
           "Run": "set \"NODE_PATH=%NODE_PATH%;{work:path}/node_modules\" & node {artifacts:path}/index.js"
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3JS/1.0.0/stream-manager-sdk.zip",
             "Unarchive": "ZIP"
           },
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3JS/1.0.0/index.js"
           }
         ]
       }
     ]
   }
   ```

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: '2020-01-25'
   ComponentName: com.example.StreamManagerS3JS
   ComponentVersion: 1.0.0
   ComponentDescription: Uses stream manager to upload a file to an S3 bucket.
   ComponentPublisher: Amazon
   ComponentDependencies:
     aws.greengrass.StreamManager:
       VersionRequirement: "^2.0.0"
   Manifests:
     - Platform:
         os: linux
       Lifecycle:
         Install: npm install {artifacts:decompressedPath}/stream-manager-sdk/aws-greengrass-stream-manager-sdk
         Run: |
           export NODE_PATH=$NODE_PATH:{work:path}/node_modules
           node {artifacts:path}/index.js
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3JS/1.0.0/stream-manager-sdk.zip
           Unarchive: ZIP
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3JS/1.0.0/index.js
     - Platform:
         os: windows
       Lifecycle:
         Install: npm install {artifacts:decompressedPath}/stream-manager-sdk/aws-greengrass-stream-manager-sdk
         Run: |
           set "NODE_PATH=%NODE_PATH%;{work:path}/node_modules"
           node {artifacts:path}/index.js
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3JS/1.0.0/stream-manager-sdk.zip
           Unarchive: ZIP
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.StreamManagerS3JS/1.0.0/index.js
   ```

------

   For more information about how to develop and test components, see [Create AWS IoT Greengrass components](create-components.md)\.

## Connect to stream manager in application code<a name="connect-to-stream-manager"></a>

To connect to stream manager in your application, create an instance of `StreamManagerClient` from the Stream Manager SDK\. This client connects to the stream manager component on its default port 8088, or the port that you specify\. For more information about how to use `StreamManagerClient` after you create an instance, see [Use StreamManagerClient to work with streams](work-with-streams.md)\.

**Example: Connect to stream manager with default port**  

```
import com.amazonaws.greengrass.streammanager.client.StreamManagerClient;

public class MyStreamManagerComponent {

    void connectToStreamManagerWithDefaultPort() {
        StreamManagerClient client = StreamManagerClientFactory.standard().build();
        
        // Use the client.
    }
}
```

```
from stream_manager import (
    StreamManagerClient
)
              
def connect_to_stream_manager_with_default_port():
    client = StreamManagerClient()
    
    # Use the client.
```

```
const {
    StreamManagerClient
} = require('aws-greengrass-stream-manager-sdk');

function connectToStreamManagerWithDefaultPort() {
    const client = new StreamManagerClient();
    
    // Use the client.
}
```

**Example: Connect to stream manager with non\-default port**  
If you configure stream manager with a port other than the default, you must use [interprocess communication](interprocess-communication.md) to retrieve the port from the component configuration\.  
The `port` configuration parameter contains the value that you specify in `STREAM_MANAGER_SERVER_PORT` when you deploy stream manager\.

```
void connectToStreamManagerWithCustomPort() {
    EventStreamRPCConnection eventStreamRpcConnection = IPCUtils.getEventStreamRpcConnection();
    GreengrassCoreIPCClient greengrassCoreIPCClient = new GreengrassCoreIPCClient(eventStreamRpcConnection);
    List<String> keyPath = new ArrayList<>();
    keyPath.add("port");

    GetConfigurationRequest request = new GetConfigurationRequest();
    request.setComponentName("aws.greengrass.StreamManager");
    request.setKeyPath(keyPath);
    GetConfigurationResponse response =
            greengrassCoreIPCClient.getConfiguration(request, Optional.empty()).getResponse().get();
    String port = response.getValue().get("port").toString();
    System.out.print("Stream Manager is running on port: " + port);

    final StreamManagerClientConfig config = StreamManagerClientConfig.builder()
            .serverInfo(StreamManagerServerInfo.builder().port(Integer.parseInt(port)).build()).build();

    StreamManagerClient client = StreamManagerClientFactory.standard().withClientConfig(config).build();
    
    // Use the client.
}
```

```
import awsiot.greengrasscoreipc
from awsiot.greengrasscoreipc.model import (
    GetConfigurationRequest
)
from stream_manager import (
    StreamManagerClient
)

TIMEOUT = 10

def connect_to_stream_manager_with_custom_port():
    # Use IPC to get the port from the stream manager component configuration.
    ipc_client = awsiot.greengrasscoreipc.connect()
    request = GetConfigurationRequest()
    request.component_name = "aws.greengrass.StreamManager"
    request.key_path = ["port"]
    operation = ipc_client.new_get_configuration()
    operation.activate(request)
    future_response = operation.get_response()
    response = future_response.result(TIMEOUT)
    stream_manager_port = str(response.value["port"])
    
    # Use port to create a stream manager client.
    stream_client = StreamManagerClient(port=stream_manager_port)
    
    # Use the client.
```