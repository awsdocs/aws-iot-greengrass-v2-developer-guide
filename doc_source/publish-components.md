# Publish components to deploy to your core devices<a name="publish-components"></a>

After you build or complete a version of a component, you can publish it to the AWS IoT Greengrass service\. Then, you can deploy it to Greengrass core devices\.

If you use the [Greengrass Development Kit CLI \(GDK CLI\)](greengrass-development-kit-cli.md) to [develop and build a component](create-components.md), you can [use the GDK CLI](#publish-component-gdk-cli) to publish the component to the AWS Cloud\. Otherwise, [use built\-in shell commands and the AWS CLI](#publish-component-shell-commands) to publish the component\.

You can also use AWS CloudFormation to create components and other AWS resources from templates\. For more information, see [What is AWS CloudFormation?](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) and [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-greengrassv2-componentversion.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-greengrassv2-componentversion.html) in the *AWS CloudFormation User Guide*\.

**Topics**
+ [Publish a component \(GDK CLI\)](#publish-component-gdk-cli)
+ [Publish a component \(shell commands\)](#publish-component-shell-commands)

## Publish a component \(GDK CLI\)<a name="publish-component-gdk-cli"></a>

Follow instructions in this section to publish a component using the GDK CLI\. The GDK CLI uploads build artifacts to an S3 bucket, updates the artifact URIs in the recipe, and creates the component from the recipe\. You specify the S3 bucket and Region to use in the [GDK CLI configuration file](gdk-cli-configuration-file.md)\.

<a name="gdk-cli-s3-bucket-name-formation"></a>If you use GDK CLI v1\.1\.0 or later, you can specify the `--bucket` argument to specify the S3 bucket where the GDK CLI uploads the component's artifacts\. <a name="gdk-cli-s3-bucket-name-formation-format"></a>If you don't specify this argument, the GDK CLI uploads to the S3 bucket whose name is `bucket-region-accountId`, where *bucket* and *region* are the values that you specify in `gdk-config.json`, and *accountId* is your AWS account ID\. The GDK CLI creates the bucket if it doesn't exist\.

**Important**  <a name="publish-component-s3-bucket-token-exchange-role-permissions"></a>
Core device roles don't allow access to S3 buckets by default\. If this is your first time using this S3 bucket, you must add permissions to the role to allow core devices to retrieve component artifacts from this S3 bucket\. For more information, see [Allow access to S3 buckets for component artifacts](device-service-role.md#device-service-role-access-s3-bucket)\.

**To publish a Greengrass component \(GDK CLI\)**

1. Open the component folder in a command prompt or terminal\.

1. If you haven't already, build the Greengrass component\. The [component build](greengrass-development-kit-cli-component.md#greengrass-development-kit-cli-component-build) command produces a recipe and artifacts in the `greengrass-build` folder in the component folder\. Run the following command\.

   ```
   gdk component build
   ```

1. Publish the component to the AWS Cloud\. The [component publish](greengrass-development-kit-cli-component.md#greengrass-development-kit-cli-component-publish) command uploads the component's artifacts to Amazon S3 and updates the component's recipe with each artifact's URI\. Then, it creates the component in the AWS IoT Greengrass service\.
**Note**  <a name="publish-component-s3-bucket-artifact-digest-warning"></a>
AWS IoT Greengrass computes the digest of each artifact when you create the component\. This means that you can't modify the artifact files in your S3 bucket after you create a component\. If you do, deployments that include this component will fail, because the file digest doesn't match\. If you modify an artifact file, you must create a new version of the component\.

   If you specify `NEXT_PATCH` for the component version in the GDK CLI configuration file, the GDK CLI uses the next patch version that doesn't already exist in the AWS IoT Greengrass service\.

   Run the following command\.

   ```
   gdk component publish
   ```

   The output tells you the version of the component that the GDK CLI created\.

   After you publish the component, you can deploy the component to core devices\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

## Publish a component \(shell commands\)<a name="publish-component-shell-commands"></a>

Use the following procedure to publish a component using shell commands and the AWS Command Line Interface \(AWS CLI\)\. When you publish a component, you do the following:

1. Publish component artifacts to an S3 bucket\.

1. Add each artifact's Amazon S3 URI to the component recipe\.

1. Create a component version in AWS IoT Greengrass from the component recipe\.

**Note**  <a name="component-version-uniqueness-note"></a>
<a name="component-version-uniqueness-para"></a>Each component version that you upload must be unique\. Make sure that you upload the correct component version, because you can't edit it after you upload it\.

You can follow these steps to publish a component from your development computer or your Greengrass core device\.

**To publish a component \(shell commands\)**

1. If the component uses a version that exists in the AWS IoT Greengrass service, then you must change the version of the component\. Open the recipe in a text editor, increment the version, and save the file\. Choose a new version that reflects the changes that you made to the component\.
**Note**  <a name="semver-note"></a>
<a name="semver-para"></a>AWS IoT Greengrass uses semantic versions for components\. Semantic versions follow a *major*\.*minor*\.*patch* number system\. For example, version `1.0.0` represents the first major release for a component\. For more information, see the [semantic version specification](https://semver.org/)\.

1. If your component has artifacts, do the following:

   1. Publish the component's artifacts to an S3 bucket in your AWS account\.
**Tip**  <a name="artifact-path-tip"></a>
We recommend that you include the component name and version in the path to the artifact in the S3 bucket\. This naming scheme can help you maintain the artifacts that previous versions of the component use, so you can continue to support previous component versions\.

      Run the following command to publish an artifact file to an S3 bucket\. Replace *DOC\-EXAMPLE\-BUCKET* with the name of the bucket, and replace *artifacts/com\.example\.HelloWorld/1\.0\.0/artifact\.py* with the path to the artifact file\.

      ```
      aws s3 cp artifacts/com.example.HelloWorld/1.0.0/artifact.py s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/artifact.py
      ```
**Important**  <a name="publish-component-s3-bucket-token-exchange-role-permissions"></a>
Core device roles don't allow access to S3 buckets by default\. If this is your first time using this S3 bucket, you must add permissions to the role to allow core devices to retrieve component artifacts from this S3 bucket\. For more information, see [Allow access to S3 buckets for component artifacts](device-service-role.md#device-service-role-access-s3-bucket)\.

   1. Add a list named `Artifacts` to the component recipe if it isn't present\. The `Artifacts` list appears in each manifest, which defines the component's requirements on each platform that it supports \(or the component's default requirements for all platforms\)\.

   1. Add each artifact to the list of artifacts, or update the URI of existing artifacts\. The Amazon S3 URI is composed of the bucket name and the path to the artifact object in the bucket\. Your artifacts' Amazon S3 URIs should look similar to the following example\.

      ```
      s3://DOC-EXAMPLE-BUCKET/artifacts/com.example.HelloWorld/1.0.0/artifact.py
      ```

   After you complete these steps, your recipe should have an `Artifacts` list that looks like the following\.

------
#### [ JSON ]

   ```
   {
     ...
     "Manifests": [
       {
         "Lifecycle": {
           ...
         },
         "Artifacts": [
           {
             "URI": "s3://DOC-EXAMPLE-BUCKET/artifacts/MyGreengrassComponent/1.0.0/artifact.py",
             "Unarchive": "NONE"
           }
         ]
       }
     ]
   }
   ```

**Note**  
You can add the `"Unarchive": "ZIP"` option for a ZIP artifact to configure the AWS IoT Greengrass Core software to unzip the artifact when the component deploys\.

------
#### [ YAML ]

   ```
   ...
   Manifests:
     - Lifecycle:
         ...
       Artifacts:
         - URI: s3://DOC-EXAMPLE-BUCKET/artifacts/MyGreengrassComponent/1.0.0/artifact.py
           Unarchive: NONE
   ```

**Note**  
You can use the `Unarchive: ZIP` option to configure the AWS IoT Greengrass Core software to unzip a ZIP artifact when the component deploys\. For more information about how to use ZIP artifacts in a component, see the [artifacts:decompressedPath recipe variable](component-recipe-reference.md#component-recipe-artifacts-decompressed-path)\.

------

   For more information about recipes, see [AWS IoT Greengrass component recipe reference](component-recipe-reference.md)\.

1. Use the AWS IoT Greengrass console to create a component from the recipe file\.

   Run the following command to create the component from a recipe file\. This command creates the component and publishes it as a private AWS IoT Greengrass component in your AWS account\. Replace *path/to/recipeFile* with the path to the recipe file\.

   ```
   aws greengrassv2 create-component-version --inline-recipe fileb://path/to/recipeFile
   ```

   Copy the `arn` from the response to check the state of the component in the next step\.
**Note**  <a name="publish-component-s3-bucket-artifact-digest-warning"></a>
AWS IoT Greengrass computes the digest of each artifact when you create the component\. This means that you can't modify the artifact files in your S3 bucket after you create a component\. If you do, deployments that include this component will fail, because the file digest doesn't match\. If you modify an artifact file, you must create a new version of the component\.

1. Each component in the AWS IoT Greengrass service has a state\. Run the following command to confirm the state of the component version that you publish in this procedure\. Replace *com\.example\.HelloWorld* and *1\.0\.0* with the component version to query\. Replace the `arn` with the ARN from the previous step\.

   ```
   aws greengrassv2 describe-component --arn "arn:aws:greengrass:region:account-id:components:com.example.HelloWorld:versions:1.0.0"
   ```

   The operation returns a response that contains the component's metadata\. The metadata contains a `status` object that contains the component state and any errors, if applicable\.

   When the component state is `DEPLOYABLE`, you can deploy the component to devices\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.