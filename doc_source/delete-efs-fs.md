# Deleting an Amazon EFS file system<a name="delete-efs-fs"></a>

File system deletion is a destructive action that you can't undo\. You lose the file system and any data you have in it\. Any data that you delete from a file system is gone, and you can't restore the data\. When users delete data from a file system, that data is immediately rendered unusable\. EFS force\-overwrites the data in an eventual manner\.

**Important**  
You should always unmount a file system before you delete it\.

## Using the Console<a name="manage-delete-fs-console"></a>

**To delete a file system**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Select the file system that you want to delete in the **File systems** page\.

1. Choose **Delete**\.

1. In the **Delete file system** dialog box, enter the file system id shown, and choose **Confirm** to confirm the delete\.  
![\[Edit General settings page in the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-delete-fs-efs.png)

   The console simplifies the file system deletion for you\. First it deletes the associated mount targets, and then it deletes the file system\.

## Using the CLI<a name="manage-delete-fs-cli"></a>

Before you can use the AWS CLI command to delete a file system, you must delete all of the mount targets and access points created for the file system\. 

For example AWS CLI commands, see [Step 4: Clean Up](wt1-clean-up.md)\. 

## Related Topics<a name="manage-delete-fs-related"></a>

 [Managing Amazon EFS file systems](managing.md) 