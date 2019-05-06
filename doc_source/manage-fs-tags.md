# Managing File System Tags<a name="manage-fs-tags"></a>

You can include file system tags when you create your Amazon EFS file system\. You can also create new tags, update values of existing tags, or delete tags associated with a file system anytime after it is created\. 

## Using the Console<a name="manage-tags-console"></a>

The console lists existing tags associated with a file system\. You can add new tags, change values of existing tags, or delete existing tags\.

**To add tags, change tag values, or delete tags \(console\)**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose the file system\.

1. Choose **Action** and then choose **Manage Tags**\.

1. On the **Manage Tags** page, add new tags, and update or delete existing tags\. For each new tag, provide a **Key** and its **Value**\. 

1. Choose **Save**\.

## Using the AWS CLI<a name="manage-tags-cli"></a>

The CLI commands for managing tags, and the equivalent Amazon EFS API actions, are listed in the following table\.


| CLI Command | Description | Equivalent API Operation | 
| --- | --- | --- | 
|  `create-tags`  |  Add new tags or update existing tags  |  [CreateTags](API_CreateTags.md)  | 
|  `describe-tags`  |  Retrieve existing tags  |  [DescribeTags](API_DescribeTags.md)  | 
|  `delete-tags`  |  Delete existing tags  |  [DeleteTags](API_DeleteTags.md)  | 

**To create file system tags \(AWS CLI\)**
+ Create tags by using the Amazon EFS `create-tags` CLI command \(the corresponding API operation is [CreateTags](API_CreateTags.md)\)\. The following example command creates a new `Department` tag that has a value of `Business Intelligence` to the file system\.

  ```
  $  aws efs create-tags \
  --file-system-id File-System-ID \
  --tags Key=Department,Value="Business Intelligence" \
  --region aws-region \
  --profile adminuser
  ```

  If the command is successful, the AWS CLI doesn't provide a response\.

**To retrieve all tags associated with a file system**
+ Retrieve a list of tags associated with a file system using the `describe-tags` CLI command \(the corresponding API operation is [DescribeTags](API_DescribeTags.md)\), as shown following\.

  ```
  $  aws efs describe-tags \
  --file-system-id File-System-ID \
  --region aws-region \
  --profile adminuser
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

**To delete one or more tags associated with a file system**
+ Delete a file system's tags using the `delete-tags` CLI command \(the corresponding API operation is [DeleteTags](API_DeleteTags.md)\)\. The following command removes the tag keys `test1` and `test2` from the tag list of the specified file system\. You specify the key of each tag that you want to delete in the request\.

  ```
  $ aws efs \
  delete-tags \
  --file-system-id fs-c5a1446c \
  --tag-keys "test1" "test2" \
  --region us-west-2 \
  --profile adminuser
  ```

  If the command is successful, the AWS CLI doesn't provide a response\.