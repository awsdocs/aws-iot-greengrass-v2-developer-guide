# Update component configurations<a name="update-component-configurations"></a>

Component configurations are JSON objects that define the parameters for each component\. Each component's recipe defines its default configuration, which you modify when you deploy components to core devices\.

When you create a deployment, you can specify the *configuration update* to apply for each component\. Configuration updates are patch operations, which means that the update modifies the component configuration that exists on the core device\. If the core device doesn't have the component, then the configuration update modifies and applies the default configuration for that deployment\.

The configuration update defines *reset* updates and *merge* updates\. Reset updates define which configuration values to reset to their defaults or remove\. Merge updates define the new configuration values to set for the component\. When you deploy a configuration update, the AWS IoT Greengrass Core software runs the reset update before the merge update\.

Components can validate the configuration updates that you deploy\. The component subscribes to receive a notification when a deployment changes its configuration, and it can reject a configuration that it doesn't support\. For more information, see [Interact with component configuration](ipc-component-configuration.md)\.

**Topics**
+ [Reset updates](#reset-configuration-update)
+ [Merge updates](#merge-configuration-update)
+ [Examples](#configuration-update-example)

## Reset updates<a name="reset-configuration-update"></a>

Reset updates define which configuration values to reset to their default values on the core device\. If a configuration value doesn't have a default value, then the reset update removes that value from the component's configuration\. This can help you fix a component that breaks as the result of an invalid configuration\.

Use a list of JSON pointers to define which configuration values to reset\. JSON pointers start with a forward slash `/`\. To identify a value in a nested component configuration, use forward slashes \(`/`\) to separate the keys for each level in the configuration\. For more information, see the [JSON pointer specification](https://tools.ietf.org/html/rfc6901)\.

**Note**  
You can reset only an entire list to its default values\. You can't use reset updates to reset an individual element in a list\. 

To reset a component's entire configuration to its default values, specify a single empty string as the reset update\.

```
"reset": [""]
```

## Merge updates<a name="merge-configuration-update"></a>

Merge updates define the configuration values to insert into the component configuration on the core\. The merge update is a JSON object that the AWS IoT Greengrass Core software merges after it resets the values in the paths that you specify in the reset update\. When you use the AWS CLI or AWS SDKs, you must serialize this JSON object as a string\.

You can merge a key\-value pair that doesn't exist in the component's default configuration\. You can also merge a key\-value pair that has a different type than the value with the same key\. The new value replaces the old value\. This means that you can change the configuration object's structure\.

You can merge null values and empty strings, lists, and objects\.

**Note**  
You can't use merge updates for the purpose of inserting or appending an element to a list\. You can replace an entire list, or you can define an object where each element has a unique key\.  
<a name="configuration-value-type-note"></a>AWS IoT Greengrass uses JSON for configuration values\. JSON specifies a number type but doesn't differentiate between integers and floats\. As a result, configuration values might convert to floats in AWS IoT Greengrass\. To ensure that your component uses the correct data type, we recommend that you define numeric configuration values as strings\. Then, have your component parse them as integers or floats\. This ensures that your configuration values have the same type in the configuration and on your core device\.

## Examples<a name="configuration-update-example"></a>

The following example demonstrates configuration updates for a dashboard component that has the following default configuration\. This example component displays information about industrial equipment\.

```
{
  "name": null,
  "mode": "REQUEST",
  "network": {
    "useHttps": true,
    "port": {
      "http": 80,
      "https": 443
    },
  },
  "tags": []
}
```

### Industrial dashboard component recipe<a name="w875aac19c23c15c17b7b1"></a>

------
#### [ JSON ]

```
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "com.example.IndustrialDashboard",
  "ComponentVersion": "1.0.0",
  "ComponentDescription": "Displays information about industrial equipment.",
  "ComponentPublisher": "Amazon",
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "name": null,
      "mode": "REQUEST",
      "network": {
        "useHttps": true,
        "port": {
          "http": 80,
          "https": 443
        },
      },
      "tags": []
    }
  },
  "Manifests": [
    {
      "Platform": {
        "os": "linux"
      },
      "Lifecycle": {
        "Run": "python3 -u {artifacts:path}/industrial_dashboard.py"
      }
    },
    {
      "Platform": {
        "os": "windows"
      },
      "Lifecycle": {
        "Run": "py -3 -u {artifacts:path}/industrial_dashboard.py"
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
ComponentName: com.example.IndustrialDashboard
ComponentVersion: '1.0.0'
ComponentDescription: Displays information about industrial equipment.
ComponentPublisher: Amazon
ComponentConfiguration:
  DefaultConfiguration:
    name: null
    mode: REQUEST
    network:
      useHttps: true
      port:
        http: 80
        https: 443
    tags: []
Manifests:
  - Platform:
      os: linux
    Lifecycle:
      Run: |
        python3 -u {artifacts:path}/industrial_dashboard.py
  - Platform:
      os: windows
    Lifecycle:
      Run: |
        py -3 -u {artifacts:path}/industrial_dashboard.py
```

------

**Example 1: Merge update**  
You create a deployment that applies the following configuration update, which specifies a merge update but not a reset update\. This configuration update tells the component to display the dashboard on HTTP port 8080 with data from two boilers\.    
**Configuration to merge**  

```
{
  "name": "Factory 2A",
  "network": {
    "useHttps": false,
    "port": {
      "http": 8080
    }
  },
  "tags": [
    "/boiler/1/temperature",
    "/boiler/1/pressure",
    "/boiler/2/temperature",
    "/boiler/2/pressure"
  ]
}
```
The following command creates a deployment to a core device\.  

```
aws greengrassv2 create-deployment --cli-input-json file://dashboard-deployment.json
```
The `dashboard-deployment.json` file contains the following JSON document\.  

```
{
  "targetArn": "arn:aws:iot:us-west-2:123456789012:thing/MyGreengrassCore",
  "deploymentName": "Deployment for MyGreengrassCore",
  "components": {
    "com.example.IndustrialDashboard": {
      "componentVersion": "1.0.0",
      "configurationUpdate": {
        "merge": "{\"name\":\"Factory 2A\",\"network\":{\"useHttps\":false,\"port\":{\"http\":8080}},\"tags\":[\"/boiler/1/temperature\",\"/boiler/1/pressure\",\"/boiler/2/temperature\",\"/boiler/2/pressure\"]}"
      }
    }
  }
}
```
The following [Greengrass CLI](greengrass-cli-component.md) command creates a local deployment on a core device\.  

```
sudo greengrass-cli deployment create \
  --recipeDir recipes \
  --artifactDir artifacts \
  --merge "com.example.IndustrialDashboard=1.0.0" \
  --update-config dashboard-configuration.json
```
The `dashboard-configuration.json` file contains the following JSON document\.  

```
{
  "com.example.IndustrialDashboard": {
    "MERGE": {
      "name": "Factory 2A",
      "network": {
        "useHttps": false,
        "port": {
          "http": 8080
        }
      },
      "tags": [
        "/boiler/1/temperature",
        "/boiler/1/pressure",
        "/boiler/2/temperature",
        "/boiler/2/pressure"
      ]
    }
  }
}
```
After this update, the dashboard component has the following configuration\.  

```
{
  "name": "Factory 2A",
  "mode": "REQUEST",
  "network": {
    "useHttps": false,
    "port": {
      "http": 8080,
      "https": 443
    }
  },
  "tags": [
    "/boiler/1/temperature",
    "/boiler/1/pressure",
    "/boiler/2/temperature",
    "/boiler/2/pressure"
  ]
}
```

**Example 2: Reset and merge updates**  
Then, you create a deployment that applies the following configuration update, which specifies a reset update and a merge update\. These updates specify to display the dashboard on the default HTTPS port with data from different boilers\. These updates modify the configuration that results from the configuration updates in the previous example\.    
**Reset paths**  

```
[
  "/network/useHttps",
  "/tags"
]
```  
**Configuration to merge**  

```
{
  "tags": [
    "/boiler/3/temperature",
    "/boiler/3/pressure",
    "/boiler/4/temperature",
    "/boiler/4/pressure"
  ]
}
```
The following command creates a deployment to a core device\.  

```
aws greengrassv2 create-deployment --cli-input-json file://dashboard-deployment2.json
```
The `dashboard-deployment2.json` file contains the following JSON document\.  

```
{
  "targetArn": "arn:aws:iot:us-west-2:123456789012:thing/MyGreengrassCore",
  "deploymentName": "Deployment for MyGreengrassCore",
  "components": {
    "com.example.IndustrialDashboard": {
      "componentVersion": "1.0.0",
      "configurationUpdate": {
        "merge": "{\"tags\":[\"/boiler/3/temperature\",\"/boiler/3/pressure\",\"/boiler/4/temperature\",\"/boiler/4/pressure\"]}"
      }
    }
  }
}
```
The following [Greengrass CLI](greengrass-cli-component.md) command creates a local deployment on a core device\.  

```
sudo greengrass-cli deployment create \
  --recipeDir recipes \
  --artifactDir artifacts \
  --merge "com.example.IndustrialDashboard=1.0.0" \
  --update-config dashboard-configuration2.json
```
The `dashboard-configuration2.json` file contains the following JSON document\.  

```
{
  "com.example.IndustrialDashboard": {
    "RESET": [
      "/network/useHttps",
      "/tags"
    ],
    "MERGE": {
      "tags": [
        "/boiler/3/temperature",
        "/boiler/3/pressure",
        "/boiler/4/temperature",
        "/boiler/4/pressure"
      ]
    }
  }
}
```
After this update, the dashboard component has the following configuration\.  

```
{
  "name": "Factory 2A",
  "mode": "REQUEST",
  "network": {
    "useHttps": true,
    "port": {
      "http": 8080,
      "https": 443
    }
  },
  "tags": [
    "/boiler/3/temperature",
    "/boiler/3/pressure",
    "/boiler/4/temperature",
    "/boiler/4/pressure",
  ]
}
```