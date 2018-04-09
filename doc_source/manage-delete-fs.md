# Deleting an Amazon EFS File System<a name="manage-delete-fs"></a>

File system deletion is a destructive action that cannot be undone\. You lose the file system and any data you have in it\. 

**Important**  
You should always unmount a file system before you delete it\.

## Using the Console<a name="manage-delete-fs-console"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Select the file system that you want to delete\.

1. Choose **Action** and then choose **Delete File System**\.

1. In **Permanently Delete File System** confirmation box, type the file system ID and then choose **Delete File System**\. 

   The console simplifies the file deletion for you\. First it deletes the associated mount targets, and then it deletes the file system\.

## Using the CLI<a name="manage-delete-fs-cli"></a>

Before you can use the AWS CLI command to delete a file system, you must delete all of the mount targets created for the file system\. 

For example AWS CLI commands, see [Step 4: Clean Up](wt1-clean-up.md)\. 

## Related Topics<a name="manage-delete-fs-related"></a>

 [Managing Amazon EFS File Systems](managing.md) 