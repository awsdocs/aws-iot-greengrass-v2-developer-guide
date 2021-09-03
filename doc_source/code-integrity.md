# Code integrity in AWS IoT Greengrass V2<a name="code-integrity"></a>

AWS IoT Greengrass deploys software components from the AWS Cloud to devices that run the AWS IoT Greengrass Core software\. These software components include [AWS\-provided components](public-components.md) and [custom components](create-components.md) that you upload to your AWS account\. Every component is composed of a recipe\. The recipe defines the component's metadata, and any number of artifacts, which are component binaries, such as compiled code and static resources\. Component artifacts are stored in Amazon S3\.

As you develop and deploy Greengrass components, you follow these basic steps that work with component artifacts in your AWS account and on your devices:

1. Create and upload artifacts to S3 buckets\.

1. Create a component from a recipe and artifacts in the AWS IoT Greengrass service, which calculates a [cryptographic hash](https://en.wikipedia.org/wiki/Cryptographic_hash_function) of each artifact\.

1. Deploy a component to Greengrass core devices, which download and verify the integrity of each artifact\.

AWS is responsible for maintaining the integrity of artifacts after you upload artifacts to S3 buckets, including when you deploy components to Greengrass core devices\. You are responsible for securing software artifacts before you upload the artifacts to S3 buckets\. You are also responsible for securing access to resources in your AWS account, including the S3 buckets where you upload component artifacts\.

**Note**  
Amazon S3 provides a feature called S3 Object Lock that you can use to protect against changes to component artifacts in S3 buckets your AWS account\. You can use S3 Object Lock to prevent component artifacts from being deleted or overwritten\. For more information, see [Using S3 Object Lock](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html) in the *Amazon Simple Storage Service User Guide*\.

When AWS publishes a public component, and when you upload a custom component, AWS IoT Greengrass calculates a cryptographic digest for each component artifact\. AWS IoT Greengrass updates the component recipe to include each artifact's digest and the hash algorithm used to calculate that digest\. This digest guarantees the integrity of the artifact, because if the artifact changes in the AWS Cloud or during download, its file digest won't match the digest that AWS IoT Greengrass stores in the component recipe\. For more information, see [Artifacts in the component recipe reference](component-recipe-reference.md#manifest-artifacts-definition)\.

When you deploy a component to a core device, the AWS IoT Greengrass Core software downloads the component recipe and each component artifact that the recipe defines\. The AWS IoT Greengrass Core software calculates the digest of each downloaded artifact file and compares it with that artifact's digest in the recipe\. If the digests don't match, the deployment fails, and the AWS IoT Greengrass Core software deletes the downloaded artifacts from the device's file system\. For more information about how connections between core devices and AWS IoT Greengrass are secured, see [Encryption in transit](encryption-in-transit.md)\.

You are responsible for securing component artifact files on your core devices' file systems\. The AWS IoT Greengrass Core software saves artifacts to the `packages` folder in the Greengrass root folder\. You can use AWS IoT Device Defender to analyze, audit, and monitor core devices\. For more information, see [Configuration and vulnerability analysis in AWS IoT Greengrass](vulnerability-analysis-and-management.md)\.