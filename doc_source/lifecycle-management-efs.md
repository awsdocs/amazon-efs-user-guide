# Amazon EFS lifecycle management<a name="lifecycle-management-efs"></a>

Amazon EFS lifecycle management automatically manages cost\-effective file storage for your file systems\. When enabled, lifecycle management migrates files that have not been accessed for a set period of time to the EFS Standard–Infrequent Access \(Standard\-IA\) or One Zone–Infrequent Access \(One Zone\-IA\) storage class, depending on your file system\. You define that period of time by using a *lifecycle policy*\. Amazon EFS lifecycle management uses an internal timer to track when a file was last accessed, and not the POSIX file system attributes that are publicly viewable\. Whenever a file in Standard or One Zone storage is accessed, the lifecycle management timer is reset\. After lifecycle management moves a file into one of the IA storage classes, the file remains there indefinitely\. 

Metadata operations, such as listing the contents of a directory, don't count as file access\. During the process of transitioning a file's content to one of the IA storage classes, the file is stored in the Standard or One Zone storage class and billed at that storage rate\. 

Lifecycle management applies to all files in the file system\.

 You can move files from one of the IA storage classes to the Standard or One Zone storage class by copying them to another location on your file system\. If you want your files to remain in the Standard or One Zone storage class, disable lifecycle management and then copy your files\.

## Using a lifecycle policy<a name="lifecycle-policy"></a>

You define when Amazon EFS transitions files an IA storage class by setting a lifecycle policy\. A file system has one lifecycle policy that applies to the entire file system\. If a file is not accessed for the period of time defined by the lifecycle policy that you choose, Amazon EFS transitions the file to the IA storage class that is applicable to your file system\. For more information, see [Managing EFS storage classes](storage-classes.md)\. You can specify one of following lifecycle policies for your Amazon EFS file system:
+ `AFTER_7_DAYS`
+  `AFTER_14_DAYS` 
+  `AFTER_30_DAYS` 
+  `AFTER_60_DAYS` 
+  `AFTER_90_DAYS` 

To learn more about enabling lifecycle management on your Amazon EFS file system and setting a lifecycle policy, see [Using lifecycle management](#enable-lifecycle-management)\. 

## File system operations for lifecycle management<a name="metadata"></a>

File system operations for lifecycle management, which transition files to the file system's IA storage class \(either Standard\-IA or One Zone\-IA, depending on the file system\), have a lower priority than operations for EFS file system workloads\. The time required to transition files to an IA storage class varies depending on the file size and file system workload\. 

File metadata, including file names, ownership information, and file system directory structure, is always stored in Standard storage to ensure consistent metadata performance\. All write operations to files in the file system's IA storage class \(Standard\-IA or One Zone\-IA\) are first written to Standard or One Zone storage, depending on whether the file system uses Standard or One Zone storage classes, then transitioned to the applicable infrequent access storage class\. Files smaller than 128 KB aren't eligible for lifecycle management and are always stored in the EFS Standard or One Zone storage class\. 

## Using lifecycle management<a name="enable-lifecycle-management"></a>

Lifecycle management is automatically enabled with a 30 days since last access policy when you create an Amazon EFS file system using the service recommended settings\. For more information, see [Step 1: Create your Amazon EFS file system](gs-step-two-create-efs-resources.md)\.

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