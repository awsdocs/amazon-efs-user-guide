# Amazon EFS replication<a name="efs-replication"></a>

You can use Amazon EFS replication to create a replica of your Amazon EFS file system in the AWS Region of your preference\. When you enable replication on an EFS file system, Amazon EFS automatically and transparently replicates the data and metadata on the source file system to a new destination EFS file system\. To manage the process of creating the destination file system and keeping it synced with the source file system, Amazon EFS uses a *replication configuration*\. 

Amazon EFS automatically keeps the source and destination file systems synchronized\. Amazon EFS replication is continual and designed to provide a recovery point objective \(RPO\) and a recovery time objective \(RTO\) of minutes\. These features should assist you in meeting your compliance and business continuity goals\.

**Topics**
+ [What's in a replication configuration](#replication-properties)
+ [Permissions required](#efs-replication-permissions)
+ [Costs](#efs-replication-costs)
+ [Performance](#efs-replication-performance)
+ [Mounting a destination file system](#mounting-dest-file-system)
+ [Monitoring replication status](#monitoring-replication-status)
+ [Failing over to a destination file system](#replication-fail-over)
+ [Creating a replication configuration](#create-replication)
+ [Viewing replication configurations](#view-replications)
+ [Deleting a replication configuration](#delete-replications)

**Note**  
EFS replication does not support sockets and named pipes, or FIFOs, and does not replicate them to the destination file system\.

## What's in a replication configuration<a name="replication-properties"></a>

When you create a replication configuration, you specify the following properties for the destination file system:
+ **AWS Region** – The AWS Region in which to create the destination file system\. Amazon EFS replication is available in all AWS Regions that Amazon EFS is available in, except the following:
  + Africa \(Cape Town\)
  + Asia Pacific \(Hong Kong\)
  + Asia Pacific \(Jakarta\)
  + Europe \(Milan\)
  + Middle East \(Bahrain\)
+ **Availability and durability** – The storage class used by the destination file system, either **Regional** or **One Zone**\. For more information about EFS storage classes, see [EFS storage classes](storage-classes.md)\.
+ **Availability Zone** – If you choose **One Zone** availability and durability, you must choose the Availability Zone to create the destination file system in\.
+ **Encryption** – All destination file systems are created with encryption at rest enabled\. You can specify the AWS Key Management Service \(AWS KMS\) key that is used to encrypt the destination file system\. If you don't specify a KMS key, your service\-managed KMS key for Amazon EFS is used\. 
**Important**  
After the destination file system is created, you cannot change the KMS key\.

The following properties are set by default:
+ **Automatic backups** – Automatic daily backups are enabled on the destination file system\. After the file system is created, you can [change the automatic backup setting](awsbackup.md#automatic-backups)\.
+ **Performance mode** – The destination file system's performance mode matches that of the source file system, unless the destination file system uses EFS One Zone storage\. In that case, the General Purpose performance mode is used\. The performance mode cannot be changed\.
+ **Throughput mode** – The destination file system uses the Bursting Throughput mode by default\. After the file system is created, you can modify the throughput mode\.

The following properties are turned off by default:
+ **Lifecycle management** – EFS lifecycle management and EFS Intelligent\-Tiering are not enabled on the destination file system\. After the destination file system is created, you can enable [EFS lifecycle management and EFS Intelligent\-Tiering](lifecycle-management-efs.md#enable-lifecycle-management)\.

Amazon EFS creates the destination file system with read\-only permissions\. After the destination file system is created, Amazon EFS performs the initial sync that copies all data and metadata on the source to the destination file system\. The amount of time that the initial sync takes to finish depends on the size of the source file system\. After the initial sync is finished, the replication process continually keeps the destination file system in sync with the source\. For more information, see [Performance](#efs-replication-performance)\.

You can monitor when the last successful sync occurred using the console, the AWS Command Line Interface \(AWS CLI\), the API, and Amazon CloudWatch\. In CloudWatch, use the [TimeSinceLastSync](efs-metrics.md) EFS metric\. For more information, see [Monitoring replication status](#monitoring-replication-status)\.

**Note**  
An Amazon EFS file system can be part of only one replication configuration\. You cannot use a destination file system as the source file system in another replication configuration\.

## Permissions required<a name="efs-replication-permissions"></a>

Amazon EFS uses the EFS service\-linked role named `AWSServiceRoleForAmazonElasticFileSystem` to synchronize the state of the replication between the source and destination file systems\. In order to use EFS replication, you must configure the following permissions to allow an IAM entity \(such as a user, group, or role\) to create a service linked role, a replication configuration, and a file system\.
+ `elasticfilesystem:CreateReplicationConfiguration`\*
+ `elasticfilesystem:DeleteReplicationConfiguration`\*
+ `elasticfilesystem:DescribeReplicationConfigurations`\*
+ `elasticfilesystem:CreateFileSystem`\*
+ `iam:CreateServiceLinkedRole` – see the example in [Using the Amazon EFS Service\-Linked Role](using-service-linked-roles.md)\.

\* You can use the `AmazonElasticFileSystemFullAccess` managed policy instead to automatically get all EFS permissions\.

## Costs<a name="efs-replication-costs"></a>

In order facilitate replication, Amazon EFS creates hidden directories and metadata on the destination file system\. These equate to approximately 12 MiB of metered data for which you are billed\. For more information about metering file system storage, see [Metering: How Amazon EFS reports file system and object sizes](metered-sizes.md)\.

## Performance<a name="efs-replication-performance"></a>

After the initial replication is finished, the majority of source file systems have their subsequent changes replicated to their destination file systems within 15 minutes\. However, if the source file system has files that change very frequently and has either more than 100 million files or files that are larger than 100 GB, replication will take longer than 15 minutes\. For information about monitoring when the last replication successfully finished, see [Monitoring replication status](#monitoring-replication-status)\.

## Mounting a destination file system<a name="mounting-dest-file-system"></a>

Amazon EFS does not create any mount targets when it creates the destination file system\. To [mount a destination file system](efs-mount-helper.md), you must [create one or more mount targets](accessing-fs.md)\. 

Because a destination file system is read\-only while it is a member of a replication configuration, any write operations to it will fail\. However, you can use the destination file system for read\-only use\-cases, including testing and development\.

## Monitoring replication status<a name="monitoring-replication-status"></a>

You can monitor the time when the last successful sync was completed in a replication configuration\. Any changes to data on the source file system that occurred before this time have been successfully replicated to the destination file system\. Any changes that occurred after this time might not be fully replicated\. To monitor when the last replication successfully finished, you can use the console, CLI, API, or Amazon CloudWatch\.
+ **In the console** – The **Last synced** property in the **File system details** > **Replication** section shows the time when the last successful sync between the source and destination was completed\.
+ **In the CLI or API** – The `LastReplicatedTimestamp` property in the `Destination` object shows the time that the last successful sync was completed\. To access this property, use the `describe-replication-configurations` CLI command\. [DescribeReplicationConfigurations](API_DescribeReplicationConfigurations.md) is the equivalent API operation\.
+ **In CloudWatch** – The `TimeSinceLastSync` CloudWatch metric for Amazon EFS shows the time that has elapsed since the last successful sync was completed\. For more information, see [Amazon CloudWatch metrics for Amazon EFS](efs-metrics.md)\.

You can also monitor a replication configuration's status by using the console, CLI, or API\. A replication configuration can have one of the status values described in the following table\.


| Replication status  | Description | 
| --- | --- | 
| `ENABLED` | The replication configuration is in a healthy state and available for use\. | 
| `ENABLING` | Amazon EFS is in the process of creating the replication configuration\. | 
| `DELETING` | Amazon EFS is deleting the replication configuration in response to a user\-initiated delete request\. | 
| `ERROR` | One \(or both\) of the file systems in the replication configuration is in a failed state and is unrecoverable\. To access the file system data, restore a backup of the failed file system to a new file system\. For more information, see [Restore a recovery point](awsbackup.md#restoring-backup-efs)\. | 

## Failing over to a destination file system<a name="replication-fail-over"></a>

To fail over to the destination file system in a replication configuration, you must delete the replication configuration\. After the replication configuration is deleted, the destination file system becomes writeable, and you can start using it in your application workflow\. This process can take several minutes to complete\. For more information, see [Deleting a replication configuration](#delete-replications)\.

After failing over to a destination file system, that file system can then be used as the source file system in a new replication configuration\. However, it cannot be designated as a destination file system again\. A replication configuration always creates a new EFS file system for the destination\.

## Creating a replication configuration<a name="create-replication"></a>

You can use the Amazon EFS console, the API, or the AWS CLI to replicate an existing Amazon EFS file system\. The following sections provide you with detailed instructions for using each of these methods\.

### To create a replication configuration \(console\)<a name="create-replication-console"></a>

1. Sign in to the AWS Management Console and open the Amazon EFS console at [ https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. In the left navigation pane, choose **File systems**\. 

1. In the **File systems** list, choose the Amazon EFS file system that you want to replicate\. The file system that you choose cannot be a source or destination file system in an existing replication configuration\.

1. Choose the **Replication** tab to display the file system's replication section\. The section should be blank\. If it is not, choose a different file system to be the source\.

1. If the file system is not already replicated, choose **Create replication** to display the **Create replication** page\.  
![\[The Create replication page in the Amazon EFS console is used to create a replication configuration.\]](http://docs.aws.amazon.com/efs/latest/ug/images/efs-console-create-regional-replication.png)

1. For **Replication settings**, choose the following options:
   + **Destination Region** – The AWS Region in which you want to create the destination file system\.
   + **Availability and durability** – Choose **Regional** or **One Zone**\.
     + To create a file system that has the highest levels of availability and durability, choose **Regional**\. The destination file system will use EFS Standard storage\. With EFS Standard storage, your file system data and metadata are stored redundantly across multiple geographically separated Availability Zones within an AWS Region\. For more information, see [EFS storage classes](storage-classes.md)\.
     + To create a file system that uses EFS One Zone storage, choose **One Zone**\. With EFS One Zone storage, your file system data and metadata are stored redundantly within a single Availability Zone within an AWS Region\. For more information, see [EFS storage classes](storage-classes.md)\.
   + If you choose **One Zone**, you must also choose the Availability Zone to create the file system in\.
   + **Encryption** – Encryption of data at rest is automatically enabled on the destination file system\. By default, Amazon EFS uses your AWS Key Management Service \(AWS KMS\) service key for Amazon EFS \(`aws/elasticfilesystem`\)\. To use a different KMS key, choose a KMS key from the list, or enter the ARN for an existing key\. 
**Important**  
After the file system is created, you cannot change the KMS key\.

1. Choose **Create replication**\. The Replication section is displayed, showing the replication details\. The **Replication state** value is initially **Enabling**, and **Last synced** is blank\. After the state reads **Enabled**, **Last synced** shows **Initial sync in progress**\.  
![\[The File system details > Replication section displays the replication configuration.\]](http://docs.aws.amazon.com/efs/latest/ug/images/efs-console-regional-replication-panel.png)

1. To see the destination file system's configuration information, choose the file system ID above **Destination file system**\. The **File system details** page for the destination file system displays in a new browser tab \(depending on your browser settings\)\.

### To create a replication configuration \(CLI\)<a name="create-replication-cli"></a>

To create a replication configuration for an existing file system, use the `create-replication-configuration` CLI command\. The equivalent API command is [CreateReplicationConfiguration](API_CreateReplicationConfiguration.md)\.
+ You can specify an `AvailabilityZoneName` parameter, a `Region` parameter, or both\. To create a file system that uses EFS One Zone storage, use the `AvailabilityZoneName` parameter and specify the Availability Zone that you want the file system to be created in\. Including both a **Region** and an **Availability Zone** also creates a file system with One Zone storage\. To create a file system that uses EFS Standard storage, which is redundant across multiple Availability Zones within an AWS Region, specify only the `Region` parameter\.
+ Encryption of data at rest is automatically enabled on the destination file system\. By default, Amazon EFS uses your AWS Key Management Service \(AWS KMS\) service key for Amazon EFS \(`aws/elasticfilesystem`\)\. You can optionally specify a `KmsKeyId` value to use for encryption using by specifying either a KMS key ID or ARN\.
**Important**  
After the file system is created, you cannot change the KMS key\.

**Example – Create a replication configuration using EFS One Zone storage**  
The following example creates a replication configuration for the file system *`fs-0123456789abcdef1`*\. This example uses the `AvailabilityZoneName` parameter to create a destination file system that uses EFS One Zone storage in the `us-west-2a` Availability Zone\. Because no KMS key is specified, the destination file system is encrypted using the account's default AWS KMS service key for Amazon EFS \(`aws/elasticfilesystem`\)\.  

```
aws efs create-replication-configuration \
--source-file-system-id fs-0123456789abcdef1 \
--destinations AvailabilityZoneName=us-west-2a
```

**Example – Create a Regional replication configuration using EFS Standard storage**  
The following example creates a replication configuration for the file system `fs-0123456789abcdef1`\. This example uses the `Region` parameter to create a destination file system with EFS Standard storage in the `eu-west-2` AWS Region\. The `KmsKeyId` parameter specifies the KMS key ID to use when encrypting the destination file system\.  

```
aws efs create-replication-configuration \
--source-file-system-id fs-0123456789abcdef1 \
--destinations [{\"Region\":\"eu-west-2\"},{\"KmsKeyId\":\"aaa-0123456789abcdef0123456789abcdef\"}]
```

```
{
    "SourceFileSystemArn": "arn:aws:elasticfilesystem:us-east-1:111122223333:file-system/fs-0123456789abcdef1", 
    "SourceFileSystemRegion": "us-east-1", 
    "Destinations": [
        {
            "Status": "ENABLING", 
            "FileSystemId": "fs-0123456789abcde22", 
            "Region": "eu-west-2"
        }
    ], 
    "SourceFileSystemId": "fs-0123456789abcdef1", 
    "CreationTime": 1641491892.0, 
    "OriginalSourceFileSystemArn": "arn:aws:elasticfilesystem:us-east-1:111122223333:file-system/fs-0123456789abcdef1"
}
```

## Viewing replication configurations<a name="view-replications"></a>

To see a file system's replication configuration, you can use the Amazon EFS console or the AWS CLI\.

### To view a replication configuration \(console\)<a name="view-replications-console"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. In the left navigation pane, choose **File systems**\.

1. Choose a file system from the list\.

1. Choose the **Replication** tab to display the **Replication** section\.  
![\[The File system details > Replication section displays the file system's replication configuration information.\]](http://docs.aws.amazon.com/efs/latest/ug/images/efs-console-replication-panel.png)

   In the **Replication** section, you can see the following information for the replication configuration:
   + **Replication state** reads either **Enabling**, **Enabled**, **Deleting**, or **Error**\. The **Error** state occurs when either the source or the destination file system \(or both\) is in a failed state and is unrecoverable\. For more information, see [Monitoring replication status](#monitoring-replication-status)\. To recover, you must delete the replication configuration, and then restore the most recent backup of the failed file system \(either the source or the destination\) to a new file system\.
   + **Replication direction** shows the direction in which data is being replicated\. The first file system listed is the source, and its data is being replicated *to* the second file system listed, which is the destination\.
   + **Last synced** shows when the last successful sync occurred on the destination file system\. Any changes to data on the source file system that occurred before this time were successfully replicated to the destination file system\. Any changes that occurred after this time might not be fully replicated\.
   + **Replication file systems** lists each file system in the replication configuration by its file system ID, the role it has in the replication configuration \(either source or destination\), the AWS Region in which it's located, and its **Permission**\. A source file system has a permission of **Writable**, and a destination file system has a permission of **Read\-only**\.

### To view a replication configuration \(CLI\)<a name="view-replications-cli"></a>

To view a replication configuration, use the `describe-replication-configurations` CLI command\. You can view the replication configuration for either a specific file system, or all replication configurations for a particular AWS account in an AWS Region\. The equivalent API command is [DescribeReplicationConfigurations](API_DescribeReplicationConfigurations.md)\.

To view the replication configuration for a file system, use the `file-system-id` URI request parameter\. You can specify the ID of either a source or destination file system\.

```
aws efs describe-replication-configurations --file-system-id fs-0123456789abcdef1
```

```
{
    "Replications": [
        {
            "SourceFileSystemArn": "arn:aws:elasticfilesystem:eu-west-1:111122223333:file-system/fs-abcdef0123456789a", 
            "CreationTime": 1641491892.0,
            "SourceFileSystemRegion": "eu-west-1", 
            "OriginalSourceFileSystemArn": "arn:aws:elasticfilesystem:eu-west-1:111122223333:file-system/fs-abcdef0123456789a", 
            "SourceFileSystemId": "fs-abcdef0123456789a", 
            "Destinations": [
                {
                    "Status": "ENABLED", 
                    "FileSystemId": "fs-0123456789abcdef1", 
                    "Region": "us-east-1"
                }
            ]
        }
    ]
}
```

To view all the replication configurations for an account in an AWS Region, don't specify the `file-system-id` parameter\.

```
aws efs describe-replication-configurations
```

```
{
    "Replications": [
        {
            "SourceFileSystemArn": "arn:aws:elasticfilesystem:eu-west-1:555555555555:file-system/fs-0123456789abcdef1", 
            "CreationTime": 1641491892.0, 
            "SourceFileSystemRegion": "eu-west-1", 
            "OriginalSourceFileSystemArn": "arn:aws:elasticfilesystem:eu-west-1:555555555555:file-system/fs-0123456789abcdef1", 
            "SourceFileSystemId": "fs-0123456789abcdef1", 
            "Destinations": [
                {
                    "Status": "ENABLED", 
                    "FileSystemId": "fs-abcdef0123456789a", 
                    "Region": "us-east-1",
                    "LastReplicatedTimestamp": 1641491802.375
                }
            ]
        }, 
        {
            "SourceFileSystemArn": "arn:aws:elasticfilesystem:eu-west-1:555555555555:file-system/fs-021345abcdef6789a", 
            "CreationTime": 1641491822.0, 
            "SourceFileSystemRegion": "eu-west-1", 
            "OriginalSourceFileSystemArn": "arn:aws:elasticfilesystem:eu-west-1:555555555555:file-system/fs-021345abcdef6789a", 
            "SourceFileSystemId": "fs-021345abcdef6789a", 
            "Destinations": [
                {
                    "Status": "ENABLED", 
                    "FileSystemId": "fs-012abc3456789def1", 
                    "Region": "us-east-1", 
                    "LastReplicatedTimestamp": 1641491823.575
                }
            ]
        }
    ]
}
```

## Deleting a replication configuration<a name="delete-replications"></a>

You can delete an existing replication configuration by using the console, the CLI, or the API\. If you need to fail over to the destination file system, delete the replication configuration of which it is a member\. After you delete a replication configuration, the destination file system is no longer in a read\-only state\.

Deleting a replication configuration and changing the destination file system to be write\-able can take several minutes to complete\. After the configuration is deleted, Amazon EFS might write some data to a `lost+found` directory in the root directory of the destination file system, using the following naming convention:

```
efs-replication-lost+found-source-file-system-id-TIMESTAMP
```

**Note**  
You cannot delete a file system that is part of a replication configuration\. You must delete the replication configuration before deleting the file system\.

When deleting a replication configuration, you must issue the delete request from the same AWS Region that the destination file system is located in\. The Amazon EFS console manages this for you, but you must ensure that you are in the correct AWS Region when using the CLI and API\.

### To delete a replication configuration \(console\)<a name="delete-replications-console"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. In the left navigation pane, choose **File systems**\.

1. Choose either the source or the destination file system that is in the replication configuration that you want to delete\.

1. Choose the **Replication** tab to display the **Replication** section\.

1. Choose **Delete** to delete the replication configuration\. When prompted, confirm your choice\.

### To delete a replication configuration \(CLI\)<a name="delete-replications-cli"></a>

To delete a replication configuration, use the `delete-replication-configuration` CLI command\. The equivalent API command is [DeleteReplicationConfiguration](API_DeleteReplicationConfiguration.md)\.

To specify which replication configuration that you're deleting, use the `source-file-system-id` parameter\. 

**Note**  
The `delete-replication-configuration` CLI request must be called from the same AWS Region in which the destination file system is located\.

```
aws efs --region us-west-2 delete-replication-configuration \
--source-file-system-id fs-0123456789abcdef1
```