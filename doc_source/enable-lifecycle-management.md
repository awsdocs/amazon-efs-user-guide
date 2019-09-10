# Enabling Lifecycle Management<a name="enable-lifecycle-management"></a>

You can enable lifecycle management when creating a new Amazon EFS file system by using the Amazon EFS console\. You can enable lifecycle management for an existing file system by using the EFS console, CLI, or API\. Following, find procedures for enabling and stopping EFS lifecycle management\.

## Enable Lifecycle Management When Creating a New File System \(Console\)<a name="enable-lifecycle-console-new-fs"></a>

You can use the AWS Management Console to enable lifecycle management when creating a new Amazon EFS file system as follows\.

**To enable lifecycle management for a new EFS file system**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create file system**\.

1. Complete **Step 1: Configure file system access** by choosing a VPC and creating mount targets\. For more information, see [Step 2: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md)\. 

1. Choose **Next Step**\.

1. On the **Configure optional settings** page, add any tags that you want\.

1. Choose a value for **Lifecycle policy** in the **Enable lifecycle management** section to enable lifecycle management\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/enable-lifecycle-mgt.png)

1. Configure any additional optional settings, and choose **Next Step**\.

1. On the **Review and create** page, review the **Lifecycle policy** setting that you chose in the **Optional settings** section\.

1.  To make any changes, choose **Previous**\. Otherwise, choose **Create File System** to create your file system with lifecycle management enabled\. 

## Enable Lifecycle Management for an Existing File System \(Console\)<a name="enable-lifecycle-console-ex-fs"></a>

You can use the AWS Management Console to enable lifecycle management for an existing file system\.

**To enable lifecycle management on an existing file system \(console\)**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File Systems**\.

1. Choose the file system for which you want to enable lifecycle management\.

1. Locate **Lifecycle policy** in **Other details**\. Choose **edit** to change the setting\. 

1.  In the **Enable lifecycle management** panel, choose the policy for **Lifecycle policy** that you want to apply to your file system\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/enable-lm-existing.png)

1. Choose **Save** to save the setting\.

 After the setting is saved, **Lifecycle policy** is enabled in the **Other details** section and shows the policy that you chose\. 

## Enable Lifecycle Management for an Existing File System \(AWS CLI\)<a name="enable-lifecycle-cli"></a>

You can use the AWS CLI to enable lifecycle management on an existing file system, as follows\. You can't use the CLI or API to enable lifecycle management when creating a new file system\. 

**To enable lifecycle management on an existing file system \(CLI\)**
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