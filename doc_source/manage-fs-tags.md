# Tagging resources<a name="manage-fs-tags"></a>

To help you manage your Amazon EFS resources, you can assign your own metadata to each resource in the form of *tags*\. Tags enable you to categorize your AWS resources in different ways, for example, by purpose, owner, or environment\. This is useful when you have many resources of the same type—you can quickly identify a specific resource based on the tags that you've assigned to it\. This topic describes tags and shows you how to create them\.

## Tag basics<a name="tag-basics"></a>

A tag is a label that you assign to an AWS resource\. Each tag consists of a key and an optional value, both of which you define\.

Tags enable you to categorize your AWS resources in different ways, for example, by purpose, owner, or environment\. For example, you could define a set of tags for your account's Amazon EFS file systems that helps you track each file system's owner\.

We recommend that you devise a set of tag keys that meets your needs for each resource type\. Using a consistent set of tag keys makes it easier for you to manage your resources\. You can search and filter the resources based on the tags you add\. For more information about how to implement an effective resource tagging strategy, see the AWS whitepaper [Tagging Best Practices](https://d1.awsstatic.com/whitepapers/aws-tagging-best-practices.pdf)\.

Tags don't have any semantic meaning to Amazon EFS and are interpreted strictly as a string of characters\. Also, tags are not automatically assigned to your resources\. You can edit tag keys and values, and you can remove tags from a resource at any time\. You can set the value of a tag to an empty string, but you can't set the value of a tag to null\. If you add a tag that has the same key as an existing tag on that resource, the new value overwrites the old value\. If you delete a resource, any tags for the resource are also deleted\.

## Tag your resources<a name="tag-resources"></a>

You can tag Amazon EFS file system and access point resources that already exist in your account\.

You can use the Amazon EFS console to apply tags to existing resources by using the **Tags** tab on the resource details screen\. The Amazon EFS console enables you to specify tags for a resource when you create the resource; for example, a tag with a key of `Name` and a value that you specify\. In most cases, the console applies the tags immediately after the resource is created \(rather than during resource creation\)\. The console may organize resources according to the `Name` tag, but this tag doesn't have any semantic meaning to the Amazon EFS service\.

If you're using the Amazon EFS API, the AWS CLI, or an AWS SDK, you can use the `TagResource` EFS API action to apply tags to existing resources\. Additionally, some resource\-creating actions enable you to specify tags for a resource when the resource is created\.

The AWS CLI commands for managing tags, and the equivalent Amazon EFS API actions, are listed in the following table\.


| CLI command | Description | Equivalent API operation | 
| --- | --- | --- | 
|  [https://docs.aws.amazon.com/cli/latest/reference/efs/tag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/tag-resource.html)  |  Add new tags or update existing tags  |  [TagResource](API_TagResource.md)  | 
|  [https://docs.aws.amazon.com/cli/latest/reference/efs/list-tags-for-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/list-tags-for-resource.html)  |  Retrieve existing tags  |  [ListTagsForResource](API_ListTagsForResource.md)  | 
|  [https://docs.aws.amazon.com/cli/latest/reference/efs/untag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/untag-resource.html)  |  Delete existing tags  |  [UntagResource](API_UntagResource.md)  | 

## Tag restrictions<a name="tag-restrictions"></a>

The following basic restrictions apply to tags:
+ Maximum number of tags per resource – 50
+ For each resource, each tag key must be unique, and each tag key can have only one value\.
+ Maximum key length – 128 Unicode characters in UTF\-8
+ Maximum value length – 256 Unicode characters in UTF\-8
+ Although Amazon EFS allows for any character in its tags, other services are more restrictive\. The allowed characters across services are: letters, numbers, and spaces representable in UTF\-8, and the following characters: \+ \- = \. \_ : / @\.
+ Tag keys and values are case\-sensitive\.
+ The `aws:` prefix is reserved for AWS use\. If a tag has a tag key with this prefix, then you can't edit or delete the tag's key or value\. Tags with the `aws:` prefix do not count against your tags per resource limit\.

You can't update or delete a resource based solely on its tags; you must specify the resource identifier\. For example, to delete file systems that you tagged with a tag key called `DeleteMe`, you must use the `DeleteFileSystem` action with the resource identifiers of the file system, such as `fs-1234567890abcdef0`\. 

When you tag public or shared resources, the tags you assign are available only to your AWS account; no other AWS account will have access to those tags\. For tag\-based access control to shared resources, each AWS account must assign its own set of tags to control access to the resource\.

You can tag Amazon EFS file system and access point resources\.