# EFS Lifecycle Management<a name="lifecycle-management-efs"></a>

EFS Lifecycle Management automatically manages cost\-effective file storage\.

When enabled, lifecycle management automatically migrates files that have not been accessed for 30 days to the EFS IA storage class\. Once lifecycle management moves a file into the IA storage class, it remains there indefinitely\. In EFS, lifecycle management uses an internal timer to track when a file was last accessed\. It doesn't use the publicly viewable POSIX file system attributes\. Whenever a file in Standard storage is written to or read from, the lifecycle management timer is reset\. Metadata operations such as listing the contents of a directory don't count as file access\. During the process of transitioning a file's content to IA storage, the file is stored in the Standard storage class and billed at the Standard storage rate\.

File metadata, such as file names, ownership information, and file system directory structure, is always stored in Standard storage to ensure consistent metadata performance\. All writes to files in IA storage are first written to Standard storage, then transitioned to IA storage\. Files smaller than 128 KB are not eligible for lifecycle management and are always stored in the standard class\.

Lifecycle management applies to all files in the file system\. For file systems created after February 13, 2019, you can enable or disable lifecycle management at any time\.

**Important**  
If you want to use lifecycle management with existing file systems created before February 13, 2019, create a new EFS file system and enable lifecycle management\. Then copy the data from the older file system into the new file system\.

Operations to transition files for lifecycle management have lower priority than EFS file system workloads, and the time required to transition files to the IA storage class varies\. There are data access charges when data is read from IA storage, and when file content is transitioned to IA storage\. 

The AWS bill displays the capacity for each storage class and the metered access against the IA storage class\. For more information on IA storage class pricing, see [Amazon EFS Pricing](https://aws.amazon.com/efs/pricing)\.

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

For additional ways to view and measure disk usage, see [Metering Amazon EFS File System Objects](metered-sizes.md#metered-sizes-fs-objects)\.

## Enabling Lifecycle Management<a name="enable-lifecycle-management"></a>

You can enable lifecycle management anytime on an EFS file system by using the EFS console, CLI, or API\. Following, find procedures for enabling and disabling EFS Lifecycle Management on EFS file systems created on or after February 13, 2019\.

### Enable Lifecycle Management \(Console\)<a name="enable-lifecycle-console"></a>

You can use the AWS Management Console to enable lifecycle management as follows\.

**Note**  
The Amazon EFS console displays the Lifecycle Management option only for file systems created on or after February 13, 2019\.

**To enable lifecycle management \(console\)**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File Systems**\.

1. Choose the file system on which to enable lifecycle management\.

1. Locate **Lifecycle policy** in **Other details**\. Choose edit to change the setting\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/enable_disable_lifecycle_mgt_existing.png)

1. Choose the check box for **Enable Lifecycle Management**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/lifecycle_change.png)

1. Choose **Save** to save the setting\.

### Enable Lifecycle Management \(AWS CLI\)<a name="enable-lifecycle-cli"></a>

You can use the AWS CLI to enable lifecycle management as follows\.  

**To enable lifecycle management \(AWS CLI\)**
+ Run the `put-lifecycle-configuration` command\.

  ```
  $  aws efs put-lifecycle-configuration \
  --file-system-id File-System-ID \
  --lifecycle-policies TransitionToIA=AFTER_30_DAYS \
  --region us-west-2 \
  --profile adminuser
  ```

  You get the following response\.

  ```
  {
    "LifecyclePolicies": [
      {
          "TransitionToIA": "AFTER_30_DAYS"
      }
    ]
  }
  ```

  If the file system is not eligible to use lifecycle management because it was created before February 13, 2019, you get the following response\.

  ```
  File systems created before February 13, 2019 do not support lifecycle management. 
  Apply a lifecycle configuration to a file system created after February 13, 2019.
  ```

  To enable lifecycle management in this case, create a new EFS file system and enable lifecycle management for that file system\.

## Disabling EFS Lifecycle Management<a name="disable-lifecycle-mgnt"></a>

You can disable lifecycle management at any time using the EFS Management Console, the CLI, or the EFS API\. When you disable lifecycle management, your files are no longer moved into the IA storage class\. Any files currently in IA storage remain there\. You can move files from IA to Standard storage by copying them to another location on your file system\. 

### Disable Lifecycle Management \(Console\)<a name="disable-lifecycle-console"></a>

You can disable EFS lifecycle management using the EFS Management Console as follows\.

**To disable lifecycle management \(console\)**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File Systems**\.

1. Choose the file system on which to disable lifecycle management\.

1. Locate **Lifecycle policy** in **Other details**\. Choose edit to change the setting\.

1. Clear the check box for **Enable Lifecycle Management**\.

1. Choose **Save** to save the setting\.

### Disable Lifecycle Management \(AWS CLI\)<a name="disable-lifecycle-cli"></a>

You can disable EFS lifecycle management using the AWS CLI as follows\.

**To disable lifecycle management \(AWS CLI\)**
+ Run the `put-lifecycle-configuration` command\.  

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