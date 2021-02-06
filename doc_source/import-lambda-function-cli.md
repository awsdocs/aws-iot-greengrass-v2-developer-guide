# Import a Lambda function as a component \(AWS CLI\)<a name="import-lambda-function-cli"></a>

Use the [CreateComponentVersion](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateComponentVersion.html) operation to create components from Lambda functions\. When you call this operation, specify `lambdaFunction` to import a Lambda function\. 

**Topics**
+ [Step 1: Define the Lambda function configuration](#create-lambda-function-configuration-cli)
+ [Step 2: Create the Lambda function component](#create-lambda-component-cli)

## Step 1: Define the Lambda function configuration<a name="create-lambda-function-configuration-cli"></a>

1. Create a file called `lambda-function-component.json`, and then copy the following JSON object into the file\. Replace the `lambdaArn` with the ARN of the Lambda function to import\.

   ```
   {
     "lambdaFunction": {
       "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1"
     }
   }
   ```
**Important**  
You must specify an ARN that includes the version of the function to import\. You can't use version aliases like `$LATEST`\.

1. \(Optional\) Specify the name \(`componentName`\) of the component\. If you omit this parameter, AWS IoT Greengrass creates the component with the name of the Lambda function\.

   ```
   {
     "lambdaFunction": {
       "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
       "componentName": "com.example.HelloWorldLambda"
     }
   }
   ```

1. \(Optional\) Specify the version \(`componentVersion`\) for the component\. If you omit this parameter, AWS IoT Greengrass creates the component with the version of the Lambda function as a valid semantic version\. For example, if your function version is `3`, the component version becomes `3.0.0`\.
**Note**  
<a name="component-version-uniqueness-para"></a>Each component version that you upload must be unique\. You can't edit a component version after you upload it\.  
<a name="semver-para"></a>AWS IoT Greengrass uses semantic versions for components\. Semantic versions follow a *major*\.*minor*\.*patch* number system\. For example, version `1.0.0` represents the first major release for a component\. For more information, see the [semantic version specification](https://semver.org/)\.

   ```
   {
     "lambdaFunction": {
       "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
       "componentName": "com.example.HelloWorldLambda",
       "componentVersion": "1.0.0"
     }
   }
   ```

1. \(Optional\) Specify the platforms that this Lambda function supports\. Each platform contains a map of attributes that identify a platform\. All core devices have attributes for operating system \(`os`\) and architecture \(`architecture`\)\. The AWS IoT Greengrass Core software may add other platform attributes\. You can also specify custom platform attributes when you deploy the [Greengrass nucleus component](greengrass-nucleus-component.md) to a core device\. Do the following:

   1. Add a list of platforms \(`componentPlatforms`\) to the Lambda function in `lambda-function-component.json`\.

      ```
      {
        "lambdaFunction": {
          "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
          "componentName": "com.example.HelloWorldLambda",
          "componentVersion": "1.0.0",
          "componentPlatforms": [
          
          ]
        }
      }
      ```

   1. Add each supported platform to the list\. Each platform has a friendly `name` to identify it and a map of attributes\. The following example specifies that this function supports x86 devices that run Linux\.

      ```
      {
        "name": "Linux x86",
        "attributes": {
          "os": "linux",
          "architecture": "x86"
        }
      }
      ```

      Your `lambda-function-component.json` might contain a document similar to the following example\.

      ```
      {
        "lambdaFunction": {
          "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
          "componentName": "com.example.HelloWorldLambda",
          "componentVersion": "1.0.0",
          "componentPlatforms": [
            {
              "name": "Linux x86",
              "attributes": {
                "os": "linux",
                "architecture": "x86"
              }
            }
          ]
        }
      }
      ```

1. \(Optional\) Specify the component dependencies for your Lambda function\. When you deploy the Lambda function component, the deployment includes these dependencies for your function to run\.
**Important**  <a name="import-v1-lambda-note"></a>
To import a Lambda function that you created to run on AWS IoT Greengrass V1, you must define individual component dependencies for the features that your function uses, such as subscriptions, secrets, and stream manager\. Define these components as [hard dependencies](component-recipe-reference.md) so that your Lambda function component restarts if the dependency changes state\. For more information, see [Run V1 Lambda functions](move-from-v1.md#run-v1-lambda-functions)\.

   Do the following:

   1. Add a map of component dependencies \(`componentDependencies`\) to the Lambda function in `lambda-function-component.json`\.

      ```
      {
        "lambdaFunction": {
          "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
          "componentName": "com.example.HelloWorldLambda",
          "componentVersion": "1.0.0",
          "componentPlatforms": [
            {
              "name": "Linux x86",
              "attributes": {
                "os": "linux",
                "architecture": "x86"
              }
            }
          ],
          "componentDependencies": {
            
          }
        }
      }
      ```

   1. Add each component dependency to the map\. Specify the component name as the key and specify an object with the following parameters:
      + `versionRequirement` – The semantic version constraint that identifies the compatible versions of the component dependency\. You can specify a single version or a range of versions\. For more information about semantic version constraints, see the [npm semver calculator](https://semver.npmjs.com/)\.
      + `dependencyType` – \(Optional\) The type of the dependency\. Choose from the following:
        + `SOFT` – The Lambda function component doesn't restart if the dependency changes state\.
        + `HARD` – The Lambda function component restarts if the dependency changes state\.

        The default is `HARD`\.

      The following example specifies that this Lambda function depends on any version in the first major version of the [stream manager component](stream-manager-component.md)\. The Lambda function component restarts when stream manager restarts or updates\.

      ```
      {
        "aws.greengrass.StreamManager": {
          "versionRequirement": "^1.0.0",
          "dependencyType": "HARD"
        }
      }
      ```

      Your `lambda-function-component.json` might contain a document similar to the following example\.

      ```
      {
        "lambdaFunction": {
          "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
          "componentName": "com.example.HelloWorldLambda",
          "componentVersion": "1.0.0",
          "componentPlatforms": [
            {
              "name": "Linux x86",
              "attributes": {
                "os": "linux",
                "architecture": "x86"
              }
            }
          ],
          "componentDependencies": {
            {
              "aws.greengrass.StreamManager": {
                "versionRequirement": "^1.0.0",
                "dependencyType": "HARD"
              }
            }
          }
        }
      }
      ```

1. \(Optional\) Configure the Lambda function parameters to use to run the function\. You can configure options such environment variables, message event sources, timeouts, and container settings\. Do the following:

   1. Add the Lambda parameters object \(`componentLambdaParameters`\) to the Lambda function in `lambda-function-component.json`\.

      ```
      {
        "lambdaFunction": {
          "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
          "componentName": "com.example.HelloWorldLambda",
          "componentVersion": "1.0.0",
          "componentPlatforms": [
            {
              "name": "Linux x86",
              "attributes": {
                "os": "linux",
                "architecture": "x86"
              }
            }
          ],
          "componentDependencies": {
            {
              "aws.greengrass.StreamManager": {
                "versionRequirement": "^1.0.0",
                "dependencyType": "HARD"
              }
            }
          },
          "componentLambdaParameters": {
          
          }
        }
      }
      ```

   1. \(Optional\) Specify the event sources to which the Lambda function subscribes for work messages\. The Lambda function is called when it receives a message from an event source\. You can subscribe this function to local publish/subscribe messages and AWS IoT Core MQTT messages\. Do the following:

      1. Add the list of event sources \(`eventSources`\) to the Lambda function parameters\.

         ```
         {
           "lambdaFunction": {
             "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
             "componentName": "com.example.HelloWorldLambda",
             "componentVersion": "1.0.0",
             "componentPlatforms": [
               {
                 "name": "Linux x86",
                 "attributes": {
                   "os": "linux",
                   "architecture": "x86"
                 }
               }
             ],
             "componentDependencies": {
               {
                 "aws.greengrass.StreamManager": {
                   "versionRequirement": "^1.0.0",
                   "dependencyType": "HARD"
                 }
               }
             },
             "componentLambdaParameters": {
               "eventSources": [
               
               ]
             }
           }
         }
         ```

      1. Add each event source to the list\. Each event source has the following parameters:
         + `topic` – The topic to subscribe for messages\. This topic supports MQTT topic wildcards \(`+` and `#`\) when you subscribe to AWS IoT Core MQTT topics\.
         + `type` – The type of event source\. Choose from the following:
           + `PUB_SUB` – Subscribe to local publish/subscribe messages\.
           + `IOT_CORE` – Subscribe to AWS IoT Core MQTT messages\.

           The following example subscribes to AWS IoT Core MQTT on topics that match the `hello/world/+` topic filter\.

           ```
           {
             "topic": "hello/world/+",
             "type": "IOT_CORE"
           }
           ```

           Your `lambda-function-component.json` might look similar to the following example\.

           ```
           {
             "lambdaFunction": {
               "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
               "componentName": "com.example.HelloWorldLambda",
               "componentVersion": "1.0.0",
               "componentPlatforms": [
                 {
                   "name": "Linux x86",
                   "attributes": {
                     "os": "linux",
                     "architecture": "x86"
                   }
                 }
               ],
               "componentDependencies": {
                 {
                   "aws.greengrass.StreamManager": {
                     "versionRequirement": "^1.0.0",
                     "dependencyType": "HARD"
                   }
                 }
               },
               "componentLambdaParameters": {
                 "eventSources": [
                   {
                     "topic": "hello/world/+",
                     "type": "IOT_CORE"
                   }
                 ]
               }
             }
           }
           ```

   1. \(Optional\) Specify any of the following parameters in the Lambda function parameters object:
      + `environmentVariables` – The map of environment variables that are available to the Lambda function when it runs\.
      + `execArgs` – The list of arguments to pass to the Lambda function when it runs\.
      + `inputPayloadEncodingType` – The type of payload that the Lambda function supports\. Choose from the following options:
        + `json`
        + `binary`

        Default: `json`
      + `pinned` – Whether or not the Lambda function is pinned\. The default is `true`\.<a name="lambda-function-lifecycle-type"></a>
        + A pinned \(or long\-lived\) Lambda function starts when AWS IoT Greengrass starts and keeps running in its own container\.
        + A non\-pinned \(or on\-demand\) Lambda function starts only when it receives a work item and exits after it remains idle for a specified maximum idle time\. If the function has multiple work items, the AWS IoT Greengrass Core software creates multiple instances of the function\.

        Use `maxIdleTimeInSeconds` to set the maximum idle time for your function\.
      + `timeoutInSeconds` – The maximum amount of time in seconds that the Lambda function can run before it times out\. The default is 3 seconds\.
      + `statusTimeoutInSeconds` – The interval in seconds at which the Lambda function component sends status updates to the Lambda manager component\. This parameter applies only to pinned functions\. The default is 60 seconds\.
      + `maxIdleTimeInSeconds` – The maximum amount of time in seconds that a non\-pinned Lambda function can idle before the AWS IoT Greengrass Core software stops its process\. The default is 60 seconds\.
      + `maxInstancesCount` – The maximum number of instances that a non\-pinned Lambda function can run at the same time\. The default is 100 instances\.
      + `maxQueueSize` – The maximum size of the message queue for the Lambda function component\. The AWS IoT Greengrass Core software stores messages in a FIFO \(first\-in\-first\-out\) queue until it can run the Lambda function to consume each message\. The default is 1,000 messages\.

      Your `lambda-function-component.json` might contain a document similar to the following example\.

      ```
      {
        "lambdaFunction": {
          "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
          "componentName": "com.example.HelloWorldLambda",
          "componentVersion": "1.0.0",
          "componentPlatforms": [
            {
              "name": "Linux x86",
              "attributes": {
                "os": "linux",
                "architecture": "x86"
              }
            }
          ],
          "componentDependencies": {
            {
              "aws.greengrass.StreamManager": {
                "versionRequirement": "^1.0.0",
                "dependencyType": "HARD"
              }
            }
          },
          "componentLambdaParameters": {
            "eventSources": [
              {
                "topic": "hello/world/+",
                "type": "IOT_CORE"
              }
            ],
            "environmentVariables": {
              "LIMIT": "300"
            },
            "execArgs": [
              "-d"
            ],
            "inputPayloadEncodingType": "json",
            "pinned": true,
            "timeoutInSeconds": 120,
            "statusTimeoutInSeconds": 30,
            "maxIdleTimeInSeconds": 30,
            "maxInstancesCount": 50,
            "maxQueueSize": 500
          }
        }
      }
      ```

   1. \(Optional\) Configure the container settings for the Lambda function\. By default, Lambda functions run in an isolated runtime environment inside the AWS IoT Greengrass Core software\. You can also choose to run the Lambda function as a process without any isolation\. If you run the Lambda function in a container, you configure the memory size of the container and what system resources are available to the Lambda function\. Do the following:

      1. Add the Linux process parameters object \(`linuxProcessParams`\) to the Lambda parameters object in `lambda-function-component.json`\.

         ```
         {
           "lambdaFunction": {
             "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
             "componentName": "com.example.HelloWorldLambda",
             "componentVersion": "1.0.0",
             "componentPlatforms": [
               {
                 "name": "Linux x86",
                 "attributes": {
                   "os": "linux",
                   "architecture": "x86"
                 }
               }
             ],
             "componentDependencies": {
               {
                 "aws.greengrass.StreamManager": {
                   "versionRequirement": "^1.0.0",
                   "dependencyType": "HARD"
                 }
               }
             },
             "componentLambdaParameters": {
               "eventSources": [
                 {
                   "topic": "hello/world/+",
                   "type": "IOT_CORE"
                 }
               ],
               "environmentVariables": {
                 "LIMIT": "300"
               },
               "execArgs": [
                 "-d"
               ],
               "inputPayloadEncodingType": "json",
               "pinned": true,
               "timeoutInSeconds": 120,
               "statusTimeoutInSeconds": 30,
               "maxIdleTimeInSeconds": 30,
               "maxInstancesCount": 50,
               "maxQueueSize": 500,
               "linuxProcessParams": {
               
               }
             }
           }
         }
         ```

      1. \(Optional\) Specify whether or not the Lambda function runs in a container\. Add the `isolationMode` parameter to the process parameters object, and choose from the following options:
         + `GreengrassContainer` – The Lambda function runs in a container\.
         + `NoContainer` – The Lambda function runs as a process without any isolation\.

         The default is `GreengrassContainer`\.

      1. \(Optional\) If you run the Lambda function in a container, you can configure the amount of memory and the system resources, such as volumes and devices, to make available to the container\. Do the following:

         1. Add the container parameters object \(`containerParams`\) to the Linux process parameters object in `lambda-function-component.json`\.

            ```
            {
              "lambdaFunction": {
                "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
                "componentName": "com.example.HelloWorldLambda",
                "componentVersion": "1.0.0",
                "componentPlatforms": [
                  {
                    "name": "Linux x86",
                    "attributes": {
                      "os": "linux",
                      "architecture": "x86"
                    }
                  }
                ],
                "componentDependencies": {
                  {
                    "aws.greengrass.StreamManager": {
                      "versionRequirement": "^1.0.0",
                      "dependencyType": "HARD"
                    }
                  }
                },
                "componentLambdaParameters": {
                  "eventSources": [
                    {
                      "topic": "hello/world/+",
                      "type": "IOT_CORE"
                    }
                  ],
                  "environmentVariables": {
                    "LIMIT": "300"
                  },
                  "execArgs": [
                    "-d"
                  ],
                  "inputPayloadEncodingType": "json",
                  "pinned": true,
                  "timeoutInSeconds": 120,
                  "statusTimeoutInSeconds": 30,
                  "maxIdleTimeInSeconds": 30,
                  "maxInstancesCount": 50,
                  "maxQueueSize": 500,
                  "linuxProcessParams": {
                    "containerParams": {
                    
                    }
                  }
                }
              }
            }
            ```

         1. \(Optional\) Add the `memorySizeInKB` parameter to specify the memory size of the container\. The default is 16,384 KB \(16 MB\)\.

         1. \(Optional\) Add the `mountROSysfs` parameter to specify whether or not the container can read information from the device's `/sys` folder\. The default is `false`\.

         1. \(Optional\) Configure the local volumes that the containerized Lambda function can access\. When you define a volume, the AWS IoT Greengrass Core software mounts the source files to the destination inside the container\. Do the following:

            1. Add the list of volumes \(`volumes`\) to the container parameters\.

               ```
               {
                 "lambdaFunction": {
                   "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
                   "componentName": "com.example.HelloWorldLambda",
                   "componentVersion": "1.0.0",
                   "componentPlatforms": [
                     {
                       "name": "Linux x86",
                       "attributes": {
                         "os": "linux",
                         "architecture": "x86"
                       }
                     }
                   ],
                   "componentDependencies": {
                     {
                       "aws.greengrass.StreamManager": {
                         "versionRequirement": "^1.0.0",
                         "dependencyType": "HARD"
                       }
                     }
                   },
                   "componentLambdaParameters": {
                     "eventSources": [
                       {
                         "topic": "hello/world/+",
                         "type": "IOT_CORE"
                       }
                     ],
                     "environmentVariables": {
                       "LIMIT": "300"
                     },
                     "execArgs": [
                       "-d"
                     ],
                     "inputPayloadEncodingType": "json",
                     "pinned": true,
                     "timeoutInSeconds": 120,
                     "statusTimeoutInSeconds": 30,
                     "maxIdleTimeInSeconds": 30,
                     "maxInstancesCount": 50,
                     "maxQueueSize": 500,
                     "linuxProcessParams": {
                       "containerParams": {
                         "memorySizeInKB": 32768,
                         "mountROSysfs": true,
                         "volumes": [
                         
                         ]
                       }
                     }
                   }
                 }
               }
               ```

            1. Add each volume to the list\. Each volume has the following parameters:
               + `sourcePath` – The path to the source folder on the core device\.
               + `destinationPath` – The path to the destination folder in the container\.
               + `permission` – \(Optional\) The permission to access the source folder from the container\. Choose from the following options:
                 + `ro` – The Lambda function has read\-only access to the source folder\.
                 + `rw` – The Lambda function has read\-write access to the source folder\.

                 The default is `ro`\.
               + `addGroupOwner` – \(Optional\) Whether or not to add the system group that runs the Lambda function component as an owner of the source folder\. The default is `false`\.

               Your `lambda-function-component.json` might contain a document similar to the following example\.

               ```
               {
                 "lambdaFunction": {
                   "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
                   "componentName": "com.example.HelloWorldLambda",
                   "componentVersion": "1.0.0",
                   "componentPlatforms": [
                     {
                       "name": "Linux x86",
                       "attributes": {
                         "os": "linux",
                         "architecture": "x86"
                       }
                     }
                   ],
                   "componentDependencies": {
                     {
                       "aws.greengrass.StreamManager": {
                         "versionRequirement": "^1.0.0",
                         "dependencyType": "HARD"
                       }
                     }
                   },
                   "componentLambdaParameters": {
                     "eventSources": [
                       {
                         "topic": "hello/world/+",
                         "type": "IOT_CORE"
                       }
                     ],
                     "environmentVariables": {
                       "LIMIT": "300"
                     },
                     "execArgs": [
                       "-d"
                     ],
                     "inputPayloadEncodingType": "json",
                     "pinned": true,
                     "timeoutInSeconds": 120,
                     "statusTimeoutInSeconds": 30,
                     "maxIdleTimeInSeconds": 30,
                     "maxInstancesCount": 50,
                     "maxQueueSize": 500,
                     "linuxProcessParams": {
                       "containerParams": {
                         "memorySizeInKB": 32768,
                         "mountROSysfs": true,
                         "volumes": [
                           {
                             "sourcePath": "/var/data/src",
                             "destinationPath": "/var/data/dest",
                             "permission": "rw",
                             "addGroupOwner": true
                           }
                         ]
                       }
                     }
                   }
                 }
               }
               ```

         1. \(Optional\) Configure the local system devices that the containerized Lambda function can access\. Do the following:

            1. Add the list of system devices \(`devices`\) to the container parameters\.

               ```
               {
                 "lambdaFunction": {
                   "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
                   "componentName": "com.example.HelloWorldLambda",
                   "componentVersion": "1.0.0",
                   "componentPlatforms": [
                     {
                       "name": "Linux x86",
                       "attributes": {
                         "os": "linux",
                         "architecture": "x86"
                       }
                     }
                   ],
                   "componentDependencies": {
                     {
                       "aws.greengrass.StreamManager": {
                         "versionRequirement": "^1.0.0",
                         "dependencyType": "HARD"
                       }
                     }
                   },
                   "componentLambdaParameters": {
                     "eventSources": [
                       {
                         "topic": "hello/world/+",
                         "type": "IOT_CORE"
                       }
                     ],
                     "environmentVariables": {
                       "LIMIT": "300"
                     },
                     "execArgs": [
                       "-d"
                     ],
                     "inputPayloadEncodingType": "json",
                     "pinned": true,
                     "timeoutInSeconds": 120,
                     "statusTimeoutInSeconds": 30,
                     "maxIdleTimeInSeconds": 30,
                     "maxInstancesCount": 50,
                     "maxQueueSize": 500,
                     "linuxProcessParams": {
                       "containerParams": {
                         "memorySizeInKB": 32768,
                         "mountROSysfs": true,
                         "volumes": [
                           {
                             "sourcePath": "/var/data/src",
                             "destinationPath": "/var/data/dest",
                             "permission": "rw",
                             "addGroupOwner": true
                           }
                         ],
                         "devices": [
                         
                         ]
                       }
                     }
                   }
                 }
               }
               ```

            1. Add each system device to the list\. Each system device has the following parameters:
               + `path` – The path to the system device on the core device\.
               + `permission` – \(Optional\) The permission to access the system device from the container\. Choose from the following options:
                 + `ro` – The Lambda function has read\-only access to the system device\.
                 + `rw` – The Lambda function has read\-write access to the system device\.

                 The default is `ro`\.
               + `addGroupOwner` – \(Optional\) Whether or not to add the system group that runs the Lambda function component as an owner of the system device\. The default is `false`\.

            Your `lambda-function-component.json` might contain a document similar to the following example\.

            ```
            {
              "lambdaFunction": {
                "lambdaArn": "arn:aws:lambda:region:account-id:function:HelloWorld:1",
                "componentName": "com.example.HelloWorldLambda",
                "componentVersion": "1.0.0",
                "componentPlatforms": [
                  {
                    "name": "Linux x86",
                    "attributes": {
                      "os": "linux",
                      "architecture": "x86"
                    }
                  }
                ],
                "componentDependencies": {
                  {
                    "aws.greengrass.StreamManager": {
                      "versionRequirement": "^1.0.0",
                      "dependencyType": "HARD"
                    }
                  }
                },
                "componentLambdaParameters": {
                  "eventSources": [
                    {
                      "topic": "hello/world/+",
                      "type": "IOT_CORE"
                    }
                  ],
                  "environmentVariables": {
                    "LIMIT": "300"
                  },
                  "execArgs": [
                    "-d"
                  ],
                  "inputPayloadEncodingType": "json",
                  "pinned": true,
                  "timeoutInSeconds": 120,
                  "statusTimeoutInSeconds": 30,
                  "maxIdleTimeInSeconds": 30,
                  "maxInstancesCount": 50,
                  "maxQueueSize": 500,
                  "linuxProcessParams": {
                    "containerParams": {
                      "memorySizeInKB": 32768,
                      "mountROSysfs": true,
                      "volumes": [
                        {
                          "sourcePath": "/var/data/src",
                          "destinationPath": "/var/data/dest",
                          "permission": "rw",
                          "addGroupOwner": true
                        }
                      ],
                      "devices": [
                        {
                          "path": "/dev/sda3",
                          "permission": "rw",
                          "addGroupOwner": true
                        }
                      ]
                    }
                  }
                }
              }
            }
            ```

1. \(Optional\) Add tags \(`tags`\) for the component\. For more information, see [Tag your AWS IoT Greengrass Version 2 resources](tag-resources.md)\.

## Step 2: Create the Lambda function component<a name="create-lambda-component-cli"></a>

1. Run the following command to create the Lambda function component from `lambda-function-component.json`\.

   ```
   aws greengrassv2 create-component-version --cli-input-json file://lambda-function-component.json
   ```

   The response looks similar to the following example if the request succeeds\.

   ```
   {
     "arn": "arn:aws:greengrass:region:123456789012:components:com.example.HelloWorldLambda:versions:1.0.0",
     "componentName": "com.example.HelloWorldLambda",
     "componentVersion": "1.0.0",
     "creationTimestamp": "Mon Dec 15 20:56:34 UTC 2020",
     "status": {
       "componentState": "REQUESTED",
       "message": "NONE",
       "errors": {}
     }
   }
   ```

   Copy the `arn` from the output to check the state of the component in the next step\.

1. When you create a component, its state is `REQUESTED`\. Then, AWS IoT Greengrass validates that the component is deployable\. You can run the following command to query the component status and verify that your component is deployable\. Replace the `arn` with the ARN from the previous step\.

   ```
   aws greengrassv2 describe-component \
     --arn "arn:aws:greengrass:region:account-id:components:com.example.HelloWorldLambda:versions:1.0.0"
   ```

   If the component validates, the response indicates that the component state is `DEPLOYABLE`\.

   ```
   {
     "arn": "arn:aws:greengrass:region:account-id:components:com.example.HelloWorldLambda:versions:1.0.0",
     "componentName": "com.example.HelloWorldLambda",
     "componentVersion": "1.0.0",
     "creationTimestamp": "2020-12-15T20:56:34.376000-08:00",
     "publisher": "AWS Lambda",
     "status": {
       "componentState": "DEPLOYABLE",
       "message": "NONE",
       "errors": {}
     },
     "platforms": [
       {
         "name": "Linux x86",
         "attributes": {
           "architecture": "x86",
           "os": "linux"
         }
       }
     ]
   }
   ```

   After the component is `DEPLOYABLE`, you can deploy the Lambda function to your core devices\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.