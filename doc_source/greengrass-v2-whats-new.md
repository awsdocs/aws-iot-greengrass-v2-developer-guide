# What's new in AWS IoT Greengrass Version 2<a name="greengrass-v2-whats-new"></a>

AWS IoT Greengrass Version 2 is a new major version release of AWS IoT Greengrass that introduces the following features:
+ **Open source edge runtime**—The edge runtime is now open source and distributed under the Apache 2\.0 license and available on GitHub\. You can now view the AWS IoT Greengrass edge runtime code, which allows you to troubleshoot interactions with your application and helps you build more reliable and performant applications running on AWS IoT Greengrass\. You can also customize and extend the AWS IoT Greengrass edge runtime to meet your specific hardware and software needs\. For more information, see [Open source AWS IoT Greengrass Core software](open-source.md)\.
+ **Improved modularity**—You can add or remove pre\-built software components based on your use cases, and your device CPU and memory resources\. For example, you can choose to include only pre\-built AWS IoT Greengrass components, such as stream manager, when you need to process data streams with your application\. Or, you can include only machine learning components when you want to perform machine learning inference locally on your devices\. For more information, see [Develop AWS IoT Greengrass components](develop-greengrass-components.md) and [AWS\-provided components](public-components.md)\.
+ **New development tools**—AWS IoT Greengrass includes [a new command line interface \(CLI\)](greengrass-cli-component.md) that enables you to locally develop and debug applications on your device\. In addition, the [new local debug console](local-debug-console-component.md) helps you visually debug applications on your device\. With these new capabilities, you can develop and debug code on a test device before using the cloud to deploy to your production devices\. AWS IoT Greengrass also provides the [Greengrass Development Kit CLI](greengrass-development-kit-cli.md) \(GDK CLI\), which you can use on your development computer to help you develop and publish custom Greengrass components\. For more information, see [AWS IoT Greengrass development tools](greengrass-development-tools.md)\.
+ **Improved fleet deployment features**—AWS IoT Greengrass is now integrated with AWS IoT thing groups\. This enables you to organize your devices in groups and manage application deployments across your devices with features that control rollout rates, timeouts, and rollbacks\. For more information, see [Deploy AWS IoT Greengrass components to devices](manage-deployments.md)\.
+ **PSA\-certified** – AWS IoT Greengrass nucleus versions 2\.7\.0 and later are now Platform Security Architecture \(PSA\) certified\. For more information, see [AWS IoT Greengrass is PSA\-certified](https://www.psacertified.org/products/aws-iot-greengrass/)\.
+ **Open Test Framework \(OTF\)** – OTF for AWS IoT Greengrass Version 2 is now available\. OTF is a collection of building blocks to support end\-to\-end automation\. It enables AWS IoT Greengrass Version 2 internal customers to use the same testing framework that the service team uses for qualifying software changes, automated acceptance, and quality assurance purposes\. For more information, see [Open Test Framework on Github](https://github.com/aws-greengrass/aws-greengrass-testing)\.

AWS IoT Greengrass release notes provide details about AWS IoT Greengrass releases—new features, updates and improvements, and general fixes\. AWS IoT Greengrass has the following types of releases:
+ New feature releases for AWS IoT Greengrass
+ AWS IoT Greengrass Core software updates

This section contains all of the AWS IoT Greengrass V2 release notes, latest first, and includes major feature changes and significant bug fixes\. For information about additional minor fixes, see the [aws\-greengrass](https://github.com/aws-greengrass) organization on GitHub\.<a name="greengrass-release-notes"></a>

**Topics**
+ [Release: AWS IoT Greengrass Core v2\.9\.5 software update on March 30, 2023](greengrass-release-2023-03-30.md)
+ [Release: AWS IoT Greengrass Core v2\.9\.4 software update on February 24, 2023](greengrass-release-2023-02-24.md)
+ [Release: AWS IoT Greengrass Core v2\.9\.3 software update on February 01, 2023](greengrass-release-2023-02-01.md)
+ [Release: AWS IoT Greengrass Core v2\.9\.2 software update on December 22, 2022](greengrass-release-2022-12-22.md)
+ [Release: AWS IoT Greengrass Core v2\.9\.1 software update on November 18, 2022](greengrass-release-2022-11-18.md)
+ [Release: AWS IoT Greengrass Core v2\.9\.0 software update on November 15, 2022](greengrass-release-2022-11-15.md)
+ [Release: AWS IoT Greengrass Core v2\.8\.1 software update on October 13, 2022](greengrass-release-2022-10-13.md)
+ [Release: AWS IoT Greengrass Core v2\.8\.0 software update on October 7, 2022](greengrass-release-2022-10-07.md)
+ [Release: AWS IoT Greengrass Core v2\.7\.0 software update on July 28, 2022](greengrass-release-2022-07-28.md)
+ [Release: AWS IoT Greengrass Core v2\.6\.0 software update on June 27, 2022](greengrass-release-2022-06-27.md)
+ [Release: AWS IoT Greengrass Core v2\.5\.6 software update on May 31, 2022](greengrass-release-2022-05-31.md)
+ [Release: AWS IoT Greengrass Core v2\.5\.5 software update on April 6, 2022](greengrass-release-2022-04-06.md)
+ [Release: AWS IoT Greengrass Core v2\.5\.4 software update on March 23, 2022](greengrass-release-2022-03-23.md)
+ [Release: AWS IoT Greengrass Core v2\.5\.3 software update on January 6, 2022](greengrass-release-2022-01-06.md)
+ [Release: AWS IoT Greengrass Core v2\.5\.2 software update on December 3, 2021](greengrass-release-2021-12-03.md)
+ [Release: AWS IoT Greengrass Core v2\.5\.1 software update on November 23, 2021](greengrass-release-2021-11-23.md)
+ [Release: AWS IoT Greengrass Core v2\.5\.0 software update on November 12, 2021](greengrass-release-2021-11-12.md)
+ [Release: AWS IoT Greengrass Core v2\.4\.0 software update on August 3, 2021](greengrass-release-2021-08-03.md)
+ [Release: AWS IoT Greengrass Core v2\.3\.0 software update on June 29, 2021](greengrass-release-2021-06-29.md)
+ [Release: AWS IoT Greengrass Core v2\.2\.0 software update on June 18, 2021](greengrass-release-2021-06-18.md)
+ [Release: AWS IoT Greengrass Core v2\.1\.0 software update on April 26, 2021](greengrass-release-2021-04-26.md)
+ [Release: AWS IoT Greengrass Core v2\.0\.5 software update on March 09, 2021](greengrass-release-2021-03-09.md)
+ [Release: AWS IoT Greengrass Core v2\.0\.4 software update on February 04, 2021](greengrass-release-2021-02-04.md)