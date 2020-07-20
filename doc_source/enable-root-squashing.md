# Walkthrough: Enable Root Squashing Using IAM Authorization for NFS Clients<a name="enable-root-squashing"></a>

In this walkthrough, you configure Amazon EFS to prevent root access to your Amazon EFS file system for all AWS principals except for a single management workstation\. You do this by configuring AWS Identity and Access Management \(IAM\) authorization for Network File System \(NFS\) clients\. For more information about IAM authorization for NFS clients in EFS, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\. 

To do this requires configuring two IAM permissions policies, as follows:
+ Create an EFS file system policy that explicitly allows read and write access to the file system, and implicitly denies root access\.
+ Assign an IAM identity to the Amazon EC2 management workstation that requires root access to the file system by using an Amazon EC2 instance profile\. For more information about Amazon EC2 instance profiles, see [Using Instance Profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html) in the *AWS Identity and Access Management User Guide*\.
+ Assign the `AmazonElasticFileSystemClientFullAccess` AWS managed policy to the IAM role of the management workstation\. For more information about AWS managed policies for EFS, see [AWS Managed \(Predefined\) Policies for Amazon EFS](access-control-managing-permissions.md#access-policy-examples-aws-managed)\.

To enable root squashing using IAM authorization for NFS clients, use the following procedures\.

**To prevent root access to the file system**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Filesystems**\.

1. On the **File systems** page, choose the file system that you want to enable root squashing on\.

1. On the file system details page, choose **File system policy**, and then choose **Edit**\. The **File system policy** page appears\.  
![\[File system policy page for editing and saving file system policy.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-disable-root-access.png)

1. Choose **Prevent root access by default\*** under **Policy options**\. The policy JSON object appears in the **Policy editor**\.

1. Choose **Save** to save the file system policy\.

Clients that aren't anonymous can get root access to the file system through an identity\-based policy\. When you attach the `AmazonElasticFileSystemClientFullAccess` managed policy to the workstation's role, IAM grants root access to the workstation based on its identity policy\.

**To enable root access from the management workstation**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Create a role for Amazon EC2 called `EFS-client-root-access`\. IAM creates an instance profile with the same name as the EC2 role you created\.

1. Assign the AWS managed policy `AmazonElasticFileSystemClientFullAccess` to the EC2 role you created\. The contents of this policy is shown following\.

   ```
   {
       “Version”: “2012-10-17”,
       “Statement”: [
           {
           “Resource”: “*”,
           “Effect”: “Allow”,
           “Action”: “elasticfilesystem:Client*”
           }
       ],
       "Condition": {}
   }
   ```

1. Attach the instance profile to the EC2 instance that you are using as the management workstation, as described following\. For more information, see [Attaching an IAM Role to an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#attach-iam-role) in the *Amazon EC2 User Guide for Linux Instances*\.

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Instances**\.

   1. Choose the instance\. For **Actions**, choose **Instance Settings**, and then choose **Attach/Replace IAM role**\.

   1. Choose the IAM role that you created in the first step, `EFS-client-root-access`, and choose **Apply**\.

1. Install the EFS mount helper on the management workstation\. For more information about the EFS mount helper and the amazon\-efs\-utils package, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\.

1. Mount the EFS file system on the management workstation by using the following command with the `iam` mount option\.

   ```
   $ sudo mount -t efs -o tls,iam file-system-id:/ efs-mount-point
   ```

   You can configure the Amazon EC2 instance to automatically mount the file system with IAM authorization\. For more information about mounting an EFS file system with IAM authorization, see [Mounting with IAM authorization](mounting-fs.md#mounting-IAM-option)\.