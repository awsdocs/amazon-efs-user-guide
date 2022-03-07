# Amazon EFS lifecycle management<a name="lifecycle-management-efs"></a>

Amazon EFS lifecycle management automatically manages cost\-effective file storage for your file systems\. When enabled, lifecycle management migrates files that have not been accessed for a set period of time to the EFS Standard–Infrequent Access \(Standard\-IA\) or One Zone–Infrequent Access \(One Zone\-IA\) storage class, depending on your file system\. You define that period of time by using the **Transition into IA** *lifecycle policy*\.

Amazon EFS lifecycle management uses an internal timer to track when a file was last accessed, and not the POSIX file system attributes that are publicly viewable\. Whenever a file in Standard or One Zone storage is accessed, the lifecycle management timer is reset\. After lifecycle management moves a file into one of the IA storage classes, the file remains there indefinitely if Amazon EFS Intelligent\-Tiering is not enabled\.

Metadata operations, such as listing the contents of a directory, don't count as file access\. During the process of transitioning a file's content to one of the IA storage classes, the file is stored in the Standard or One Zone storage class and billed at that storage rate\. 

Lifecycle management applies to all files in the file system\.

## Amazon EFS Intelligent\-Tiering<a name="intelligent-tiering-efs"></a>

Amazon EFS Intelligent‐Tiering uses lifecycle management to monitor the access patterns of your workload and is designed to automatically transition files to and from the file system's Infrequent Access \(IA\) storage class\. With intelligent tiering, files in the standard storage class \(EFS Standard or EFS One Zone\) that are not accessed for the duration of the **Transition into IA** lifecycle policy setting, for example 30 days, are transitioned to the corresponding Infrequent Access \(IA\) storage class\. Additionally, if access patterns change, EFS Intelligent‐Tiering automatically moves files back to the EFS Standard or EFS One Zone storage classes when the **Transition out of IA** lifecycle policy is set to **On first access**\. This helps to eliminate the risk of unbounded access charges, while providing consistent low latencies\.

## Using lifecycle policies<a name="lifecycle-policy"></a>

Amazon EFS supports two lifecycle policies\. **Transition into IA** instructs lifecycle management when to transition files into the file systems's Infrequent Access storage class\. **Transition out of IA** instructs intelligent tiering when to transition files out of IA storage\. Lifecycle policies apply to the entire Amazon EFS file system\.

The **Transition into IA** lifecycle policy has the following values:
+ **None**
+ **7 days since last access**
+ **14 days since last access**
+ **30 days since last access**
+ **60 days since last access**
+ **90 days since last access**

The **Transition out of IA** lifecycle policy can have the following values
+ **None**
+ **On first access**

You can combine the two EFS lifecycle policies to achieve a variety of storage patterns, shown in the following table\.


| Transition into IA7\-90 days | Transition out of IA | Effect | 
| --- | --- | --- | 
| On | Off | Classic lifecycle management policyFiles stay in IA storage | 
| On | On | EFS Intelligent\-TieringFiles move into and out of IA storage | 
| Off | On |  All files in IA are moved into standard, eventually | 
| Off | Off | Only standard storage class is used | 

For more information about setting lifecycle policies, see [Using EFS Lifecycle Management and EFS Intelligent Tiering](#enable-lifecycle-management)\. 

## File system operations for lifecycle management<a name="metadata"></a>

File system operations for lifecycle management and intelligent tiering, have a lower priority than operations for EFS file system workloads\. The time required to transition files into or out of IA storage varies depending on the file size and file system workload\.

File metadata, including file names, ownership information, and file system directory structure, is always stored in standard storage \(Standard or One Zone\) to ensure consistent metadata performance\. All write operations to files in the file system's IA storage class \(Standard\-IA or One Zone\-IA\) are first written to standard storage, then transitioned to the applicable infrequent access storage class\. Files smaller than 128 KB aren't eligible for lifecycle management and are always stored in the file system's standard storage class\.

## Using EFS Lifecycle Management and EFS Intelligent Tiering<a name="enable-lifecycle-management"></a>

When you create an Amazon EFS file system that uses the service recommended settings using the AWS Management Console, the file system's lifecycle policies use the following default settings:
+ **Transition into IA** is set to **30 days since last access**
+ **Transition out of IA** is set to **On first access**

For more information about creating a file system with the service recommended settings, see [Step 1: Create your Amazon EFS file system](gs-step-two-create-efs-resources.md)\.

When you create a new file system with customized settings, you can set the lifecycle policies as needed\. For more information, see [Creating a file system with custom settings using the Amazon EFS console](creating-using-create-fs.md#creating-using-fs-part1-console)\.

You can configure lifecycle policies using the AWS Management Console and the AWS CLI, as described in the following procedures\.

### Manage lifecycle policies on an existing file system \(console\)<a name="console2-enable-lifemgnt-filesystem"></a>

You can use the AWS Management Console to set the lifecycle policies for an existing file system\.

1. Sign in to the AWS Management Console and open the Amazon EFS console at [ https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File systems** to display the list of file systems in your account\.

   Choose the file system on which you want to modify lifecycle policies\.

1. On the file system details page, in the **General** panel, choose **Edit**\. The **Edit >> General** settings page displays\.  
![\[Edit General settings page in the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-edit-fs-general-INT.png)

1. For Lifecycle management, you can change the following lifecycle policies:
   + Set **Transition into IA** to one of the available settings\. To stop moving files into IA storage, choose **None**\.
   + Set **Transition out of IA** to **On first access** to move files that are in IA storage to standard storage when they're accessed for non\-metadata operations\.

     To stop moving files from IA to standard storage on first access, set to **None**\.

1. Choose **Save changes** to save your changes\.

### Manage lifecycle policies on an existing file system \(CLI\)<a name="lifecycle-mgnt-cli"></a>

You can use the AWS CLI to set or modify a file system's lifecycle policies;\.
+ Run the [https://docs.aws.amazon.com/aws-cli/elasticfilesystem-2015-02-01/PutLifecycleConfiguration](https://docs.aws.amazon.com/aws-cli/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) AWS CLI command or the [PutLifecycleConfiguration](API_PutLifecycleConfiguration.md) API command, specifying the file system ID of the file system for which you are managing lifecycle management\.

  ```
  $  aws efs put-lifecycle-configuration \
  --file-system-id File-System-ID \
  --lifecycle-policies "[{\"TransitionToIA\":\"AFTER_60_DAYS\"},{\"TransitionToPrimaryStorageClass\":\"AFTER_1_ACCESS\"}]" \
  --region us-west-2 \
  --profile adminuser
  ```

  You get the following response\.

  ```
  {
      "LifecyclePolicies": [
          {
              "TransitionToIA": "AFTER_60_DAYS"
          },
          {
              "TransitionToPrimaryStorageClass": "AFTER_1_ACCESS"
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