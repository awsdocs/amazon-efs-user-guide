# Troubleshooting EFS File Sync<a name="troubleshooting-file-sync"></a>

Following, you can find information on how to troubleshoot issues with EFS File Sync\.

**Topics**
+ [Your On\-Premises Source File System Is Stuck in Mounting Status](#sync-onpremise-location-stuck-mounting)
+ [Your Amazon EC2 Source File System Is Stuck in Mounting Status](#sync-ec2-location-stuck-mounting)
+ [Your Sync Task Is Stuck in Starting Status](#sync-task-stuck-starting)
+ [Your Sync Task Failed with Permission Denied Error Message](#sync-task-permission-denied)
+ [How Long Does the Preparing Status of a Sync Task Take to Complete?](#Preparing-status-too-long)
+ [How Long Does the Verifying Status of a Sync Task Take to Complete?](#verifying-status-too-long)
+ [Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running On\-Premises](#enable-support-access-sync)
+ [Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running on Amazon EC2](#Sync-EC2-EnableAWSSupportAccess)

## Your On\-Premises Source File System Is Stuck in Mounting Status<a name="sync-onpremise-location-stuck-mounting"></a>

Your on\-premises source file system can become stuck in **Mounting** status when the sync agent that you chose can't mount the location that you specified during configuration\.

**Action to Take**  
First, make sure that the NFS server and export that you specified are both valid\. If they aren't, delete the sync set, create a new one using the correct NFS server, and then export\.

If the NFS server and export are both valid, it generally indicates one of two things\. Either a firewall is preventing the sync agent from mounting the NFS server, or the NFS server is not configured to allow the sync agent to mount it\.

Make sure that there is no firewall between the sync agent and the NFS server\. Then make sure that the NFS server is configured to allow the sync agent to mount the export specified in the sync set\.

If you perform these actions and the sync agent still can't mount the NFS server and export, open a support channel and engage AWS customer support\. For information about how to open a support channel, see [Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running On\-Premises](#enable-support-access-sync) or [Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running on Amazon EC2](#Sync-EC2-EnableAWSSupportAccess)\.

## Your Amazon EC2 Source File System Is Stuck in Mounting Status<a name="sync-ec2-location-stuck-mounting"></a>

Your Amazon EC2 source file system can become stuck in **Mounting** status when the sync agent that you chose can't mount the location that you specified during configuration\.

**Action to Take**  
First, make sure that the NFS server and export that you specified are both valid\. If they aren't, delete the sync set, create a new one using the correct NFS server, and then export\.

If the NFS server and export are both valid, it generally indicates one of two things\. Either a firewall is preventing the sync agent from mounting the NFS server, or the NFS server is not configured to allow the sync agent to mount it\.

Make sure that the VPC in which your NFS server resides has a security group inbound rule that allows all traffic to the sync agent that you created for your source file system\. Then make sure that the VPC in which your sync agent is running in has a security group outbound rule that allows all traffic from the sync agent\. 

If you perform these actions and the sync agent still can't mount the NFS server and export, open a support channel and engage AWS customer support\. For information about how to open a support channel, see [Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running On\-Premises](#enable-support-access-sync) or [Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running on Amazon EC2](#Sync-EC2-EnableAWSSupportAccess)\.

## Your Sync Task Is Stuck in Starting Status<a name="sync-task-stuck-starting"></a>

Your sync task can become stuck in **Starting** status when EFS File Sync can't instruct the specified source sync agent to begin a sync task\. This issue usually occurs because the sync agent either is powered off or has lost network connectivity\.

**Action to Take**  
Make sure that the source sync agent is connected and the status is **Running**\. If the status is **Offline**, then the agent is not connected\.

Next, make sure that your sync agent is powered on\. If it isn't, power it on\.

If the sync agent is powered on and the sync task is still stuck in **Starting** status, then a network connectivity problem between the sync agent and EFS File Sync is the most likely issue\. Check your network and firewall settings to make sure that the sync agent can connect to EFS File Sync\.

If you perform these actions and the issue isn't resolved, open a support channel and engage AWS customer support\. For information about how to open a support channel, see [Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running On\-Premises](#enable-support-access-sync) or [Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running on Amazon EC2](#Sync-EC2-EnableAWSSupportAccess)\.

## Your Sync Task Failed with Permission Denied Error Message<a name="sync-task-permission-denied"></a>

You can get a permissions denied error message if you configure your NFS server with root\_squash or all\_squash enabled, and your files don't have all read access\.

**Action to Take**  
To fix this issue, either configure the NFS export with no\_root\_squash, or ensure that the permissions for all of the files you want to sync allow read access for all users\. Doing either enables the sync agent to read the files\. For the sync agent to access directories, you must additionally enable all execute access\. For information about NFS export configuration, see [18\.7\. The /etc/exports Configuration File](https://www.centos.org/docs/5/html/Deployment_Guide-en-US/s1-nfs-server-config-exports.html) in the Centos documentation\.

If you perform these actions and the issue isn't resolved, contact AWS customer support\. 

## How Long Does the Preparing Status of a Sync Task Take to Complete?<a name="Preparing-status-too-long"></a>

The time EFS File Sync spends in the **Preparing** status depends on the number of files in both the source and destination file systems, and the performance of these file systems\. When a sync task starts, EFS File Sync performs a recursive directory listing to discover all files and file metadata in the source and destination file system\. These listings are used to identify differences and determine what to copy\.

**Action to take**  
You don't need to take any action\. Wait for the **Preparing** status to complete and status changes to **Syncing**\. If the status doesn’t change to **Syncing** status, contact AWS customer support\.

## How Long Does the Verifying Status of a Sync Task Take to Complete?<a name="verifying-status-too-long"></a>

The time EFS File Sync spends in the **Verifying** status depends on the number of files, the total size of all files in both the source and destination file systems, and the performance of these file systems\. By default, **Verification mode** is enabled in the sync setting\. The verification EFS File Sync performs includes a SHA256 checksum on all file content and an exact comparison of all file metadata\.

**Action to take**  
You don't need to take any action\. Wait for the **Verifying** status to complete\. If the **Verifying** status doesn’t complete, contact AWS customer support\.

## Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running On\-Premises<a name="enable-support-access-sync"></a>

EFS File Sync provides a local console you can use to perform several maintenance tasks, including enabling AWS Support to access your EFS File Sync to assist you with troubleshooting EFS File Sync issues\. By default, AWS Support access to your EFS File Sync is disabled\. You enable this access through the host's local console\. To give AWS Support access to your EFS File Sync, you first log in to the local console for the host then connect to the support server\.

**To enable AWS Support access to EFS File Sync**

1. Log in to your host's local console\. use user name: admin and password: password

   The local console looks like the following\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-on-premise-local-console1.png)

1. At the prompt, type **4** to open the help menu\. 

1. Type **h** to open the **AVAILABLE COMMANDS** window\.

1. In the **AVAILABLE COMMANDS** window, type **open\-support\-channel** to connect to customer support\. You must allow TCP port 22 to initiate a support channel to AWS\. When you connect to customer support, EFS File Sync assigns you a support number\. Make a note of your support number\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-on-premise-local-console2.png)
**Note**  
The channel number is not a Transmission Control Protocol/User Datagram Protocol \(TCP/UDP\) port number\. Instead, it makes a Secure Shell \(SSH\) \(TCP 22\) connection to servers and provides the support channel for the connection\.

1. Once the support channel is established, provide your support service number to AWS Support so AWS Support can provide troubleshooting assistance\. 

1. When the support session is completed, type **q** to end it\.

1. Type **exit** to log out of the EFS File Sync local console\.

1. Follow the prompts to exit the local console\.

## Enabling AWS Support to Help Troubleshoot Your EFS File Sync Running on Amazon EC2<a name="Sync-EC2-EnableAWSSupportAccess"></a>

EFS File Sync provides a local console you can use to perform several maintenance tasks, including enabling AWS Support to access your EFS File Sync to assist you with troubleshooting EFS File Sync issues\. By default, AWS Support access to your EFS File Sync is disabled\. You enable this access through the Amazon EC2 local console\. You log in to the Amazon EC2 local console through a Secure Shell \(SSH\)\. To successfully log in through SSH, your instance's security group must have a rule that opens TCP port 22\.

**Note**  
If you add a new rule to an existing security group, the new rule applies to all instances that use that security group\. For more information about security groups and how to add a security group rule, see [Amazon EC2 Security Groups](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide*\.

To let AWS Support connect to your EFS File Sync, you first log in to the local console for the Amazon EC2 instance, navigate to the EFS File Sync's console, and then provide the access\.

**To enable AWS support access to an EFS File Sync deployed on an Amazon EC2 instance**

1. Log in to the local console for your Amazon EC2 instance\. For instructions, go to [Connect to Your Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide*\.

   You can use the following command to log in to the EC2 instance's local console\. the user name is **admin**\.

   ```
   ssh –i PRIVATE-KEY admin@INSTANCE-PUBLIC-DNS-NAME
   ```
**Note**  
The *PRIVATE\-KEY* is the `.pen` file containing the private certificate of the EC2 key pair that you used to launch the Amazon EC2 instance\. For more information, see [Retrieving the Public Key for Your Key Pair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#retriving-the-public-key) in the *Amazon EC2 User Guide*\.  
The *INSTANCE\-PUBLIC\-DNS\-NAME* is the public Domain Name System \(DNS\) name of your Amazon EC2 instance that your EFS File Sync is running on\. You obtain this public DNS name by selecting the Amazon EC2 instance in the EC2 console and clicking the **Description** tab\.

    The local console looks like the following\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-ec2-local-console1.png)

1. At the prompt, type **2** to open the help menu\. 

1. Type **h** to open the **AVAILABLE COMMANDS** window\.

1. In the **AVAILABLE COMMANDS** window, type **open\-support\-channel** to connect to customer support for EFS File Sync\. You must allow TCP port 22 to initiate a support channel to AWS\. When you connect to customer support, EFS File Sync assigns you a support number\. Make a note of your support number\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-ec2-local-console2.png)
**Note**  
The channel number is not a Transmission Control Protocol/User Datagram Protocol \(TCP/UDP\) port number\. Instead, the EFS File Sync makes a Secure Shell \(SSH\) \(TCP 22\) connection to EFS File Sync servers and provides the support channel for the connection\.

1. Once the support channel is established, provide your support service number to AWS Support so AWS Support can provide troubleshooting assistance\. 

1. When the support session is completed, type **q** to end it\.

1. Type **exit** to exit the EFS File Sync console\.

1. Follow the console menus to log out of the EFS File Sync instance\. 