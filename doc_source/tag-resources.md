# Tag your AWS IoT Greengrass Version 2 resources<a name="tag-resources"></a>

With tags, you can organize and manage your resources in AWS IoT Greengrass\. You can use tags to assign metadata to your resources, and you can use tags in IAM policies to define conditional access to your resources\.

**Note**  
Currently, Greengrass resource tags are not supported for AWS IoT billing groups or cost allocation reports\.

## Using tags in AWS IoT Greengrass V2<a name="tag-basics"></a>

You can use tags to categorize your AWS IoT Greengrass resources by purpose, owner, environment, or any other classification for your use case\. When you have many resources of the same type, tags help you more readily identify a specific resource\.

Each tag consists of a key and an optional value, both of which you define\. For example, you could define a set of tags for your core devices that helps you track them by the customers who own the devices\. We recommend that you create a set of tag keys that meets your needs for each kind of resource\. By using a consistent set of tag keys, you can more easily manage your resources\.

### Tag with the AWS Management Console<a name="use-tag-console"></a>

The **Tag Editor** in the AWS Management Console provides a central, unified way for you to create and manage your tags for resources from all AWS services\. For more information, see [Tag Editor](https://docs.aws.amazon.com/ARG/latest/userguide/tag-editor.html) in the *AWS Resource Groups User Guide*\.

### Tag with the AWS IoT Greengrass V2 API<a name="use-tag-api"></a>

You can also use the AWS IoT Greengrass V2 API to work with tags\. Before you create tags, be aware of tagging restrictions\. For more information, see [Tag naming and usage conventions](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html#tag-conventions) in the *AWS General Reference*\.
+ To add tags when you create a resource, define them in the `tags` property of the resource\.
+ To add tags to an existing resource, or to update tag values, use the [TagResource](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_TagResource.html) operation\.
+ To remove tags from a resource, use the [UntagResource](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_UntagResource.html) operation\.
+ To retrieve the tags that are associated with a resource, use the [ListTagsForResource](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ListTagsForResource.html) operation, or describe the resource and inspect its `tags` property\.

The following table lists resources that you can tag using the AWS IoT Greengrass V2 API and their corresponding `Create` and `Describe` or `Get` operations\.


**Taggable AWS IoT Greengrass V2 resources**  

| Resource | Create operation | Describe or get operation | 
| --- | --- | --- | 
| Core device |  None\. Run the AWS IoT Greengrass Core software on a device to create a core device\.  |  [GetCoreDevice](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_GetCoreDevice.html)  | 
| Component |  [CreateComponentVersion](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateComponentVersion.html)  |  [DescribeComponent](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_DescribeComponent.html), [GetComponent](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_GetComponent.html)  | 
| Deployment |  [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html)  |  [GetDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_GetDeployment.html)  | 

Use the following operations to view and manage tags for resources that support tagging:
+ [TagResource](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_TagResource.html) – Adds tags to a resource, or updates an existing tag's value\.
+ [ListTagsForResource](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ListTagsForResource.html) – Lists the tags for a resource\.
+ [UntagResource](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_UntagResource.html) – Removes tags from a resource\.

You can add or remove tags for a resource at any time\. To change the value of a tag key, add a tag to the resource that defines the same key and the new value\. The new value replaces the previous value\. You can set a value to an empty string, but you can't set a value to null\.

When you delete a resource, tags that are associated with that resource are also deleted\.

## Using tags with IAM policies<a name="tags-iam"></a>

In your IAM policies, you can use resource tags to control user access and permissions\. For example, policies can allow users to create only those resources that have a specific tag\. Policies can also restrict users from creating or modifying resources that have certain tags\.

**Note**  
If you use tags to allow or deny users' access to resources, you should deny users the ability to add or remove those tags for the same resources\. Otherwise, a user could circumvent your restrictions and gain access to a resource by modifying its tags\.

You can use the following condition context keys and values in the `Condition` element, also called the `Condition` block, of a policy statement\.

`greengrassv2:ResourceTag/tag-key: tag-value`  
Allow or deny actions on resources with specific tags\.

`aws:RequestTag/tag-key: tag-value`  
Require that a specific tag be used, or not used, when creating or modifying a taggable resource\.

`aws:TagKeys: [tag-key, ...]`  
Require that a specific set of tag keys be used, or not used, when creating or modifying a taggable resource\.

**Note**  
The condition context keys and values in an IAM policy apply only to actions that have a taggable resource as a required parameter\. For example, you can set tag\-based conditional access for [ListCoreDevices](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_ListCoreDevices.html)\. 

For more information, see [Controlling access to AWS resources using resource tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) and [IAM JSON policy reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.