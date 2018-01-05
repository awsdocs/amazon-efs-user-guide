# Managing Amazon EFS File Sync<a name="managing-file-sync"></a>

In this section, you can find information about how to manage your Amazon EFS File Sync\.


+ [Deleting a Sync Agent](#delete-sync-agent)
+ [Deleting a Sync Task](#delete-sync-task)
+ [Understanding Sync Agent Status](#sync-agent-status)
+ [Understanding Sync Task Status](#sync-task-status)
+ [Performing Tasks on the EFS File Sync VM Local Console](#sync-onpremises-local-console-tasks)
+ [Performing Tasks on Amazon EC2 EFS File Sync Local Console](#sync-ec2-local-console-tasks)

## Deleting a Sync Agent<a name="delete-sync-agent"></a>

If you no longer need a sync agent, you can delete it from the Amazon EFS Management Console\.

**To delete a sync agent**

1. Choose **File syncs**, choose **Agents**, and then choose the sync agent that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. In the **Confirm deletion of sync agent** dialog box, choose **Check box confirm deletion**, and then choose **OK**\. 

## Deleting a Sync Task<a name="delete-sync-task"></a>

If you no longer need a sync task, you can delete it from the Amazon EFS Management Console\.

**To delete a sync task**

1. Choose **File syncs**, choose **Tasks**, and then choose the sync task that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. In the **Confirm deletion of sync task** dialog box, choose **Check box confirm deletion**, and then choose **OK**\. 

## Understanding Sync Agent Status<a name="sync-agent-status"></a>

The following table describes each sync agent status, and if and when you should take action based on the status\. When a sync agent is in use, it has **Running** status all or most of the time\.


****  

| Sync Agent Status | Meaning | 
| --- | --- | 
| Running | The sync agent is configured properly and is available to use\. The Running status is the normal running status for a sync agent\. | 
| Offline | The sync agent's VM or EC instance is turned off or the agent is in an unhealthy state\. When the issue that caused the unhealthy state is resolved, the agent returns to Running status\. | 

## Understanding Sync Task Status<a name="sync-task-status"></a>

The following table described each sync task status, and if and when you should take action based on the status\.


****  

| Sync Task Status | Meaning | 
| --- | --- | 
| Available | The sync task is configured properly and is available to be started\. | 
| Completed | The task creating process has completed\. | 
| Creating | EFS File Sync is creating the sync task\. | 
| Error |  | 
| Starting | The task creating process has started\. | 
| Preparing | The sync task is examining the source and destination file systems to determine which files to sync\. | 
| Syncing | EFS File Sync is syncing file from the source file system to the destination Amazon EFS file system\. | 
| Verifying | EFS File Sync is verifying consistency between the source and destination file systems\. | 

## Performing Tasks on the EFS File Sync VM Local Console<a name="sync-onpremises-local-console-tasks"></a>

For a EFS File Sync deployed on\-premises, you can perform the following maintenance tasks using the VM host's local console\.


+ [Logging in to the Local Console Using Default Credentials](#sync-local-console-login)
+ [Configuring Your EFS File Sync Network](#sync-configure-network)
+ [Testing Your EFS File Sync Connection to the Internet](#sync-test-internet-connectivity)
+ [Viewing Your EFS File Sync System Resource Status](#sync-system-resource-check)
+ [Synchronizing Your EFS File Sync VM Time](#sync-synchronize-time)
+ [Running EFS File Sync Commands on the Local Console](#sync-command-prompt)

### Logging in to the Local Console Using Default Credentials<a name="sync-local-console-login"></a>

When the VM is ready for you to log in, the login screen is displayed\.

**To log in to the EFS File Sync's local console**

+ If this is your first time logging in to the local console, log in to the VM with the user name *sguser* and password *sgpassword*\. Otherwise, use your credentials to log in\.

After you log in, you see the** Amazon EFS File Sync** **Configuration** main menu, as shown in the following screenshot\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-on-premise-local-console1.png)

**Note**  
We recommend changing the default password\. You do this by running the `passwd` command from the EFS File Sync Command Prompt \(item 5 on the main menu\)\. For information about how to run the command, see [Running EFS File Sync Commands on the Local Console](#sync-command-prompt)\. 


| To | See | 
| --- | --- | 
| Configure your network |  [Configuring Your EFS File Sync Network](#sync-configure-network)\. | 
| Test network connectivity |  [Testing Your EFS File Sync Connection to the Internet](#sync-test-internet-connectivity)\. | 
| View system resource check |  [Viewing Your EFS File Sync System Resource Status](#sync-system-resource-check)\. | 
| Manage VM time |  [Synchronizing Your EFS File Sync VM Time](#sync-synchronize-time)\. | 
| Run Local console commands |  [Running EFS File Sync Commands on the Local Console](#sync-command-prompt)\. | 

To shut down EFS File Sync, type **0**\. 

To exit the configuration session, type **x** to exit the menu\. 

### Configuring Your EFS File Sync Network<a name="sync-configure-network"></a>

The default network configuration for the EFS File Sync is Dynamic Host Configuration Protocol \(DHCP\)\. With DHCP, your EFS File Sync is automatically assigned an IP address\. In some cases, you might need to manually assign your EFS File Sync's IP as a static IP address, as described following\.

**To configure your EFS File Sync to use static IP addresses**

1. Log in to your EFS File Sync's local console\.

1. On the Amazon EFS File Sync **Configuration** main menu, type option **1** to begin configuring a static IP address\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-on-premise-local-console1.png)

1. Choose one of the following options on the **Amazon EFS File Sync Configuration** menu:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-network-configuration.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/managing-file-sync.html)

### Testing Your EFS File Sync Connection to the Internet<a name="sync-test-internet-connectivity"></a>

You can use your EFS File Sync's local console to test your Internet connection\. This test can be useful when you are troubleshooting network issues with your EFS File Sync\.

**To test your EFS File Sync's connection to the Internet**

1. Log in to your EFS File Sync's local console\.

1. On the **EFS File Sync Configuration** main menu, type option **2** to begin testing network connectivity\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-on-premise-local-console1.png)

1. The endpoint in the selected region displays either a PASSED or FAILED message, as shown following\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/managing-file-sync.html)

### Viewing Your EFS File Sync System Resource Status<a name="sync-system-resource-check"></a>

When your gateway starts, it checks its virtual CPU cores, root volume size, and RAM and determines whether these system resources are sufficient for your EFS File Sync to function properly\. You can view the results of this check on the EFS File Sync's local console\.

**To view the status of a system resource check**

1. Log in to your EFS File Sync's local console\.

1. In the **EFS File Sync Configuration** main menu, type **3** to view the results of a system resource check\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-on-premise-local-console1.png)

   The console displays an **\[OK**\], **\[WARNING\]**, or **\[FAIL\]** message for each resource as described in the table following\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/managing-file-sync.html)

   The console also displays the number of errors and warnings next to the resource check menu option\.

   The following screenshot shows a **\[FAIL\]** message and the accompanying error message\.

### Synchronizing Your EFS File Sync VM Time<a name="sync-synchronize-time"></a>

After your EFS File Sync is deployed and running, in some scenarios the EFS File Sync VM's time can drift\. For example, if there is a prolonged network outage and your hypervisor host and EFS File Sync do not get time updates, then the EFS File Sync VM's time will be different from the true time\. When there is a time drift, a discrepancy occurs between the stated times when operations such as snapshots occur and the actual times that the operations occur\.

For a EFS File Sync deployed on VMware ESXi, setting the hypervisor host time and synchronizing the VM time to the host is sufficient to avoid time drift\.

### Running EFS File Sync Commands on the Local Console<a name="sync-command-prompt"></a>

The EFS File Sync console helps provide a secure environment for configuring and diagnosing issues with your EFS File Sync\. Using the console commands, you can perform maintenance tasks such as saving routing tables or connecting to AWS Support\.

**To run a configuration or diagnostic command**

1. Log in to your EFS File Sync's local console\.

1. On the **EFS File Sync Configuration** main menu, type option **5** for **Command Prompt**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-on-premise-local-console1.png)

1. On the EFS File Sync console, type **h**, and then press the **Return** key\.

   The console displays the **Available Commands** menu with the available commands and after the menu a **Command Prompt**, as shown in the following screenshot\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-on-premise-local-console2.png)

1. To learn about a command, type **man** \+ ***command name*** at the **EFS File Sync Console** prompt\.

## Performing Tasks on Amazon EC2 EFS File Sync Local Console<a name="sync-ec2-local-console-tasks"></a>

Some maintenance tasks require that you log in to the local console when running a EFS File Sync deployed on an Amazon EC2 instance\. In this section, you can find information about how to log in to the local console and perform maintenance tasks\.


+ [Logging In to Amazon EC2 EFS File Sync Local Console](#sync-ec2-local-console-login)
+ [Testing EFS File Sync Connectivity to the Internet](#sync-test-ec2-connectivity)
+ [Viewing EFS File Sync System Resource Status](#sync-ec2-resource-check)
+ [Running EFS File Sync Commands on the Local Console](#sync-ec2-command-prompt)

### Logging In to Amazon EC2 EFS File Sync Local Console<a name="sync-ec2-local-console-login"></a>

You can connect to your Amazon EC2 instance by using a Secure Shell \(SSH\) client\. For detailed information, see [Connect to Your Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide//AccessingInstances.html) in the *Amazon EC2 User Guide*\. To connect this way, you will need the SSH key pair you specified when you launched the instance\. For information about Amazon EC2 key pairs, see [Amazon EC2 Key Pairs](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide//ec2-key-pairs.html) in the *Amazon EC2 User Guide\.*

**To log in to the EFS File Sync local console**

1. Log in to your local console\. If you are connecting to your EC2 instance from a Windows computer, log in as *sguser*\.

1. After you log in, you see the **Amazon EFS File Sync Configuration** main menu, as shown in the following screenshot\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-ec2-local-console1.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/managing-file-sync.html)

To shut down the EFS File Sync, type **0**\. 

To exit the configuration session, type **x** to exit the menu\. 

### Testing EFS File Sync Connectivity to the Internet<a name="sync-test-ec2-connectivity"></a>

You can use your EFS File Sync's local console to test your Internet connection\. This test can be useful when you are troubleshooting network issues\. 

**To test EFS File Sync's connection to the Internet**

1. Log in to your EFS File Sync's local console\. For instructions, see [Logging In to Amazon EC2 EFS File Sync Local Console](#sync-ec2-local-console-login)\.

1. In the **Amazon EFS File Sync Configuration** main menu, type **1** to begin testing network connectivity\.

1. The endpoint in the region you select displays either a **\[PASSED\]** or **\[FAILED\]** message, as shown following\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/managing-file-sync.html)

### Viewing EFS File Sync System Resource Status<a name="sync-ec2-resource-check"></a>

When your EFS File Sync starts, it checks its virtual CPU cores, root volume size, and RAM and determines whether these system resources are sufficient for your EFS File Sync to function properly\. You can view the results of this check on the EFS File Sync's local console\.

**To view the status of a system resource check**

1. Log in to your EFS File Sync's local console\. For instructions, see [Logging In to Amazon EC2 EFS File Sync Local Console](#sync-ec2-local-console-login)\.

1. In the **Amazon EFS File Sync Configuration** main menu, type **2** to view the results of a system resource check\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-ec2-local-console1.png)

   The console displays an **\[OK**\], **\[WARNING\]**, or **\[FAIL\]** message for each resource as described in the table following\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/managing-file-sync.html)

   The console also displays the number of errors and warnings next to the resource check menu option\.

   The following screenshot shows a **\[FAIL\]** message and the accompanying error message\.

### Running EFS File Sync Commands on the Local Console<a name="sync-ec2-command-prompt"></a>

The EFS File Sync local console helps provide a secure environment for configuring and diagnosing issues with your EFS File Sync\. Using the local console commands, you can perform maintenance tasks such as saving routing tables or connecting to AWS Support\. 

**To run a configuration or diagnostic command**

1. Log in to your EFS File Sync's local console\. For instructions, see [Logging In to Amazon EC2 EFS File Sync Local Console](#sync-ec2-local-console-login)\.

1. In the **Amazon EFS File Sync Configuration** main menu, type **3** for **EFS File Sync Console**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-ec2-local-console1.png)

1. In the EFS File Sync console, type **h**, and then press the **Return** key\.

   The console displays the **Available Commands** menu with the available commands\. After the menu, a **EFS File Sync Console** prompt appears, as shown in the following screenshot\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-ec2-local-console2.png)

1. To learn about a command, type **man** \+ ***command name*** at the **EFS File Sync Console** prompt\.