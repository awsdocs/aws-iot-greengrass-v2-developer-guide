# What's new in AWS IoT Greengrass Version 2<a name="greengrass-v2-whats-new"></a>

AWS IoT Greengrass Version 2 is a new major version release of AWS IoT Greengrass that introduces the following features:
+ **Open source edge runtime**—The edge runtime is now open source and distributed under the Apache 2\.0 license and available on GitHub\. You can now view the AWS IoT Greengrass edge runtime code, which allows you to troubleshoot interactions with your application and helps you build more reliable and performant applications running on AWS IoT Greengrass\. You can also customize and extend the AWS IoT Greengrass edge runtime to meet your specific hardware and software needs\. For more information, see [Open source AWS IoT Greengrass Core software](open-source.md)\.
+ **Improved modularity**—You can add or remove pre\-built software components based on your use cases, and your device CPU and memory resources\. For example, you can choose to include only pre\-built AWS IoT Greengrass components, such as stream manager, when you need to process data streams with your application\. Or, you can include only machine learning components when you want to perform machine learning inference locally on your devices\. For more information, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md) and [AWS\-provided components](public-components.md)\.
+ **New local development tools**—AWS IoT Greengrass includes a new command line interface \(CLI\) that enables you to locally develop and debug applications on your device\. In addition, the new local debug console helps you visually debug applications on your device\. With these new capabilities, you can develop and debug code on a test device before using the cloud to deploy to your production devices\. For more information, see [Greengrass CLI](greengrass-cli-component.md) and [Local debug console](local-debug-console-component.md)\.
+ **Improved fleet deployment features**—AWS IoT Greengrass is now integrated with AWS IoT thing groups\. This enables you to organize your devices in groups and manage application deployments across your devices with features that control rollout rates, timeouts, and rollbacks\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.

AWS IoT Greengrass release notes provide details about AWS IoT Greengrass releases—new features, updates and improvements, and general fixes\. AWS IoT Greengrass has the following types of releases:
+ New feature releases for AWS IoT Greengrass
+ AWS IoT Greengrass Core software updates

This section contains all of the AWS IoT Greengrass V2 release notes, latest first, and includes major feature changes and significant bug fixes\. For information about additional minor fixes, see the [aws\-greengrass](https://github.com/aws-greengrass) organization on GitHub\.<a name="greengrass-release-notes"></a>

**Topics**
+ [Release: AWS IoT Greengrass Core v2\.4\.0 software update on August 3, 2021](greengrass-release-2021-08-03.md)
+ [Release: AWS IoT Greengrass Core v2\.3\.0 software update on June 29, 2021](greengrass-release-2021-06-29.md)
+ [Release: AWS IoT Greengrass Core v2\.2\.0 software update on June 18, 2021](greengrass-release-2021-06-18.md)
+ [Release: AWS IoT Greengrass Core v2\.1\.0 software update on April 26, 2021](greengrass-release-2021-04-26.md)
+ [Release: AWS IoT Greengrass Core v2\.0\.5 software update on March 09, 2021](greengrass-release-2021-03-09.md)
+ [Release: AWS IoT Greengrass Core v2\.0\.4 software update on February 04, 2021](greengrass-release-2021-02-04.md)