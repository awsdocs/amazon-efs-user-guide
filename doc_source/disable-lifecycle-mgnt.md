# Stopping EFS Lifecycle Management<a name="disable-lifecycle-mgnt"></a>

You can stop EFS lifecycle management at any time using the EFS Management Console, the CLI, or the EFS API\. When you stop lifecycle management, your files are no longer moved into the IA storage class\. Any files currently in IA storage remain there\. You can move files from IA to Standard storage by copying them to another location on your file system\. 

## Stopping Lifecycle Management for an Existing File System \(Console\)<a name="disable-lifecycle-console"></a>

You can stop EFS lifecycle management by using the console as follows\.

**To stop lifecycle management for an existing file system \(console\)**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File Systems**\.

1. Choose the file system for which to stop lifecycle management\.

1. Locate **Lifecycle policy** in **Other details**\. Choose **edit** to change the setting\.

1. In the **Enable lifecycle management** panel, choose **None** for **Lifecycle policy**\.

1. Choose **Save** to save the setting\.

 In **Other details**, **Lifecycle policy** is now set to **None**\. 

## Stopping Lifecycle Management for an Existing File System \(AWS CLI\)<a name="disable-lifecycle-cli"></a>

You can stop EFS lifecycle management by using the AWS CLI as follows\.

**To stop lifecycle management for an existing file system \(CLI\)**
+  Run the `put-lifecycle-configuration` command specifying the file system ID of the file system for which you are stopping lifecycle management\. 

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