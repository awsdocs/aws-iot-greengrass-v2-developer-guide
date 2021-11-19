# Component environment variable reference<a name="component-environment-variables"></a>

The AWS IoT Greengrass Core software sets environment variables when it runs lifecycle scripts for components\. You can get these environment variables in your components to get the thing name, AWS Region, and Greengrass nucleus version\. The software also sets environment variables that your component requires to use [the interprocess communication SDK](interprocess-communication.md) and to [interact with AWS services](interact-with-aws-services.md)\.

You can also set custom environment variables for your component's lifecycle scripts\. For more information, see [Setenv](component-recipe-reference.md#lifecycle-setenv-definition)\.

The AWS IoT Greengrass Core software sets the following environment variables:

`AWS_IOT_THING_NAME`  
The name of the AWS IoT thing that represents this Greengrass core device\.

`AWS_REGION`  
The AWS Region where this Greengrass core device operates\.  
The AWS SDKs use this environment variable to identify the default Region to use\. This variable is equivalent to `AWS_DEFAULT_REGION`\.

`AWS_DEFAULT_REGION`  
The AWS Region where this Greengrass core device operates\.  
The AWS CLI uses this environment variable to identify the default Region to use\. This variable is equivalent to `AWS_REGION`\.

`GGC_VERSION`  
The version of the [Greengrass nucleus component](greengrass-nucleus-component.md) that runs on this Greengrass core device\.

`AWS_GG_NUCLEUS_DOMAIN_SOCKET_FILEPATH_FOR_COMPONENT`  
The path to the IPC socket that components use to communicate with the AWS IoT Greengrass Core software\. For more information, see [Use the AWS IoT Device SDK to communicate with the Greengrass nucleus, other components, and AWS IoT CoreCommunicate with the Greengrass nucleus, other components, and AWS IoT Core](interprocess-communication.md)\.

`SVCUID`  
The secret token that components use to connect to the IPC socket and communicate with the AWS IoT Greengrass Core software\. For more information, see [Use the AWS IoT Device SDK to communicate with the Greengrass nucleus, other components, and AWS IoT CoreCommunicate with the Greengrass nucleus, other components, and AWS IoT Core](interprocess-communication.md)\.

`AWS_CONTAINER_AUTHORIZATION_TOKEN`  
The secret token that components use to retrieve credentials from the [token exchange service component](token-exchange-service-component.md)\.

`AWS_CONTAINER_CREDENTIALS_FULL_URI`  
The URI that components request to retrieve credentials from the [token exchange service component](token-exchange-service-component.md)\.