# Walkthrough 5: Create and Mount a File System On\-Premises with AWS Direct Connect<a name="efs-onpremises"></a>

This walkthrough uses the console to create and mount a file system on an on\-premises server using a AWS Direct Connect connection\.

In this walkthrough, it's assumed that you already have an AWS Direct Connect connection\. If you don't have one, you can begin the process now and come back to this walkthrough when your connection is established\. For more information, see [AWS Direct Connect Product Details](https://aws.amazon.com/directconnect/details/)\.

When you have an AWS Direct Connect connection, you'll create the following AWS resources in your account:

+ Amazon EFS resources:

  + A file system\.

  + A mount target for your file system\.

    To mount your file system on your on\-premises servers, you need to create a mount target in your VPC\. You can create one mount target in each of the Availability Zones in your VPC\. For more information, see [Amazon EFS: How It Works](how-it-works.md)\. 

Then, you'll test the file system from your on\-premises server\. The clean\-up step at the end of the walkthrough provides information for you to remove these resources\.

The walkthrough creates all these resources in the US West \(Oregon\) Region \(`us-west-2`\)\. Whichever AWS Region you use, be sure to use it consistently\. All of your resources—your VPC, your mount target, and your Amazon EFS file system—must be in the same AWS Region\.

**Note**  
If your local application needs to know if the EFS file system is available, then your application should be able to point to a different mount point IP address if the first mount point becomes temporarily unavailable\. In this scenario, we recommend that you have two on\-premises servers connected to your file system through different Availability Zones \(AZs\) for higher availability\. 

## Before You Begin<a name="wt5-prepare"></a>

You can use the root credentials of your AWS account to sign in to the console and try this exercise\. However, AWS Identity and Access Management \(IAM\) Best practices recommend that you do not use the root credentials of your AWS account\. Instead, create an administrator user in your account and use those credentials to manage resources in your account\. For more information, see [Setting Up](setting-up.md)\.

You can use a default VPC or a custom VPC that you have created in your account\. For this walkthrough, the default VPC configuration works\. However, if you use a custom VPC, verify the following:

+ The Internet gateway is attached to your VPC\. For more information, see [Internet Gateways](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.

+ The VPC route table includes a rule to send all Internet\-bound traffic to the Internet gateway\.

## Step 1: Create Your Amazon Elastic File System Resources<a name="wt5-step1-efs"></a>

In this step, you create your Amazon EFS file system and mount targets\.

**To create your Amazon EFS file system**

1. Open the Amazon EFS console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create File System**\.

1. Choose your default VPC from the **VPC** list\.

1. Select the check boxes for all of the Availability Zones\. Make sure that they all have the default subnets, automatic IP addresses, and the default security groups chosen\. These are your mount targets\. For more information, see [Creating Mount Targets](accessing-fs.md)\.

1. Choose **Next Step**\.

1. Name your file system, keep **general purpose** selected as your default performance mode, and choose **Next Step**\.

1. Choose **Create File System**\.

1. Choose your file system from the list and make a note of the **Security group** value\. You'll need this value for the next step\.

The file system you just created has mount targets, created in step 1\.4\. Each mount target has an associated security group\. The security group acts as a virtual firewall that controls network traffic\. If you didn't provide a security group when creating a mount target, Amazon EFS associates the default security group of the VPC with it\. If you followed the above steps exactly, then your mount targets are using the default security group\.

Next, you'll add a rule to the mount target's security group to allow inbound traffic to the NFS port \(2049\)\. You can use the AWS Management Console to add the rule to your mount target's security groups in your VPC\.

**To allow inbound traffic to the NFS port**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Under **NETWORK & SECURITY**, choose **Security Groups**\.

1. Choose the security group associated with your file system\. You made a note of this at the end of [Step 1: Create Your Amazon Elastic File System Resources](#wt5-step1-efs)\.

1. In the tabbed pane that appears below the list of security groups, choose the **Inbound** tab\.

1. Choose **Edit**\.

1. Choose **Add Rule**, and choose a rule of the following type:

   + **Type** – NFS

   + **Source** – Anywhere

   We recommend that you only use the **Anywhere** source for testing\. You can choose to create a custom source set to the IP address of the on\-premises server, or use the console from the server itself, and choose **My IP**\.
**Note**  
You don't need to add an outbound rule because the default outbound rule allows all traffic to leave \(otherwise, you will need to add an outbound rule to open TCP connection on the NFS port, identifying the mount target security group as the destination\)\.

## Step 2: Mount the Amazon EFS File System on Your On\-Premises Server<a name="wt5-step2-connect"></a>

Open a terminal on your on\-premises Linux server\. To mount the Amazon EFS file system, you need the mount target **IP Address** for your Amazon EFS file system\. If you created multiple mount targets for your file system, then you can choose any one of these\. Once you have that and your terminal open, you can run the following command to mount your Amazon EFS file system\.

```
$ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 MOUNT_TARGET_IP_ADDRESS:/ efs
```

Change directories to the new directory that you created with the following command\.

```
$ cd efs
```

Make a subdirectory and change the ownership of that subdirectory to your EC2 instance user\. Then, navigate to that new directory with the following commands\.

```
$ sudo mkdir getting-started
$ sudo chown ec2-user getting-started
$ cd getting-started
```

Create a text file with the following command\.

```
$ touch test-file.txt
```

List the directory contents with the following command\.

```
$ ls -al
```

As a result, the following file is created\.

```
-rw-rw-r-- 1 username username 0 Nov 15 15:32 test-file.txt
```

## Step 3: Clean Up Resources and Protect Your AWS Account<a name="wt5-step3-cleanup"></a>

After you have finished this walkthrough, or if you don't want to explore the walkthroughs, you should follow these steps to clean up your resources and protect your AWS account\.

**To clean up resources and protect your AWS account**

1. Unmount the Amazon EFS file system with the following command\.

   ```
   $ sudo umount efs
   ```

1. Open the Amazon EFS console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose the Amazon EFS file system that you want to delete from the list of file systems\.

1. For **Actions**, choose **Delete file system**\.

1. In the **Permanently delete file system** dialog box, type the file system ID for the Amazon EFS file system that you want to delete, and then choose **Delete File System**\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the name of the security group that you added the rule to for this walkthrough\.
**Warning**  
Don't delete the default security group for your VPC\.

1. For **Actions**, choose **Edit inbound rules**\.

1. Choose the X at the end of the inbound rule you added, and choose **Save**\.