# EFS lifecycle management<a name="lifecycle-management-efs"></a>

Amazon EFS lifecycle management automatically manages cost\-effective file storage for your file systems\. When enabled, lifecycle management migrates files that have not been accessed for a set period of time to the Infrequent Access \(IA\) storage class\. You define that period of time by using a *lifecycle policy\. *

After lifecycle management moves a file into the IA storage class, the file remains there indefinitely\. Amazon EFS lifecycle management uses an internal timer to track when a file was last accessed\. It doesn't use the POSIX file system attributes that are publicly viewable\. Whenever a file in Standard storage is written to or read from, the lifecycle management timer is reset\. 

Metadata operations, such as listing the contents of a directory, don't count as file access\. During the process of transitioning a file's content to IA storage, the file is stored in the Standard storage class and billed at the Standard storage rate\. 

Lifecycle management applies to all files in the file system\.

## Using a lifecycle policy<a name="lifecycle-policy"></a>

You define when EFS transitions files to the IA storage class by setting a lifecycle policy\. A file system has one lifecycle policy that applies to the entire file system\. If a file is not accessed for the period of time defined by the lifecycle policy that you choose, Amazon EFS transitions the file to the IA storage class\. You can specify one of following lifecycle policies for your Amazon EFS file system: 
+ `AFTER_7_DAYS`
+  `AFTER_14_DAYS` 
+  `AFTER_30_DAYS` 
+  `AFTER_60_DAYS` 
+  `AFTER_90_DAYS` 

To learn more about enabling lifecycle management on your Amazon EFS file system and setting a lifecycle policy, see [Using lifecycle management](#enable-lifecycle-management)\. 

## File system operations for lifecycle management<a name="metadata"></a>

File system operations for lifecycle management, which transition files to IA storage, have a lower priority than operations for EFS file system workloads\. The time required to transition files to the IA storage class varies depending on the file size and file system workload\. 

File metadata, including file names, ownership information, and file system directory structure, is always stored in Standard storage to ensure consistent metadata performance\. All write operations to files in IA storage are first written to Standard storage, then transitioned to IA storage\. Files smaller than 128 KB aren't eligible for lifecycle management and are always stored in the Standard class\. 

## Pricing and billing<a name="billing"></a>

You are billed for the amount of data in each storage class\. You are also billed for data access when files in IA storage are read and when files are transitioned to IA storage from Standard storage\. The AWS bill displays the capacity for each storage class and the metered access against the IA storage class\. To learn more, see [Amazon EFS Pricing](https://aws.amazon.com/efs/pricing)\.

**Note**  
You don't incur data access charges when using AWS Backup to back up lifecycle management\-enabled EFS file systems\. To learn more about AWS Backup and EFS lifecycle management, see [EFS storage classes](awsbackup.md#backups-storage-classes)\.

You can view how much data is stored in each storage class of your file system using the the Amazon EFS console, the AWS CLI, or the EFS API\.

### Viewing storage data size in the Amazon EFS console<a name="billing-metric"></a>

The `Storage bytes` graph metric displays the size of the file system in binary multiples of bytes \(kibibytes, mebibytes, gibibytes, and tebibytes\)\. The metric is emitted every 15 minutes and lets you view your file system's metered size over time\. `Storage bytes` displays the following information for the file system storage size:
+ `Total` is the size \(in binary bytes\) of data stored in the file system, including both storage classes\.
+ `Standard` is the size \(in binary bytes\) of data stored in the Standard storage class\.
+ `IA` is the size \(in binary bytes\) of data stored in the Infrequent Access \(IA\) storage class\.

You can view the `Storage bytes` metric on the **Monitoring** tab on the **File system metrics** page in the Amazon EFS console\. For more information, see [Accessing CloudWatch metrics](accessingmetrics.md)\.

### Viewing storage data size using the AWS CLI<a name="billing-cli"></a>

You can view how much data is stored in each storage class of your file system using the AWS CLI or EFS API\. View data storage details by calling the `describe-file-systems` CLI command \(the corresponding API operation is [DescribeFileSystems](API_DescribeFileSystems.md)\)\.

```
$  aws efs describe-file-systems \
--region us-west-2 \
--profile adminuser
```

In the response, `SizeInBytes`>`ValueInIA` displays the last metered size in bytes in IA storage\. `ValueInStandard` displays the last metered size in bytes in Standard storage\. Added together, they equal the size of the entire file system, displayed by `Value`\.

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

## Using lifecycle management<a name="enable-lifecycle-management"></a>

Lifecycle management is automatically enabled with a 30 days since last access policy when you create an Amazon EFS file system using the service recommended settings\. For more information, see [Step 1: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md)\.

If you create a new file system with customized settings, you can enable Lifecycle management in Step 1 of the configuration wizard\. For more information, see [Step 1: Configure file system settings](creating-using-create-fs.md#config-file-system-settings)\.

You can start, stop, and modify lifecycle management using the AWS Management Console and the AWS CLI, described as follows\.

### Start, stop, or modify lifecycle management on an existing file system \(console\)<a name="console2-enable-lifemgnt-filesystem"></a>

You can use the AWS Management Console to start, stop, or modify lifecycle management for an existing file system\.

**To enable lifecycle management on an existing file system \(console\)**

1. Sign in to the AWS Management Console and open the Amazon EFS console at [ https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File systems** to display the list of file systems in your account\.

   Choose the file system that you want to enable or modify lifecycle management on\. 

1. On the file system details page, in the **General** panel, choose **Edit**\. The **Edit >> General** settings page displays\.  
![\[Edit General settings page in the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-lifecycle-mgnt.png)

1. For **Lifecycle management** you can do the following:
   + Start lifecycle management – Choose one of the settings, from 7 to 90 days since last access, in the list\.
   + Stop lifecycle management – Choose **None**\.
   + Modify lifecycle management – Choose a different setting from the list\.

1. Choose **Save changes** to implement the lifecycle management modification\.

### Start, stop, or modify lifecycle management on an existing file system \(CLI\)<a name="lifecycle-mgnt-cli"></a>

You can use the AWS CLI to start, stop, or modify lifecycle management for an existing file system\.

**To start lifecycle management on an existing file system \(CLI\)**
+ Run the `put-lifecycle-configuration` command specifying the file system ID of the file system for which you are enabling lifecycle management\.

  ```
  $  aws efs put-lifecycle-configuration \
  --file-system-id File-System-ID \
  --lifecycle-policies TransitionToIA=AFTER_60_DAYS \
  --region us-west-2 \
  --profile adminuser
  ```

  You get the following response\.

  ```
  {
    "LifecyclePolicies": [
      {
          "TransitionToIA": "AFTER_60_DAYS"
      }
    ]
  }
  ```

**To stop lifecycle management for an existing file system \(CLI\)**
+  Run the `put-lifecycle-configuration` command specifying the file system ID of the file system for which you are stopping lifecycle management\. Keep the `--lifecycle-policies` property empty\.

  ```
  $  aws efs put-lifecycle-configuration \
  --file-system-id File-System-ID \
  --lifecycle-policies \
  --region us-west-2 \
  --profile adminuser
  ```

  You get the following response\.

  ```
  {
      "LifecyclePolicies": []
  }
  ```