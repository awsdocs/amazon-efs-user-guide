# Walkthrough 8: Sync a File System from Amazon EC2 to Amazon EFS Using EFS File Sync<a name="walkthrough-file-sync-ec2"></a>

This walkthrough shows the steps how to sync files from a file system that is in AWS to Amazon EFS using EFS File Sync\. 

**Topics**
+ [Before You Begin](#walkthrough-file-sync-ec2-prepare)
+ [Step 1: Create a Sync Agent](#create-sync-agent-ec2)
+ [Step 2: Create a Sync Task](#create-sync-task-ec2)
+ [Step 3: Sync Your Source File System to Amazon EFS](#copy-files-ec2)
+ [Step 4: Access Your Files](#sync-access-files-ec2)
+ [Step 4: Clean Up](#cleanup-file-sync-resources-ec2)

## Before You Begin<a name="walkthrough-file-sync-ec2-prepare"></a>

In this walkthrough, we assume the following:
+ You have a Network File System \(NFS\) file server on an Amazon EC2 instance\.
+ You have created an Amazon EFS file system\. If you don't have an Amazon EFS file system, create one now and come back to this walkthrough when you are done\. For more information about how to create an Amazon EFS file system, see [Getting Started with Amazon Elastic File System](getting-started.md)\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-walkthrough8.png)

## Step 1: Create a Sync Agent<a name="create-sync-agent-ec2"></a>

To create a sync agent in Amazon EC2, you will use the AMI provided to create an Amazon EC2 instance that can mount the source file system in your AWS environment\. This Amazon EC2 instance will run in the same AWS Region as your source file system\. Once deployed, you will activate the agent to securely associate it with your AWS account\.<a name="syncagent-ec2"></a>

**To create a sync agent for data in AWS**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/) and choose the AWS Region where you created your source file system\.

1. Choose **File syncs**\. If you haven't used EFS File Sync in this AWS Region, you see an introductory page\. Choose **Get started** to open the **Select a host platform** page\.

   If you have previously used EFS File Sync in this AWS Region, choose **Agents** from the left navigation, and then choose **Create sync agent** to open the **Select a host platform** page\. 

1. From **Select a host platform** page, choose **Amazon EC2**, choose the AWS Region where your source file system is located and then choose **Launch instance**\. You will be redirected to the **Choose an Instance Type** page in the Amazon EC2 Management Console in that AWS Region, where you can choose an instance type\.
**Note**  
A sync agent syncs files to EFS file systems in the AWS region where the sync agent is activated\. Standard Amazon EC2 rates apply to the instance\.

1. On the **Choose an Instance Type** page, choose the hardware configuration of your instance\. When deploying your sync agent on Amazon EC2,we recommend choosing one of the **Memory optimized r4\.xlarge** instance types for your sync agent\. The instance size you choose must be at least **xlarge**\.

1. Choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, choose a value for **Auto\-assign Public IP**\. If you want your instance to be accessible from the public internet, set **Auto\-assign Public IP** to **Enable**\. Otherwise, set **Auto\-assign Public IP** to **Disable**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-configure-instance-details.png)

1. Choose Next: **Add Storage** and choose **Next: Add tags**\. The EFS File Sync agent uses the root volume and doesn't require additional storage\.

1. On the **Add Tags** page, you can optionally add tags to your instance\. Then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, add firewall rules for specific traffic to reach your instance\. You can create a new security group or choose an existing security group\. 
**Important**  
At a minimum, your security group must allow inbound access to HTTP port 80 from your web browser to activate your sync agent\.

1. Choose **Review and Launch** to review your configuration, then choose **Launch** to launch your instance\. We recommend selecting an existing key pair or creating a new key pair for your instance\. This key pair is not needed for normal operation of EFS File Sync, but it may be needed if you contact AWS to get support\.

   A confirmation page appears to say that your instance is launching\.

1. Choose **View Instances** to close the confirmation page and return to the console\. On the **Instances** screen, you can view the status of your instance\. It takes a short time for an instance to launch\. When you launch an instance, its initial state is **pending**\. After the instance starts, its state changes to **running**, and it's assigned a public DNS name and IP address\.

1. Choose your instance and take note of the public IP address in the **Description** tab\. You use this IP address to connect to your sync agent\. 
**Note**  
The IP address doesn’t need to be accessible from outside your network\.
**Important**  
If your source file system and destination Amazon EFS file system are in different AWS Regions, you open the Amazon EFS Console in the AWS Region where your destination Amazon EFS file system is located to connect\.  
Your source and destination file system must be in different virtual private clouds \(VPCs\)\.

1. Choose **File syncs**, choose **Create sync agent**, and then choose **Next: Connect to agent** on the **Select host platform** page\.

1. For **IP address**, type the Amazon EC2 instance IP address, and then choose **Next: Activate agent**\. Your browser will connect to this IP address to get a unique activation key from your sync agent\. This key securely associates your sync agent with your AWS account\. This IP address doesn't need to be accessible from outside your network, but must be accessible from your browser\.

1. On the **Activate agent** page, type a name for your sync agent and choose **Activate agent**\. 

At this point, you should see your activated sync agent on the Amazon EFS console\.

## Step 2: Create a Sync Task<a name="create-sync-task-ec2"></a>

Create a sync task and configure the source and destination file systems\.

**To create a sync task**

1. Choose **Create sync task**\. The **Configure source location** page appears\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-config-source-location.png)

1. Provide the following information for the source file system:
   + For **NFS server**, type the domain name or IP address of the source NFS server\. 
   + For **Mount Path**, type the mount path for your source file system\. 
   + For **Agent**, choose the sync agent that you created earlier\.

1. Choose **Next: Configure destination**\. The **Configure destination location** page appears\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-config-destination-location.png)

1. Provide the following information for the destination file system:
   + For **Amazon EFS file system**, choose the EFS file system you want to sync to\. If you don't have an Amazon EFS file system, create one now and restart this walkthrough when you are done\. For more information about how to create an Amazon EFS file system, see [Getting Started with Amazon Elastic File System](getting-started.md)\. 
   + For **File system path**, type the path of the file system that you want to write data to\. This path must exist in the destination file system\.
   + For **Security group**, choose a security group that allows access to the destination Amazon EFS file system you selected\.

1. Choose **Next: Configure settings**\. The **Configure sync settings** page appears\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-configure-sync-settings.png)

1. Configure the default settings that you want this sync task to use for synchronizing your files:
**Note**  
You can override these settings later when you start a sync task\.
   + Choose **Ownership \(User/Group ID\)** to copy the user and group IDs from the source files\.
   + Choose **Permissions** to copy the source files permissions\.
   + Choose **Timestamps** to copy time stamps from the source files\.
   + Choose **File deletion** to keep all files in the destination that are not found in the source file system\. If this box is cleared, all files in the destination that are not found in the source file system will be deleted\. 
   + Choose **Verification mode** to check that the destination file system is an exact copy of the source file system after the sync task completes\. If you do not choose this option, only the data that is transferred is verified\. Changes made to files while they are being actively transferred and changes made to files that are not actively being transferred, will not be discovered\. We recommend choosing full verification\.

1. Choose **Next: Review and Create**, and then review your sync task settings\. When you are ready, choose **Create sync task**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-new-sync-task.png)

