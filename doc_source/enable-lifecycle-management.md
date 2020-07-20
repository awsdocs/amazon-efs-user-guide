# Using lifecycle management<a name="enable-lifecycle-management"></a>

Lifecycle management is automatically enabled with a 30 days since last access policy when you create an Amazon EFS file system using the service recommended settings\. For more information, see [Step 1: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md)\.

If you create a new file system with customized settings, you can enable Lifecycle management in Step 1 of the configuration wizard\. For more information, see [Step 1: Configure file system settings](creating-using-create-fs.md#config-file-system-settings)\.

You can start, stop, and modify lifecycle management using the AWS Management Console and the AWS CLI, described as follows\.

## Start, stop, or modify lifecycle management on an existing file system<a name="console2-enable-lifemgnt-filesystem"></a>

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

## Start, stop, or modify lifecycle management on an existing file system \(CLI\)<a name="lifecycle-mgnt-cli"></a>

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