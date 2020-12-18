# Update component configurations<a name="update-component-configurations"></a>

Component configurations are JSON objects that define the parameters for each component\. Each component's recipe defines its default configuration, which you modify when you deploy components to core devices\.

When you create a deployment, you can specify the *configuration update* to apply for each component\. Configuration updates are patch operations, which means that the update modifies the component configuration that exists on the core device\. If the core device doesn't have the component, then the configuration update modifies and applies the default configuration for that deployment\.

The configuration update defines *reset* updates and *merge* updates\. Reset updates define which configuration values to reset to their defaults or remove\. Merge updates define the new configuration values to set for the component\. When you deploy a configuration update, the AWS IoT Greengrass Core software runs the reset update before the merge update\.

Components can validate the configuration updates that you deploy\. The component subscribes to receive a notification when a deployment changes its configuration, and it can reject a configuration that it doesn't support\. For more information, see [Component configuration](interprocess-communication.md#ipc-component-configuration)\.

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

Then, you apply the following configuration update, which specifies a merge update but not a reset update\. This configuration tells the component to display the dashboard on HTTP port 8080 with data from two boilers\.

```
{
  "merge": {
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

Then, apply the following configuration update to display the dashboard on the default HTTPS port with data from different boilers\.

```
{
  "reset": [
    "/network/useHttps",
    "/tags"
  ],
  "merge": {
    "tags": [
      "/boiler/3/temperature",
      "/boiler/3/pressure",
      "/boiler/4/temperature",
      "/boiler/4/pressure"
    ]
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