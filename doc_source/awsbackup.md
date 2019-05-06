# Using AWS Backup with Amazon EFS<a name="awsbackup"></a>

AWS Backup is a simple and cost\-effective way to protect your data by backing up your Amazon EFS file systems\. AWS Backup is a unified backup service designed to simplify the creation, migration, restoration, and deletion of backups, while providing improved reporting and auditing\. AWS Backup makes it easier to develop a centralized backup strategy for legal, regulatory, and professional compliance\. AWS Backup also makes protecting your AWS storage volumes, databases, and file systems simpler by providing a central place where you can do the following:
+ Configure and audit the AWS resources that you want to back up
+ Automate backup scheduling
+ Set retention policies
+ Monitor all recent backup and restore activity

Amazon EFS is integrated with AWS Backup\. You can use AWS Backup to set backup plans where you specify your backup frequency, when to back up, how long to retain backups, and a lifecycle policy for backups\. You can then assign Amazon EFS file systems, or other AWS resources, to that backup plan\.

## How AWS Backup Works with EFS File Systems<a name="how-awsbackup-works-efs"></a>

With AWS Backup, first you create a backup plan\. The backup plan defines backup schedule, backup window, retention policy, lifecycle policy, and tags\. You can create a backup plan using the [AWS Backup Management Console](https://console.aws.amazon.com/backup), the AWS CLI, or the AWS Backup API\. As part of a backup plan, you can define the following:
+ Schedule – When the backup occurs
+ Backup window – The window of time in which the backup needs to start
+ Lifecycle – When to move a recovery point to cold storage and when to delete it
+ Backup vault – Used to organize recovery points created by the Backup rule\.

After your backup plan is created, you assign the specific Amazon EFS file systems to the backup plan by using either tags or the Amazon EFS file system ID\. After a plan is assigned, AWS Backup begins automatically backing up the Amazon EFS file system on your behalf according to the backup plan that you defined\. You can use the AWS Backup console to manage backup configurations or monitor backup activity\. For more information, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide/](https://docs.aws.amazon.com/aws-backup/latest/devguide/)\. 

**Note**  
Sockets and named pipes are not supported, and are omitted from backups\.

### Incremental Backups<a name="incremental-backup"></a>

AWS Backup performs incremental backups of EFS file systems\. During the initial backup, a copy of the entire file system is made\. During subsequent backups of that file system, only files and directories that have been changed, added, or removed are copied\. This approach minimizes the time required to complete the backup and saves on storage costs by not duplicating data\.

### Backup Consistency<a name="backup-consistency"></a>

Amazon EFS is designed to be highly available\. You can access and modify your Amazon EFS file systems while your backup is occurring in AWS Backup\. However, inconsistencies, such as duplicated, skewed, or excluded data, can occur if you make modifications to your file system while the backup is occurring\. These modifications include write, rename, move, or delete operations\. To ensure consistent backups, we recommend that you pause applications or processes that are modifying the file system for the duration of the backup process, or schedule your backups to occur during periods when the file system is not being modified\.

### Performance<a name="efs-performance"></a>

In general, you can expect the following backup rates with AWS Backup:
+ 100 MB/s for file systems composed of mostly large files
+ 500 files/s for file systems composed of mostly small files
+ The maximum duration for a backup or a restore operation in AWS Backup is seven days\.

Restore operations generally take longer than the corresponding backup\.

Using AWS Backup doesn't consume accumulated burst credits, and it doesn't count against the General Purpose mode limit of 7,000 file system operations per second\. 

### Completion Window<a name="backup-window"></a>

You can optionally specify a completion window for a backup\. This window defines the period of time in which a backup needs to complete\. If you specify a completion window, make sure that you consider the expected performance and the size and makeup of your file system\. Doing this helps make sure that your backup can complete during the window\.

Backups that don't complete during the specified window are flagged with an incomplete status\. During the next scheduled backup, AWS Backup resumes at the point that it left off\. You can see the status of all of your backups on the [AWS Backup Management Console](https://console.aws.amazon.com/backup)\.

### EFS Storage Classes<a name="backups-storage-classes"></a>

You can use AWS Backup to back up all data in an EFS file system, regardless of which storage class the data is in\. When restoring a recovery point, all files are restored to the standard storage class\. If you restore the recovery point to a file system with lifecycle management enabled, EFS lifecycle management moves files that have not been accessed in the 30 days since restoring your recovery point to the Infrequent Access storage class\. For more information on storage classes, see [EFS Storage Classes](storage-classes.md)\.

### On\-Demand Backups<a name="ondemand-backup"></a>

Using either the [AWS Backup Management Console](https://console.aws.amazon.com/backup) or the CLI, you can save a single resource to a backup vault on\-demand\. Unlike with scheduled backups, you don't need to create a backup plan to initiate an on\-demand backup\. You can still assign a lifecycle to your backup, which automatically moves the recovery point to the cold storage tier and notes when to delete it\.

### Concurrent Backups<a name="concurrent-backups"></a>

AWS Backup limits backups to one concurrent backup per resource\. Therefore, scheduled or on\-demand backups may fail if a backup job is already in progress\. For more information about AWS Backup limits, see [AWS Backup Limits](https://docs.aws.amazon.com/aws-backup/latest/devguide/aws-backup-limits.html) in the *AWS Backup Developer Guide*\.

### Restore a Recovery Point<a name="restoring-backup-efs"></a>

Using either the [AWS Backup Management Console](https://console.aws.amazon.com/backup) or the CLI, you can restore a recovery point to a new EFS file system or to the source file system\. In both cases, your recovery point is restored to the restore directory, `aws-backup-restore_2019-01-07T21-06-22-108Z`, which you can see at the root of the file system when the restore is complete\. If the restore fails to complete, you can see the directory `aws-backup-failed-restore_2019-01-07T21-06-22-108Z`\. You need to manually delete this directory when you are through using it\.

**Note**  
After restoring a recovery point, data fragments that can't be restored to the appropriate directory are placed in the `aws-backup-lost+found` directory\. Fragments might be moved to this directory if modifications are made to the file system while the backup is occurring\.