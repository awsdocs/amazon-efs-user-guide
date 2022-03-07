# Using AWS Backup to back up and restore Amazon EFS file systems<a name="awsbackup"></a>

AWS Backup is a simple and cost\-effective way to protect your data by backing up your Amazon EFS file systems\. AWS Backup is a unified backup service designed to simplify the creation, migration, restoration, and deletion of backups, while providing improved reporting and auditing\. AWS Backup makes it easier to develop a centralized backup strategy for legal, regulatory, and professional compliance\. AWS Backup also makes protecting your AWS storage volumes, databases, and file systems simpler by providing a central place where you can do the following:
+ Configure and audit the AWS resources that you want to back up
+ Automate backup scheduling
+ Set retention policies
+ Monitor all recent backup and restore activity

Amazon EFS is natively integrated with AWS Backup\. You can use the EFS console, API, and AWS Command Line Interface \(AWS CLI\) to enable automatic backups for your file system\. Automatic backups use a default backup plan with the AWS Backup recommended settings for automatic backups\. For more information, see [Automatic backups](#automatic-backups)\. You can also use AWS Backup to [manually set](#manual-backup) your own backup plans where you specify the backup frequency, when to back up, how long to retain backups, and a lifecycle policy for backups\. You can then assign Amazon EFS file systems, or other AWS resources, to that backup plan\.

## Incremental backups<a name="incremental-backup"></a>

AWS Backup performs incremental backups of EFS file systems\. During the initial backup, a copy of the entire file system is made\. During subsequent backups of that file system, only files and directories that have been changed, added, or removed are copied\. With each incremental backup, AWS Backup retains the necessary reference data to allow a full restore\. This approach minimizes the time required to complete the backup and saves on storage costs by not duplicating data\.

## Backup consistency<a name="backup-consistency"></a>

Amazon EFS is designed to be highly available\. You can access and modify your Amazon EFS file systems while your backup is occurring in AWS Backup\. However, inconsistencies, such as duplicated, skewed, or excluded data, can occur if you make modifications to your file system while the backup is occurring\. These modifications include write, rename, move, or delete operations\. To ensure consistent backups, we recommend that you pause applications or processes that are modifying the file system for the duration of the backup process\. Or, schedule your backups to occur during periods when the file system is not being modified\.

## Performance<a name="efs-performance"></a>

In general, you can expect the following backup rates with AWS Backup:
+ 100 MB/s for file systems composed of mostly large files
+ 500 files/s for file systems composed of mostly small files
+ The maximum duration for a backup operation in AWS Backup is seven days\.

Complete restore operations generally take longer than the corresponding backup\.

Using AWS Backup doesn't consume accumulated burst credits, and it doesn't count against the General Purpose mode file operation limits\. For more information, see [Quotas for Amazon EFS file systems](limits.md#limits-fs-specific)\. 

## Backup completion window<a name="backup-window"></a>

You can optionally specify a completion window for a backup\. This window defines the period of time in which a backup needs to be completed\. If you specify a completion window, make sure that you consider the expected performance and the size and makeup of your file system\. Doing this helps make sure that your backup can be completed during the window\.

Backups that aren't completed during the specified window are flagged with an incomplete status\. During the next scheduled backup, AWS Backup resumes at the point that it left off\. You can see the status of all of your backups on the [AWS Backup Management Console](https://console.aws.amazon.com/backup)\.

## EFS storage classes<a name="backups-storage-classes"></a>

You can use AWS Backup to back up all data in an EFS file system, whatever storage class the data is in\. You don't incur data access charges when backing up an EFS file system that has lifecycle management enabled and has data in the Infrequent Access \(IA\) storage class\. 

When you restore a recovery point, all files are restored to the Standard storage class\. For more information on storage classes, see [EFS storage classes](storage-classes.md) and [Amazon EFS lifecycle management](lifecycle-management-efs.md)\.

## IAM permissions for creating and restoring backups<a name="backup-permissions"></a>

You can use the `elasticfilesystem:backup` and `elasticfilesystem:restore` actions to allow or deny an IAM entity \(such as a user, group, or role\) the ability to create or restore backups of an EFS file system\. You can use these actions in a file system policy or in an identity\-based IAM policy\. For more information, see [Managing access to resources](access-control-overview.md#access-control-manage-access-intro) and [Using IAM to control file system data access](iam-access-control-nfs-efs.md)\.

## On\-demand backups<a name="ondemand-backup"></a>

Using either the [AWS Backup Management Console](https://console.aws.amazon.com/backup) or the CLI, you can save a single resource to a backup vault on\-demand\. Unlike with scheduled backups, you don't need to create a backup plan to initiate an on\-demand backup\. You can still assign a lifecycle to your backup, which automatically moves the recovery point to the cold storage tier and notes when to delete it\.

## Concurrent backups<a name="concurrent-backups"></a>

AWS Backup limits backups to one concurrent backup per resource\. Therefore, scheduled or on\-demand backups might fail if a backup job is already in progress\. For more information about AWS Backup limits, see [AWS Backup Limits](https://docs.aws.amazon.com/aws-backup/latest/devguide/aws-backup-limits.html) in the *AWS Backup Developer Guide*\.

## Automatic backups<a name="automatic-backups"></a>

 When you create a file system using the Amazon EFS console, automatic backups are turned on by default\. You can turn on automatic backups after creating your file system using the CLI or API\. The default EFS backup plan uses the AWS Backup recommended settings for automatic backups—daily backups with a 35\-day retention period\. The backups created using the default EFS backup plan are stored in a default EFS backup vault, which is also created by EFS on your behalf\. The default backup plan and backup vault cannot be deleted\. You can edit the default backup plan settings using the AWS Backup console\. For more information, see [ Option 3: Create Automatic Backups](https://docs.aws.amazon.com/aws-backup/latest/devguide/create-auto-backup.html) in the *AWS Backup Developer Guide*\. You can see all of your automatic backups, and edit the default EFS backup plan settings using the [AWS Backup console](https://console.aws.amazon.com/backup)\. You can turn off automatic backups at any time using the Amazon EFS console or CLI, described in the following section\. 

Amazon EFS applies the `aws:elasticfilesystem:default-backup` system tag key with a value of `enabled` to EFS file systems when automatic backups are enabled\.

**Note**  
Automatic backups are exempt from the AWS Backup service opt\-out configuration\. For more information, see [ Getting Started with AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/getting-started.html) in the *AWS Backup Developer Guide*\.

### Turning automatic backups on or off for existing file systems<a name="enable-autobkp-efs"></a>

After you create a file system you can turn automatic backups on or off using the console, the CLI, or the EFS API\.

#### Turn automatic backups on or off for an existing file system \(console\)<a name="enable-autobkp-console-efs"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. In the **File systems** page, choose the file system that you want to turn automatic backups on or off for and display the **File system details** page\.

1. Choose **Edit** in the **General** settings panel\.

1. 
   + To turn on automatic backups, select **Enable automatic backups**\.
   + To turn off automatic backups, clear **Enable automatic backups**\.

1. Choose **Save changes**\.

#### Turn automatic backups on or off for an existing file system \(CLI\)<a name="enable-autobkp-cli-efs"></a>
+ Use the `put-backup-policy` CLI command \(the corresponding API operation is [PutBackupPolicy](API_PutBackupPolicy.md)\) turn automatic backups on or off for an existing file system\.
  + Use the following command to turn on automatic backups\.

    ```
    $ aws efs put-backup-policy --file-system-id fs-01234567 \
    --backup-policy Status="ENABLED"
    ```

    EFS responds with the new backup policy\.

    ```
    {
       "BackupPolicy": { 
          "Status": "ENABLING"
       }
    }
    ```
  + Use the following command to turn off automatic backups\.

    ```
    $ aws efs put-backup-policy --file-system-id fs-01234567 \
    --backup-policy Status="DISABLED"
    ```

    EFS responds with the new backup policy\.

    ```
    {
       "BackupPolicy": { 
          "Status": "DISABLING"
       }
    }
    ```

## Using AWS Backup to manually configure backups<a name="manual-backup"></a>

When you use AWS Backup to manually set up your file system backups, you first create a backup plan\. The backup plan defines backup schedule, backup window, retention policy, lifecycle policy, and tags\. You can create a backup plan using the [AWS Backup Management Console](https://console.aws.amazon.com/backup), the AWS CLI, or the AWS Backup API\. As part of a backup plan, you can define the following:
+ Schedule – When the backup occurs
+ Backup window – The window of time during which the backup must start
+ Lifecycle – When to move a recovery point to cold storage and when to delete it
+ Backup vault – Which vault is used to organize recovery points created by the Backup rule

After your backup plan is created, you assign the specific Amazon EFS file systems to the backup plan by using either tags or the Amazon EFS file system ID\. After a plan is assigned, AWS Backup begins automatically backing up the Amazon EFS file system on your behalf according to the backup plan that you defined\. You can use the AWS Backup console to manage backup configurations or monitor backup activity\. For more information, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)\. 

**Note**  
Sockets and named pipes are not supported, and are omitted from backups\.

## Restore a recovery point<a name="restoring-backup-efs"></a>

Using either the [AWS Backup console](https://console.aws.amazon.com/backup) or the CLI, you can restore a recovery point to a new EFS file system or to the source file system\. You can perform a Complete restore, which restores the entire file system\. Or, you can restore specific files and directories using a Partial restore\. To restore a specific file or directory, you must specify the relative path related to the mount point\. For example, if the file system is mounted to `/user/home/myname/efs` and the file path is `user/home/myname/efs/file1`, enter `/file1`\. Paths are case sensitive and cannot contain special characters, wildcard characters, or regular expression \(regex\) strings\.

**Note**  
 To restore a recovery point, users must have the `backup:StartRestoreJob` permission\.

When you perform either a Complete or a Partial restore, your recovery point is restored to the restore directory, `aws-backup-restore_timestamp-of-restore`\. When the restore is finished, you can see the restore directory at the root of the file system\. If you attempt multiple restores for the same path, several directories containing the restored items might exist\. If the restore fails to finish, you can see the directory `aws-backup-failed-restore_timestamp-of-restore`\. You must manually delete the `restore` and `failed-restore` directories when you are through using them\.

**Note**  
For Partial restores to an existing EFS file system, AWS Backup restores the files and directories to a new directory under the file system root directory\. The full hierarchy of the specified items is preserved in the recovery directory\. For example, if directory A contains subdirectories B, C, and D, AWS Backup retains the hierarchical structure when A, B, C, and D are recovered\.

After restoring a recovery point, data fragments that can't be restored to the appropriate directory are placed in the `aws-backup-lost+found` directory\. Fragments might be moved to this directory if modifications are made to the file system while the backup is occurring\.

## Deleting backups<a name="delete-backups"></a>

The default EFS backup vault Access policy is set to deny deleting recovery points\. To delete existing backups of your EFS file systems, you must change the vault access policy\. If you attempt to delete an EFS recovery point without modifying the vault access policy, you receive the following error message:

```
"Access Denied: Insufficient privileges to perform this action. Please consult with the account administrator for necessary permissions."
```

To edit the default backup vault access policy, you must have AWS Identity and Access Management \(IAM\) permissions to edit your EFS policies\. To revise IAM policy settings, you must have Admin rights\. For example, you can perform these actions using your AWS account root user, or by using an admin role\. For more information, see [Allow all IAM actions \(admin access\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_delegate-permissions_examples.html#creds-policies-all-iam)\.

**To delete an EFS recovery point in AWS Backup**

1. Open the AWS Backup console at [https://console\.aws\.amazon\.com/backup](https://console.aws.amazon.com/backup)\.

1. In the left navigation pane, choose **Backup vaults**\.

1. In the list **Backup vaults**, choose **aws/efs/automatic\-backup\-vault**\.

1. On the vault details page, choose **Manage access** in the upper\-right corner of the page\. The **Edit access policy** page appears\.

1. To allow all actions on the EFS backup vault, find the line `"Effect": "Deny",` in the JSON editor, and edit the line to read `"Effect": "Allow",`\.

1. Choose **Save policy** to save your changes\.

1. On the vault details page, scroll down to the **Backups** section, and select the recovery points that you want to delete from the list of **Backups**\. Then choose **Actions**, and then choose **Delete**\.

1. Follow the instructions to confirm the deletion\. Then choose **Delete recovery points**\.