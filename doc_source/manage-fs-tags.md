# Managing File System Tags<a name="manage-fs-tags"></a>

You can create new tags, update values of existing tags, or delete tags associated with a file system\. 

## Using the Console<a name="manage-tags-console"></a>

The console lists existing tags associated with a file system\. You can add new tags, change values of existing tags, or delete existing tags\.

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose the file system\.

1. Choose **Action** and then choose **Manage Tags**\.

1. On the **Manage Tags** page, add or delete tags\. For each new tag, provide a **Key** and its **Value**\. 

1. Choose **Save**\.

## Using the AWS CLI<a name="manage-tags-cli"></a>

You can use the `create-tags` CLI command to add new tags, `delete-tags` to delete existing tags, or use the `describe-tags` command to retrieve tags associated with a file system\. Each CLI command corresponds to the [CreateTags](API_CreateTags.md), [DeleteTags](API_DeleteTags.md), and [DescribeTags](API_DescribeTags.md) Amazon EFS operations\.

For an example walkthrough of the AWS CLI commands that you can use to add and list tags, see [Step 2\.1: Create Amazon EFS File System](wt1-create-efs-resources.md#wt1-create-file-system)\. 

The following `delete-tags` command removes the tag keys `test1` and `test2` from the tag list of the specified file system\.

```
$ aws efs \
delete-tags \
--file-system-id fs-c5a1446c \
--tag-keys "test1" "test2" \
--region us-west-2 \
--profile adminuser
```