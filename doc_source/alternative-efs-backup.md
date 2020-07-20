# Backing Up Amazon EFS File Systems Using AWS Data Pipeline<a name="alternative-efs-backup"></a>

**Note**  
Using the AWS Data Pipeline to back up your EFS file systems is a legacy solution\.

## Recommended EFS Backup Solutions<a name="recommended-backup-solutions"></a>

There are two recommended solutions available for backing up your EFS file systems\.
+ AWS Backup service
+ The EFS\-to\-EFS backup solution

AWS Backup is a simple and cost\-effective way to back up your Amazon EFS file systems\. AWS Backup is a unified backup service designed to simplify the creation, migration, restoration, and deletion of backups, while providing improved reporting and auditing\. For more information, see [Using AWS Backup with Amazon EFS](awsbackup.md)\.

The EFS\-to\-EFS backup solution is suitable for all Amazon EFS file systems in all AWS Regions\. It includes an AWS CloudFormation template that launches, configures, and runs the AWS services required to deploy this solution\. This solution follows AWS best practices for security and availability\. For more information, see [EFS\-to\-EFS Backup Solution](https://aws.amazon.com/answers/infrastructure-management/efs-backup/) in AWS Answers\.

## Legacy EFS Backup Solution Using AWS Data Pipeline<a name="legacy-solution"></a>

Using AWS Data Pipeline to backup EFS file system is a legacy backup solution\. In this backup solution, you create a data pipeline by using the AWS Data Pipeline service\. This pipeline copies data from your Amazon EFS file system \(called the *production file system*\) to another Amazon EFS file system \(called the *backup file system*\)\. 

This solution consists of AWS Data Pipeline templates that implement the following:
+ Automated EFS backups based on a schedule that you define \(for example, hourly, daily, weekly, or monthly\)\.
+ Automated rotation of the backups, where the oldest backup is replaced with the newest backup based on the number of backups that you want to retain\.
+ Quicker backups using rsync, which only back up the changes made between one backup to the next\.
+ Efficient storage of backups using hard links\. A *hard link* is a directory entry that associates a name with a file in a file system\. By setting up a hard link, you can perform a full restoration of data from any backup while only storing what changed from backup to backup\.

After you set up the backup solution, this walkthrough shows you how to access your backups to restore your data\. This backup solution depends on running scripts that are hosted on GitHub, and is therefore subject to GitHub availability\. If you'd prefer to eliminate this reliance and host the scripts in an Amazon S3 bucket instead, see [Hosting the rsync Scripts in an Amazon S3 Bucket](#hostingscripts)\.

**Important**  
This solution requires using AWS Data Pipeline in the same AWS Region as your file system\. Because AWS Data Pipeline is not supported in US East \(Ohio\), this solution doesn't work in that AWS Region\. We recommend that if you want to back up your file system using this solution, you use your file system in one of the other supported AWS Regions\.

**Topics**
+ [Recommended EFS Backup Solutions](#recommended-backup-solutions)
+ [Legacy EFS Backup Solution Using AWS Data Pipeline](#legacy-solution)
+ [Performance for Amazon EFS Backups Using AWS Data Pipeline](#backup-performance)
+ [Considerations for Amazon EFS Backup by Using AWS Data Pipeline](#backup-considerations)
+ [Assumptions for Amazon EFS Backup with AWS Data Pipeline](#backup-assumptions)
+ [How to Back Up an Amazon EFS File System with AWS Data Pipeline](#backup-steps)
+ [Additional Backup Resources](#walkthrough4-appendix)

## Performance for Amazon EFS Backups Using AWS Data Pipeline<a name="backup-performance"></a>

When performing data backups and restorations, your file system performance is subject to [Amazon EFS Performance](performance.md), including baseline and burst throughput capacity\. The throughput used by your backup solution counts toward your total file system throughput\. The following table provides some recommendations for the Amazon EFS file system and Amazon EC2 instance sizes that work for this solution, assuming that your backup window is 15 minutes long\.


| EFS Size \(30 MB Average File Size\) | Daily Change Volume | Remaining Burst Hours | Minimum Number of Backup Agents | 
| --- | --- | --- | --- | 
| 256 GB | Less than 25 GB | 6\.75 | 1 \- m3\.medium | 
| 512 GB | Less than 50 GB | 7\.75 | 1 \- m3\.large | 
| 1\.0 TB | Less than 75 GB | 11\.75 | 2 \- m3\.large\* | 
| 1\.5 TB | Less than 125 GB | 11\.75 | 2 \- m3\.xlarge\* | 
| 2\.0 TB | Less than 175 GB | 11\.75 | 3 \- m3\.large\* | 
| 3\.0 TB | Less than 250 GB | 11\.75 | 4 \- m3\.xlarge\* | 

\* These estimates are based on the assumption that data stored in an EFS file system that is 1 TB or larger is organized so that the backup can be spread across multiple backup nodes\. The multiple\-node example scripts divide the backup load across nodes based on the contents of the first\-level directory of your EFS file system\. 

For example, if there are two backup nodes, one node backs up all of the even files and directories located in the first\-level directory\. The odd node does the same for the odd files and directories\. In another example, with six directories in the Amazon EFS file system and four backup nodes, the first node backs up the first and the fifth directories\. The second node backs up the second and the sixth directories, and the third and fourth nodes back up the third and the fourth directories respectively\.

## Considerations for Amazon EFS Backup by Using AWS Data Pipeline<a name="backup-considerations"></a>

Consider the following when you're deciding whether to implement an Amazon EFS backup solution using AWS Data Pipeline:
+ This approach to EFS backup involves a number of AWS resources\. For this solution, you need to create the following:
  + One production file system and one backup file system that contains a full copy of the production file system\. The system also contains any incremental changes to your data over the backup rotation period\.
  + Amazon EC2 instances, whose lifecycles are managed by AWS Data Pipeline, that perform restorations and scheduled backups\.
  + One regularly scheduled AWS Data Pipeline for backing up data\.
  + An AWS Data Pipeline for restoring backups\.

  When this solution is implemented, it results in billing to your account for these services\. For more information, see the pricing pages for [Amazon EFS](https://aws.amazon.com/efs/pricing/), [Amazon EC2](https://aws.amazon.com/ec2/pricing/), and [AWS Data Pipeline](https://aws.amazon.com/datapipeline/pricing/)\.
+ This solution isn't an offline backup solution\. To ensure a fully consistent and complete backup, pause any file writes to the file system or unmount the file system while the backup occurs\. We recommend that you perform all backups during scheduled downtime or off hours\.

## Assumptions for Amazon EFS Backup with AWS Data Pipeline<a name="backup-assumptions"></a>

This walkthrough makes several assumptions and declares example values as follows:
+ Before you get started, this walkthrough assumes that you already completed [Getting Started](getting-started.md)\.
+ After you've completed the Getting Started exercise, you have two security groups, a VPC subnet, and a file system mount target for the file system that you want to back up\. For the rest of this walkthrough, you use the following example values:
  + The ID of the file system that you back up in this walkthrough is `fs-12345678`\.
  + The security group for the file system that is associated with the mount target is called `efs-mt-sg (sg-1111111a)`\.
  + The security group that grants Amazon EC2 instances the ability to connect to the production EFS mount point is called `efs-ec2-sg (sg-1111111b)`\.
  + The VPC subnet has the ID value of `subnet-abcd1234`\.
  + The source file system mount target IP address for the file system that you want to back up is `10.0.1.32:/`\.
  + The example assumes that the production file system is a content management system serving media files with an average size of 30 MB\.

The preceding assumptions and examples are reflected in the following initial setup diagram\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/1st-efs-backup-diagram.png)

## How to Back Up an Amazon EFS File System with AWS Data Pipeline<a name="backup-steps"></a>

Follow the steps in this section to back up or restore your Amazon EFS file system with AWS Data Pipeline\.

**Topics**
+ [Step 1: Create Your Backup Amazon EFS File System](#step1-create-backup)
+ [Step 2: Download the AWS Data Pipeline Template for Backups](#step2-download-template)
+ [Step 3: Create a Data Pipeline for Backup](#step3-create-pipeline)
+ [Step 4: Access Your Amazon EFS Backups](#step4-access-backup)

### Step 1: Create Your Backup Amazon EFS File System<a name="step1-create-backup"></a>

In this walkthrough, you create separate security groups, file systems, and mount points to separate your backups from your data source\. In this first step, you create those resources:

1. First, create two new security groups\. The example security group for the backup mount target is `efs-backup-mt-sg (sg-9999999a)`\. The example security group for the EC2 instance to access the mount target is `efs-backup-ec2-sg (sg-9999999b)`\. Remember to create these security groups in the same VPC as the EFS volume that you want to back up\. In this example, the VPC associated with the `subnet-abcd1234` subnet\. For more information about creating security groups, see [Creating security groups](accessing-fs-create-security-groups.md)\.

1. Next, create a backup Amazon EFS file system\. In this example, the file system ID is `fs-abcdefaa`\. For more information about creating file systems, see [Creating Amazon EFS file systems](creating-using-create-fs.md)\.

1. Finally, create a mount point for the EFS backup file system and assume that it has the value of `10.0.1.75:/`\. For more information about creating mount targets, see [Creating mount targets](accessing-fs.md)\.

After you've completed this first step, your setup should look similar to the following example diagram\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/2nd-efs-backup-diagram.png)

### Step 2: Download the AWS Data Pipeline Template for Backups<a name="step2-download-template"></a>

AWS Data Pipeline helps you reliably process and move data between different AWS compute and storage services at specified intervals\. By using the AWS Data Pipeline console, you can create preconfigured pipeline definitions, known as templates\. You can use these templates to get started with AWS Data Pipeline quickly\. For this walkthrough, a template is provided to make the process of setting up your backup pipeline easier\.

When implemented, this template creates a data pipeline that launches a single Amazon EC2 instance on the schedule that you specify to back up data from the production file system to the backup file system\. This template has a number of placeholder values\. You provide the matching values for those placeholders in the **Parameters** section of the AWS Data Pipeline console\. Download the AWS Data Pipeline template for backups at [1\-Node\-EFSBackupDataPipeline\.json](https://github.com/awslabs/data-pipeline-samples/blob/master/samples/EFSBackup/1-Node-EFSBackupPipeline.json) from GitHub\.

**Note**  
This template also references and runs a script to perform the backup commands\. You can download the script before creating the pipeline to review what it does\. To review the script, download [efs\-backup\.sh](https://github.com/awslabs/data-pipeline-samples/blob/master/samples/EFSBackup/efs-backup.sh) from GitHub\. This backup solution depends on running scripts that are hosted on GitHub and is subject to GitHub availability\. If you'd prefer to eliminate this reliance and host the scripts in an Amazon S3 bucket instead, see [Hosting the rsync Scripts in an Amazon S3 Bucket](#hostingscripts)\.

### Step 3: Create a Data Pipeline for Backup<a name="step3-create-pipeline"></a>

Use the following procedure to create your data pipeline\.

**To create a data pipeline for Amazon EFS backups**

1. Open the AWS Data Pipeline console at [https://console\.aws\.amazon\.com/datapipeline/](https://console.aws.amazon.com/datapipeline/)\.
**Important**  
Make sure that you're working in the same AWS Region as your Amazon EFS file systems\. 

1. Choose **Create new pipeline**\.

1. Add values for **Name** and optionally for **Description**\.

1. For **Source**, choose **Import a definition**, and then choose **Load local file**\.

1. In the file explorer, navigate to the template that you saved in [Step 2: Download the AWS Data Pipeline Template for Backups](#step2-download-template), and then choose **Open**\.

1. In **Parameters**, provide the details for both your backup and production EFS file systems\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/backuppipeline.png)

1. Configure the options in **Schedule** to define your Amazon EFS backup schedule\. The backup in the example runs once every day, and the backups are kept for a week\. When a backup is seven days old, it is replaced with next oldest backup\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/backupschedule.png)
**Note**  
We recommend that you specify a run time that occurs during your off\-peak hours\.

1. \(Optional\) Specify an Amazon S3 location for storing pipeline logs, configure a custom IAM role, or add tags to describe your pipeline\.

1. When your pipeline is configured, choose **Activate**\.

You’ve now configured and activated your Amazon EFS backup data pipeline\. For more information about AWS Data Pipeline, see the [AWS Data Pipeline Developer Guide](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/)\. At this stage, you can perform the backup now as a test, or you can wait until the backup is performed at the scheduled time\.

### Step 4: Access Your Amazon EFS Backups<a name="step4-access-backup"></a>

Your Amazon EFS backup has now been created, activated, and is running on the schedule you defined\. This step outlines how you can access your EFS backups\. Your backups are stored in the EFS backup file system that you created in the following format\.

```
backup-efs-mount-target:/efs-backup-id/[backup interval].[0-backup retention]-->
```

Using the values from the example scenario, the backup of the file system is located in `10.1.0.75:/fs-12345678/daily.[0-6]`, where `daily.0` is the most recent backup and `daily.6` is the oldest of the seven rotating backups\.

Accessing your backups gives you the ability to restore data to your production file system\. You can choose to restore an entire file system, or you can choose to restore individual files\.

#### Step 4\.1: Restore an Entire Amazon EFS Backup<a name="step4-1-full-restore"></a>

Restoring a backup copy of an Amazon EFS file system requires another AWS Data Pipeline, similar to the one you configured in [Step 3: Create a Data Pipeline for Backup](#step3-create-pipeline)\. However, this restoration pipeline works in the reverse of the backup pipeline\. Typically, these restorations aren't scheduled to begin automatically\.

As with backups, restores can be done in parallel to meet your recovery time objective\. Keep in mind that when you create a data pipeline, you need to schedule when you want it run\. If you choose to run on activation, you start the restoration process immediately\. We recommend that you only create a restoration pipeline when you need to do a restoration, or when you already have a specific window of time in mind\.

Burst capacity is consumed by both the backup EFS and restoration EFS\. For more information about performance, see [Amazon EFS Performance](performance.md)\. The following procedure shows you how to create and implement your restoration pipeline\.

**To create a data pipeline for EFS data restoration**

1. Download the data pipeline template for restoring data from your backup EFS file system\. This template launches a single Amazon EC2 instance based on the specified size\. It launches only when you specify it to launch\. Download the AWS Data Pipeline template for backups at [1\-Node\-EFSRestoreDataPipeline\.json ](https://github.com/awslabs/data-pipeline-samples/blob/master/samples/EFSBackup/1-Node-EFSRestorePipeline.json) from GitHub\.
**Note**  
This template also references and runs a script to perform the restoration commands\. You can download the script before creating the pipeline to review what it does\. To review the script, download [efs\-restore\.sh](https://github.com/awslabs/data-pipeline-samples/blob/master/samples/EFSBackup/efs-restore.sh) from GitHub\. 

1. Open the AWS Data Pipeline console at [https://console\.aws\.amazon\.com/datapipeline/](https://console.aws.amazon.com/datapipeline/)\.
**Important**  
Make sure that you're working in the same AWS Region as your Amazon EFS file systems and Amazon EC2\. 

1. Choose **Create new pipeline**\.

1. Add values for **Name** and optionally for **Description**\.

1. For **Source**, choose **Import a definition**, and then choose **Load local file**\.

1. In the file explorer, navigate to the template that you saved in [Step 1: Create Your Backup Amazon EFS File System](#step1-create-backup), and then choose **Open**\.

1. In **Parameters**, provide the details for both your backup and production EFS file systems\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/restorationpipeline.png)

1. Because you typically perform restorations only when you need them, you can schedule the restoration to run **once on pipeline activation**\. Or schedule a one\-time restoration at a future time of your choosing, like during an off\-peak window of time\.

1. \(Optional\) Specify an Amazon S3 location for storing pipeline logs, configure a custom IAM role, or add tags to describe your pipeline\.

1. When your pipeline is configured, choose **Activate**\.

You’ve now configured and activated your Amazon EFS restoration data pipeline\. Now when you need to restore a backup to your production EFS file system, you just activate it from the AWS Data Pipeline console\. For more information, see the [https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/)\.

#### Step 4\.2: Restore Individual Files from Your Amazon EFS Backups<a name="step4-2-partial-restore"></a>

You can restore files from your Amazon EFS file system backups by launching an Amazon EC2 instance to temporarily mount both the production and backup EFS file systems\. The EC2 instance must be a member of both of the EFS client security groups \(in this example, **efs\-ec2\-sg** and **efs\-backup\-clients\-sg**\)\. Both EFS mount targets can be mounted by this restoration instance\. For example, a recovery EC2 instance can create the following mount points\. Here, the `-o ro` option is used to mount the Backup EFS as read\-only to prevent accidentally modifying the backup when attempting to restore from a backup\.

```
mount -t nfs source-efs-mount-target:/ /mnt/data
```

```
mount -t nfs -o ro backup-efs-mount-target:/fs-12345678/daily.0 /mnt/backup>
```

After you've mounted the targets, you can copy files from `/mnt/backup` to the appropriate location in `/mnt/data` in the terminal using the `cp -p` command\. For example, an entire home directory \(with its file system permissions\) can be recursively copied with the following command\.

```
sudo cp -rp /mnt/backup/users/my_home /mnt/data/users/my_home
```

You can restore a single file by running the following command\.

```
sudo cp -p /mnt/backup/user/my_home/.profile /mnt/data/users/my_home/.profile
```

**Warning**  
When you are manually restoring individual data files, be careful that you don't accidentally modify the backup itself\. Otherwise, you might corrupt it\.

## Additional Backup Resources<a name="walkthrough4-appendix"></a>

The backup solution presented in this walkthrough uses templates for AWS Data Pipeline\. The templates used in [Step 2: Download the AWS Data Pipeline Template for Backups](#step2-download-template) and [Step 4\.1: Restore an Entire Amazon EFS Backup](#step4-1-full-restore) both use a single Amazon EC2 instance to perform their work\. However, there's no real limit to the number of parallel instances that you can run for backing up or restoring your data in Amazon EFS file systems\. In this topic, you can find links to other AWS Data Pipeline templates configured for multiple EC2 instances that you can download and use for your backup solution\. You can also find instructions for how to modify the templates to include additional instances\.

**Topics**
+ [Using Additional Templates](#additional-templates)
+ [Adding Additional Backup Instances](#add-backup-instances)
+ [Adding Additional Restoration Instances](#add-restore-instances)
+ [Hosting the rsync Scripts in an Amazon S3 Bucket](#hostingscripts)

### Using Additional Templates<a name="additional-templates"></a>

You can download the following additional templates from GitHub:
+ [2\-Node\-EFSBackupPipeline\.json](https://github.com/awslabs/data-pipeline-samples/blob/master/samples/EFSBackup/2-Node-EFSBackupPipeline.json) – This template starts two parallel Amazon EC2 instances to back up your production Amazon EFS file system\.
+ [2\-Node\-EFSRestorePipeline\.json](https://github.com/awslabs/data-pipeline-samples/blob/master/samples/EFSBackup/2-Node-EFSRestorePipeline.json) – This template starts two parallel Amazon EC2 instances to restore a backup of your production Amazon EFS file system\.

### Adding Additional Backup Instances<a name="add-backup-instances"></a>

You can add additional nodes to the backup templates used in this walkthrough\. To add a node, modify the following sections of the `2-Node-EFSBackupDataPipeline.json` template\.

**Important**  
If you're using additional nodes, you can't use spaces in file names and directories stored in the top\-level directory\. If you do, those files and directories aren't backed up or restored\. All files and subdirectories that are at least one level below the top level are backed up and restored as expected\.
+ Create an additional EC2Resource for each additional node you want to create \(in this example, a fourth EC2 instance\)\.

  ```
  {
  "id": "EC2Resource4",
  "terminateAfter": "70 Minutes",
  "instanceType": "#{myInstanceType}",
  "name": "EC2Resource4",
  "type": "Ec2Resource",
  "securityGroupIds" : [ "#{mySrcSecGroupID}","#{myBackupSecGroupID}" ],
  "subnetId": "#{mySubnetID}",
  "associatePublicIpAddress": "true"
  },
  ```
+ Create an additional data pipeline activity for each additional node \(in this case, activity `BackupPart4`\), make sure to configure the following sections:
  + Update the `runsOn` reference to point to the EC2Resource created previously \(`EC2Resource4` in the following example\)\.
  + Increment the last two `scriptArgument` values to equal the backup part that each node is responsible for and the total number of nodes\. For `"2"` and `"3"` in the example following, the backup part is `"3"` for the fourth node because in this example our modulus logic needs to count starting with 0\.

  ```
  {
  "id": "BackupPart4",
  "name": "BackupPart4",
  "runsOn": {
  "ref": "EC2Resource4"
  },
  "command": "wget https://raw.githubusercontent.com/awslabs/data-pipeline-samples/master/samples/EFSBackup/efs-backup-rsync.sh\nchmod a+x efs-backup-rsync.sh\n./efs-backup-rsync.sh $1 $2 $3 $4 $5 $6 $7",
  "scriptArgument": ["#{myEfsSource}","#{myEfsBackup}", "#{myInterval}", "#{myRetainedBackups}","#{myEfsID}", "3", "4"],
  "type": "ShellCommandActivity",
  "dependsOn": {
  "ref": "InitBackup"
  },
  "stage": "true"
  },
  ```
+ Increment the last value in all existing `scriptArgument` values to the number of nodes \(in this example, `"4"`\)\.

  ```
  {
  "id": "BackupPart1",
  ...
  "scriptArgument": ["#{myEfsSource}","#{myEfsBackup}", "#{myInterval}", "#{myRetainedBackups}","#{myEfsID}", "1", "4"],
  ...
  },
  {
  "id": "BackupPart2",
  ...
  "scriptArgument": ["#{myEfsSource}","#{myEfsBackup}", "#{myInterval}", "#{myRetainedBackups}","#{myEfsID}", "2", "4"],
  ...
  },
  {
  "id": "BackupPart3",
  ...
  "scriptArgument": ["#{myEfsSource}","#{myEfsBackup}", "#{myInterval}", "#{myRetainedBackups}","#{myEfsID}", "0", "4"],
  ...
  },
  ```
+ Update `FinalizeBackup` activity and add the new backup activity to the `dependsOn` list \(`BackupPart4` in this case\)\.

  ```
  { 
  "id": "FinalizeBackup", "name": "FinalizeBackup", "runsOn": { "ref":
  "EC2Resource1" }, "command": "wget
  https://raw.githubusercontent.com/awslabs/data-pipeline-samples/master/samples/EFSBackup/efs-backup-end.sh\nchmod a+x
  efs-backup-end.sh\n./efs-backup-end.sh $1 $2", "scriptArgument": ["#{myInterval}",
  "#{myEfsID}"], "type": "ShellCommandActivity", "dependsOn": [ { "ref": "BackupPart1" },
  { "ref": "BackupPart2" }, { "ref": "BackupPart3" }, { "ref": "BackupPart4" } ], "stage":
  "true"
  ```

### Adding Additional Restoration Instances<a name="add-restore-instances"></a>

You can add nodes to the restoration templates used in this walkthrough\. To add a node, modify the following sections of the `2-Node-EFSRestorePipeline.json` template\.
+ Create an additional EC2Resource for each additional node you want to create \(in this case, a third EC2 instance called `EC2Resource3`\)\.

  ```
  {
  "id": "EC2Resource3",
  "terminateAfter": "70 Minutes",
  "instanceType": "#{myInstanceType}",
  "name": "EC2Resource3",
  "type": "Ec2Resource",
  "securityGroupIds" : [ "#{mySrcSecGroupID}","#{myBackupSecGroupID}" ],
  "subnetId": "#{mySubnetID}",
  "associatePublicIpAddress": "true"
  },
  ```
+ Create an additional data pipeline activity for each additional node \(in this case, Activity `RestorePart3`\)\. Make sure to configure the following sections:
  + Update the `runsOn` reference to point to the `EC2Resource` created previously \(in this example, `EC2Resource3`\)\.
  + Increment the last two `scriptArgument` values to equal the backup part that each node is responsible for and the total number of nodes\. For `"2"` and `"3"` in the example following, the backup part is `"3"` for the fourth node because in this example our modulus logic needs to count starting with 0\.

  ```
  {
  "id": "RestorePart3",
  "name": "RestorePart3",
  "runsOn": {
  "ref": "EC2Resource3"
  },
  "command": "wget https://raw.githubusercontent.com/awslabs/data-pipeline-samples/master/samples/EFSBackup/efs-restore-rsync.sh\nchmod a+x efs-restore-rsync.sh\n./efs-backup-rsync.sh $1 $2 $3 $4 $5 $6 $7",
  "scriptArgument": ["#{myEfsSource}","#{myEfsBackup}", "#{myInterval}", "#{myBackup}","#{myEfsID}", "2", "3"],
  "type": "ShellCommandActivity",
  "dependsOn": {
  "ref": "InitBackup"
  },
  "stage": "true"
  },
  ```
+ Increment the last value in all existing `scriptArgument` values to the number of nodes \(in this example, `"3"`\)\.

  ```
  {
  "id": "RestorePart1",
  ...
  "scriptArgument": ["#{myEfsSource}","#{myEfsBackup}", "#{myInterval}", "#{myBackup}","#{myEfsID}", "1", "3"],
  ...
  },
  {
  "id": "RestorePart2",
  ...
  "scriptArgument": ["#{myEfsSource}","#{myEfsBackup}", "#{myInterval}", "#{myBackup}","#{myEfsID}", "0", "3"],
  ...
  },
  ```

### Hosting the rsync Scripts in an Amazon S3 Bucket<a name="hostingscripts"></a>

This backup solution is dependent on running rsync scripts that are hosted in a GitHub repository on the internet\. Therefore, this backup solution is subject to the GitHub repository being available\. This requirement means that if the GitHub repository removes these scripts, or if the GitHub website goes offline, the backup solution as implemented preceding doesn't function\.

If you'd prefer to eliminate this GitHub dependency, you can choose to host the scripts in an Amazon S3 bucket that you own instead\. Following, you can find the steps necessary to host the scripts yourself\.

**To host the rsync scripts in your own Amazon S3 bucket**

1. **Sign Up for AWS** – If you already have an AWS account, go ahead and skip to the next step\. Otherwise, see [Sign up for AWS](setting-up.md#setting-up-signup)\.

1. **Create an AWS Identity and Access Management User** – If you already have an IAM user, go ahead and skip to the next step\. Otherwise, see [Create an IAM User](setting-up.md#setting-up-iam)\.

1. **Create an Amazon S3 bucket** – If you already have a bucket that you want to host the rsync scripts in, go ahead and skip to the next step\. Otherwise, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

1. **Download the rsync scripts and templates** – Download all of the rsync scripts and templates in the [EFSBackup folder](https://github.com/awslabs/data-pipeline-samples/tree/master/samples/EFSBackup) from GitHub\. Make a note of the location on your computer where you downloaded these files\.

1. **Upload the rsync scripts to your S3 bucket** – For instructions on how to upload objects into your S3 bucket, see [Add an Object to a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

1. Change the permissions on the uploaded rsync scripts to allow **Everyone** to **Open/Download** them\. For instructions on how to change the permissions on an object in your S3 bucket, see [Editing Object Permissions](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/EditingPermissionsonanObject.html) in the *Amazon Simple Storage Service Console User Guide*\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/s3upload.png)

1. **Update your templates** – Modify the `wget` statement in the `shellCmd` parameter to point to the Amazon S3 bucket where you put the startup script\. Save the updated template, and use that template when you're following the procedure in [Step 3: Create a Data Pipeline for Backup](#step3-create-pipeline)\.
**Note**  
We recommend that you limit access to your Amazon S3 bucket to include the IAM account that activates the AWS Data Pipeline for this backup solution\. For more information, see [Editing Bucket Permissions](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/EditingBucketPermissions.html) in the *Amazon Simple Storage Service Console User Guide*\.

You are now hosting the rsync scripts for this backup solution, and your backups are no longer dependent on GitHub availability\.