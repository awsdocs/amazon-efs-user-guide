# Walkthrough 7: Sync Files from an On\-Premises File System to Amazon EFS by Using EFS File Sync<a name="walkthrough-file-sync-onpremise"></a>

This walkthrough shows the steps how to sync files from an on\-premises file system to Amazon EFS using EFS File Sync\. 

**Topics**
+ [Before You Begin](#file-sync-onpremise-prepare)
+ [Step 1: Create a Sync Agent](#create-sync-agent-op)
+ [Step 2: Create a Sync Task](#create-sync-task)
+ [Step 3: Sync Your Source File System to Amazon EFS](#copy-files)
+ [Step 4: Access Your Files](#file-sync-access-files)
+ [Step 5: Clean Up](#cleanup-file-sync-resources-op)

## Before You Begin<a name="file-sync-onpremise-prepare"></a>

In this walkthrough, we assume the following:
+ You have a Network File System \(NFS\) file server in your on\-premises data center\.
+ You have a VMware ESXi Hypervisor host in your on\-premises data center\.
+ You have created an Amazon EFS file system\. If you don't have an Amazon EFS file system, create one now and come back to this walkthrough when you are done\. For more information about how to create an Amazon EFS file system, see [Getting Started with Amazon Elastic File System](getting-started.md)\. 

You can securely and efficiently copy your files over the Internet or use AWS Direct Connect\. For information about how to use AWS Direct Connect , see [Walkthrough 5: Create and Mount a File System On\-Premises with AWS Direct Connect](efs-onpremises.md)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-walkthrough7.png)

## Step 1: Create a Sync Agent<a name="create-sync-agent-op"></a>

To create a sync agent, you download a virtual machine \(VM\) image and deploy it into your on\-premises environment so that it can mount your source file system\. Once deployed, you activate the agent to securely associate it with your AWS account\.<a name="syncagent-onpremise"></a>

**To create a sync agent for on\-premises data**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File syncs**\. If you haven't yet used EFS File Sync in this AWS Region, you see an introductory page\. Choose **Get started** to open the **Select a host platform** page\.

   If you have previously used EFS File Sync in this AWS Region, choose **Agents** from the left navigation, and then choose **Create sync agent** to open the **Select a host platform** page\. 

1. From the **Select host platform** page, choose **VMware ESXi**, and then choose **Download image**\. The virtual machine \(VM\) image will begin downloading\.

1. When the download completes, deploy the VM to your VMware ESXi hypervisor and, use the VMware client to configure the VM\. We recommend a VM with 4 vCPUs, 32 GB of memory, 10 Gigabit networking, and an 80 GB root volume\. 

1. Start the VM, and then take note of the VM IP address\. This VM must be able to mount your source file system using NFS\.
**Note**  
Although it's not required, we recommend that you use paravirtualized network controllers for your VMware ESXi VM\.   
You don't need to add additional disks to the VM\. EFS File Sync uses only the root disk\.

1. On the Amazon EFS Console, choose **Next: Connect to agent** 

1. For **IP address**, type the VM's IP address, and then choose **Next: Activate agent**\. Your browser will connect to this IP address to get a unique activation key from your sync agent\. This key securely associates your sync agent with your AWS account\. This IP address doesn't need to be accessible from outside your network, but must be accessible from your browser\.

1. On the** Activate agent** page, type a name for your sync agent, and then choose **Activate agent**\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-file-sync-console.png)

At this point, you should see your activated sync agent on the Amazon EFS console\.

## Step 2: Create a Sync Task<a name="create-sync-task"></a>

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

Your sync task is created\. The status of the task show as **Available** when the source and destination file systems have been mounted\.

The **Details** tab shows the status and settings for your source and destination file systems\.

## Step 3: Sync Your Source File System to Amazon EFS<a name="copy-files"></a>

Now that you have a sync task, you can start your sync task to begin syncing files from the source file system to the destination Amazon EFS file system\.

**To sync the source file system**

1. On the Tasks page, choose the sync task you just created\. The Details tab shows the status of your sync task\.

1. In the **Actions** menu, choose **Start**\. 

1. In the **Start sync task** dialog box, you can modify the settings for your sync task and choose** Start**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-start-task.png)

1. Choose **Start** to start syncing files\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-start-copy.png)

1. When the sync task starts, the **Status** column shows the progress of the sync task\. As the sync task begins preparation, the status changes from **Starting** to **Preparing**\. When the task starts to sync files, the status changes from **Preparing** to **Syncing**\. When file consistency verification starts, the status changes to **Verifying**\. When the sync task is done, the status changes to **Success**\.

## Step 4: Access Your Files<a name="file-sync-access-files"></a>

To access your files, you connect to the Amazon EFS file system from an Amazon EC2 instance or use AWS Direct Connect\. 

For information about how to connect using Amazon EC2, see [Connect to Your Amazon EC2 Instance and Mount the Amazon EFS File System](http://docs.aws.amazon.com/efs/latest/ug/gs-step-three-connect-to-ec2-instance.html)\.

For information about how to connect using AWS Direct Connect, see [Walkthrough 5: Create and Mount a File System On\-Premises with AWS Direct Connect](efs-onpremises.md)\.

## Step 5: Clean Up<a name="cleanup-file-sync-resources-op"></a>

If you no longer need the resources you created, you should remove them:
+ Delete the task you created\. For more information, see [Deleting a Sync Task](managing-file-sync.md#delete-sync-task)\.
+ Delete the sync agent you created\. This will not delete the VM you deployed to your on\-premises hypervisor\.
+ Clean up the Amazon EFS resources you created\. For more information, see [Step 5: Clean Up Resources and Protect Your AWS Account](gs-step-four-cleanup.md)\.