Your sync task is created\. The status of the task will show as **Available** when the source and destination file systems have been mounted\.

The **Details** tab shows the status and settings for your source and destination file systems\.

## Step 3: Sync Your Source File System to Amazon EFS<a name="copy-files-ec2"></a>

Now that you have a sync task, you can start your sync task to begin syncing files from the source file system to the destination EFS File Sync file system\.

**To sync the source file system**

1. On the Tasks page, choose the sync task you just created\. The Details tab shows the status of your sync task\.

1. In the **Actions** menu, choose **Start**\. 

1. In the **Start sync task** dialog box, you can modify the settings for your sync task and choose** Start**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-start-task.png)

1. Choose **Start** to start syncing files\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-start-copy.png)

1. When the sync task starts, the **Status** column shows the progress of the sync task\. As the sync task begins preparation, the status changes from **Starting** to **Preparing**\. When the task starts to sync files, the status changes from **Preparing** to **Syncing**\. When file consistency verification starts, the status changes to **Verifying**\. When the sync task is done, the status changes to **Success**\.

## Step 4: Access Your Files<a name="sync-access-files-ec2"></a>

To access your files, connect to the Amazon EFS file system from an Amazon EC2 instance or use AWS Direct Connect\. 

For information about how to connect using Amazon EC2, see [Connect to Your Amazon EC2 Instance and Mount the Amazon EFS File System](http://docs.aws.amazon.com/efs/latest/ug/gs-step-three-connect-to-ec2-instance.html)\.

For information about how to connect using AWS Direct Connect, see [Walkthrough 5: Create and Mount a File System On\-Premises with AWS Direct Connect](efs-onpremises.md)\.

## Step 4: Clean Up<a name="cleanup-file-sync-resources-ec2"></a>

If you no longer need the resources you created, you should remove them to protect your account:

If you no longer need the resources you created, you should remove them:
+ Delete the task you created\. For more information, see [Deleting a Sync Task](managing-file-sync.md#delete-sync-task)\.
+ Delete the sync agent you created\. This will not delete the Amazon EC2 instance you launched\. \.
+ Clean up the Amazon EFS resources you created\. For more information, see [Step 5: Clean Up Resources and Protect Your AWS Account](gs-step-four-cleanup.md)\.
+ Clean up your instance if you created your EFS File Sync on Amazon EC2\. For more information, see [Step 3: Clean Up Your Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-clean-up-your-instance) in the *Amazon EC2 User Guide for Linux Instances\.*