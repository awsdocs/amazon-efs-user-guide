# Managing file system tags<a name="manage-fs-tags"></a>

You can include file system tags when you create your Amazon EFS file system\. You can also create new tags, update values of existing tags, or delete tags associated with a file system anytime after it is created\. 

## Using the console<a name="manage-tags-console"></a>

The console lists existing tags associated with a file system\. You can add new tags, change values of existing tags, or delete existing tags\.

**To add tags, change tag values, or delete tags \(console\)**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File systems**\.

1. Choose the file system that you want to manage tags for\.

1. On the **File system details** page, choose **Tags**\.

1. To edit existing tags or create new tags, choose **Manage tags**\.

1. In the **Manage tags** dialog box, you can do the following:
   + Edit a tag value \- Choose the tag value, edit the value, and then choose **Save**\.
   + Add a new tag \- Choose **Add tag**, enter the key\-value pair, and choose **Save**\.
   + Remove an existing tag \- Choose **Remove tag** next to the tag you want to delete\. Then choose **Save**, or choose **Undo** to revert the deletion\.  
![\[Manage file system tages in the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-manage-tags.png)

## Using the CLI<a name="manage-tags-cli"></a>

The AWS CLI commands for managing tags, and the equivalent Amazon EFS API actions, are listed in the following table\.


| CLI command | Description | Equivalent API operation | 
| --- | --- | --- | 
|  [https://docs.aws.amazon.com/cli/latest/reference/efs/tag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/tag-resource.html)  |  Add new tags or update existing tags  |  [TagResource](API_TagResource.md)  | 
|  [https://docs.aws.amazon.com/cli/latest/reference/efs/list-tags-for-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/list-tags-for-resource.html)  |  Retrieve existing tags  |  [ListTagsForResource](API_ListTagsForResource.md)  | 
|  [https://docs.aws.amazon.com/cli/latest/reference/efs/untag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/untag-resource.html)  |  Delete existing tags  |  [UntagResource](API_UntagResource.md)  | 

**To create file system tags \(AWS CLI\)**
+ Create tags by using the Amazon EFS [https://docs.aws.amazon.com/cli/latest/reference/efs/tag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/tag-resource.html) CLI command \(the corresponding API operation is [TagResource](API_TagResource.md)\)\. The following example command creates a new `Department` tag that has a value of `Business Intelligence` to the file system\.

  ```
  $  aws efs tag-resource \
  --resource-id File-System-Id \
  --tags Key=Department,Value="Business Intelligence" \
  --region aws-region \
  ```

  If the command is successful, the AWS CLI doesn't provide a response\.

**To create an access point tag**
+ You can create a tag for an EFS access point using the [https://docs.aws.amazon.com/cli/latest/reference/efs/tag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/tag-resource.html) CLI command\.

  ```
  $  aws efs tag-resource \
  --resource-id Access-Point-Id \
  --tags Key=AccessTeam,Value="Data_Lake" \
  --region aws-region \
  ```

**To retrieve all tags associated with a file system**
+ Retrieve a list of tags associated with a file system by using the [https://docs.aws.amazon.com/cli/latest/reference/efs/list-tags-for-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/list-tags-for-resource.html) CLI command \(the corresponding API operation is [ListTagsForResource](API_ListTagsForResource.md)\), as shown following\.

  ```
  $  aws efs list-tags-for-resource \
  --resource-id File-System-Id \
  --region aws-region \
  ```

  Amazon EFS returns these descriptions as JSON\. The following is an example of tags returned by the `DescribeTags` operation\. It shows a file system as having three tags\.

  ```
  {
      "Tags": [
         {
            "Key": "Name",
            "Value": "Test File System"            
         },
         {
            "Key": "developer",
            "Value": "rhoward"
         },
         {
            "Key": "Department",
            "Value": "Business Intelligence"
         }
      ]
  }
  ```

**To delete one or more tags associated with an EFS resource**
+ Delete a file system's tags using the [https://docs.aws.amazon.com/cli/latest/reference/efs/untag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/untag-resource.html) CLI command \(the corresponding API operation is [UntagResource](API_UntagResource.md)\)\. The following command removes the tag keys `test1` and `test2` from the tag list of the specified file system\. You specify the key of each tag that you want to delete in the request\.

  ```
  $ aws efs untag-resource\
  --resource-id fs-c5a1446c \
  --tag-keys "test1" "test2" \
  --region us-west-2 \
  ```

  If the command is successful, the AWS CLI doesn't provide a response\.

**To delete, or untag, an EFS access point**
+ Delete an EFS access point's tag using the [https://docs.aws.amazon.com/cli/latest/reference/efs/untag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/efs/untag-resource.html) CLI command \(the corresponding API operation is [UntagResource](API_UntagResource.md)\) as follows\.

  ```
  $  aws efs untag-resource \
  --resource-id Access-Point-Id \
  --tags Key=AccessTeam,Value="Data_Lake" \
  --region us-west-2 \
  ```