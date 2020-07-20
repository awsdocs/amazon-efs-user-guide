# EFS lifecycle management<a name="lifecycle-management-efs"></a>

Amazon EFS lifecycle management automatically manages cost\-effective file storage for your file systems\. When enabled, lifecycle management migrates files that have not been accessed for a set period of time to the Infrequent Access \(IA\) storage class\. You define that period of time by using a *lifecycle policy\. *

After lifecycle management moves a file into the IA storage class, the file remains there indefinitely\. Amazon EFS lifecycle management uses an internal timer to track when a file was last accessed\. It doesn't use the POSIX file system attributes that are publicly viewable\. Whenever a file in Standard storage is written to or read from, the lifecycle management timer is reset\. 

Metadata operations, such as listing the contents of a directory, don't count as file access\. During the process of transitioning a file's content to IA storage, the file is stored in the Standard storage class and billed at the Standard storage rate\. 

Lifecycle management applies to all files in the file system\.

## Using a lifecycle policy<a name="lifecycle-policy"></a>

You define when EFS transitions files to the IA storage class by setting a lifecycle policy\. A file system has one lifecycle policy that applies to the entire file system\. If a file is not accessed for the period of time defined by the lifecycle policy that you choose, Amazon EFS transitions the file to the IA storage class\. You can specify one of four lifecycle policies for your Amazon EFS file system, as follows: 
+ `AFTER_7_DAYS`
+  `AFTER_14_DAYS` 
+  `AFTER_30_DAYS` 
+  `AFTER_60_DAYS` 
+  `AFTER_90_DAYS` 

To learn more about enabling lifecycle management on your Amazon EFS file system and setting a lifecycle policy, see [Using lifecycle management](enable-lifecycle-management.md)\. 

## File system operations for lifecycle management<a name="metadata"></a>

File system operations for lifecycle management, which transition files to IA storage, have a lower priority than operations for EFS file system workloads\. The time required to transition files to the IA storage class varies depending on the file size and file system workload\. 

File metadata, including file names, ownership information, and file system directory structure, is always stored in Standard storage to ensure consistent metadata performance\. All write operations to files in IA storage are first written to Standard storage, then transitioned to IA storage\. Files smaller than 128 KB aren't eligible for lifecycle management and are always stored in the Standard class\. 

## Pricing and billing<a name="billing"></a>

You are billed for the amount of data in each storage class\. You are also billed for data access when files in IA storage are read and when files are transitioned to IA storage from Standard storage\. The AWS bill displays the capacity for each storage class and the metered access against the IA storage class\. To learn more, see [Amazon EFS Pricing](https://aws.amazon.com/efs/pricing)\.

**Note**  
You don't incur data access charges when using AWS Backup to back up lifecycle management\-enabled EFS file systems\. To learn more about AWS Backup and EFS lifecycle management, see [EFS storage classes](awsbackup.md#backups-storage-classes)\.

You can view how much data is stored in each storage class of your file system using the AWS CLI or EFS API\. View data storage details by calling the `describe-file-systems` CLI command\.

```
$  aws efs describe-file-systems \
--region us-west-2 \
--profile adminuser
```

In the response, `ValueInIA` displays the last metered size in IA storage\. `ValueInStandard` displays the last metered size in Standard storage\. Added together, they equal the size of the entire file system, displayed by `Value`\.

```
{
   "FileSystems":[
      {
         "OwnerId":"251839141158",
         "CreationToken":"MyFileSystem1",
         "FileSystemId":"fs-47a2c22e",
         "PerformanceMode" : "generalPurpose",
         "CreationTime": 1403301078,
         "LifeCycleState":"created",
         "NumberOfMountTargets":1,
         "SizeInBytes":{
            "Value": 29313417216,
            "ValueInIA": 675432,
            "ValueInStandard": 29312741784
        },
        "ThroughputMode": "bursting"
      }
   ]
}
```

For additional ways to view and measure disk usage, see [Metering Amazon EFS file system objects](metered-sizes.md#metered-sizes-fs-objects)\.