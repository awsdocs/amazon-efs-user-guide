# Walkthrough: Mount a File System from a Different VPC<a name="efs-different-vpc"></a>

In this walkthrough, you set up an Amazon EC2 instance to mount an Amazon EFS file system that is in a different virtual private cloud \(VPC\)\. You do so using either an AWS transit gateway or an Amazon VPC peering connection\.

**Note**  
Using Amazon EFS with Microsoft Windows–based clients isn't supported\.

**Topics**
+ [Before You Begin](#wt6-prepare)
+ [Step 1: Setting Up Your EC2 Instance](#wt6-step1-efs)
+ [Step 2: Mount the EFS File System](#wt6-step2-mount-file-system)
+ [Step 4: Clean Up Resources and Protect Your AWS Account](#wt6-step4-cleanup)
+ [Optional: Encrypting Data in Transit](#wt6-step3-get-efs-utils)

## Before You Begin<a name="wt6-prepare"></a>

In this walkthrough, we assume that you already have the following:
+ One of the following:
  + A VPC peering connection between the VPC where the EFS file system resides and the VPC where the EC2 instance resides\. For more information, see [Creating and Accepting a VPC Peering Connection](https://docs.aws.amazon.com/vpc/latest/peering/create-vpc-peering-connection.html) in the *Amazon VPC Peering Guide*\.
  + A transit gateway connecting the VPC where the EFS file system resides and the VPC where the EC2 instance resides\. For more information, see [Getting Started with Transit Gateways](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html) in the *Amazon VPC Transit Gateways Guide*\.
+ An EC2 instance\.
+ An EFS file system in a different VPC than this EC2 instance\.

If you don't have these resources, you can set these up now and come back to this walkthrough when you are done\.

You can use the root credentials of your AWS account to sign in to the console and try this exercise\. However, AWS Identity and Access Management \(IAM\) best practices recommend that you don't use the root credentials of your AWS account\. Instead, create an administrator user in your account and use those credentials to manage resources in your account\. For more information, see [Setting Up](setting-up.md)\.

## Step 1: Setting Up Your EC2 Instance<a name="wt6-step1-efs"></a>

In this step, you set up your Amazon EC2 instance so that it can mount an EFS file system that is in a different VPC\.

**To allow inbound traffic to the NFS port**
**Note**  
If you require data to be encrypted in transit, use the Amazon EFS mount helper, `amazon-efs-utils`, instead of the NFS client\. For information on installing amazon\-efs\-utils, see the section [](#wt6-step3-get-efs-utils)\.

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Under **NETWORK & SECURITY**, choose **Security Groups**\.

1. Choose the security group associated with your file system\.

1. In the tabbed pane that appears below the list of security groups, choose the **Inbound** tab\.

1. Choose **Edit**\.

1. Choose **Add Rule**, and choose a rule of the following type:
   + **Type** – **NFS**
   + **Source** – **Anywhere**

   We recommend that you only use the **Anywhere** source for testing\.
**Note**  
You don't need to add an outbound rule, because the default outbound rule allows all traffic to leave\. If you don't have this default outbound rule, add an outbound rule to open a TCP connection on the Network File System \(NFS\) port, identifying the mount target security group as the destination\.

**To install the NFS client on your EC2 instance**

1. Open a Secure Shell \(SSH\) client and connect to your EC2 instance\. For more information on how to do so, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Install the NFS client on your EC2 instance\. For more information, see [Installing the NFS Client](mounting-fs-old.md#mounting-fs-install-nfsclient)\.

## Step 2: Mount the EFS File System<a name="wt6-step2-mount-file-system"></a>

**To create a mount directory**

1. Open an SSH client and connect to your EC2 instance\. For more information on how to do so, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Create a new directory on your EC2 instance, such as "efs"\. 

   ```
   sudo mkdir efs
   ```

**To mount the file system**
+ Mount your file system using one of the mount target IP addresses that your EC2 instance can access over the VPC peering connection\. If your EC2 instance and your EFS file system are in the same AWS Region, use the mount target IP address that is in your Availability Zone\. For more information, see [Creating or Deleting Mount Targets in a VPC](manage-fs-access-create-delete-mount-targets.md)\. You can find the mount target IP address from the console, API, or CLI of the EFS file system owner\.

  ```
  sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport mount-target-IP:/ efs
  ```

Now that you've mounted your Amazon EFS file system, you can test it with the following procedure\.

**To test the Amazon EFS file system connection**

1. Change directories to the new directory that you created with the following command\.

   ```
   $ cd ~/efs
   ```

1. Make a subdirectory and change the ownership of that subdirectory to your EC2 instance user\. Then navigate to that new directory with the following commands\.

   ```
   $ sudo mkdir getting-started
   $ sudo chown ec2-user getting-started
   $ cd getting-started
   ```

1. Create a text file with the following command\.

   ```
   $ touch test-file.txt
   ```

1. List the directory contents with the following command\.

   ```
   $ ls -al
   ```

As a result, the following file is created\.

```
-rw-rw-r-- 1 username username 0 Nov 15 15:32 test-file.txt
```

You can also mount your file system automatically by adding an entry to the `/etc/fstab` file\. For more information, see [Mounting Your Amazon EFS File System Automatically](mount-fs-auto-mount-onreboot.md)\.

**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting-efs-mounting.md#automount-fails)\.

## Step 4: Clean Up Resources and Protect Your AWS Account<a name="wt6-step4-cleanup"></a>

After you have finished this walkthrough, or if you don't want to explore the walkthroughs, you should follow these steps to clean up your resources and protect your AWS account\.

**To clean up resources and protect your AWS account**

1. Unmount the Amazon EFS file system with the following command\.

   ```
   $ sudo umount ~/efs
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

## Optional: Encrypting Data in Transit<a name="wt6-step3-get-efs-utils"></a>

To encrypt data in transit, use the Amazon EFS mount helper, amazon\-efs\-utils, instead of the NFS client\.

The *amazon\-efs\-utils *package is an open\-source collection of Amazon EFS tools\. The amazon\-efs\-utils collection comes with a mount helper and tooling that makes it easier to encrypt data in transit for Amazon EFS\. For more information on this package, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\. This package is available as a free download from GitHub, which you can get by cloning the package's repository\.

**To clone amazon\-efs\-utils from GitHub**

1. Access the terminal for your on\-premises client\.

1. From the terminal, clone the amazon\-efs\-utils tool from GitHub to a directory of your choice, with the following command\.

   ```
   git clone https://github.com/aws/efs-utils
   ```

Now that you have the package, you can install it\. This installation is handled differently depending on the Linux distribution of your on\-premises client\. The following distributions are supported:
+ Amazon Linux 2
+ Amazon Linux
+ Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer
+ Ubuntu 16\.04 LTS and newer

**To build and install amazon\-efs\-utils as an RPM package**

1. Open a terminal on your client and navigate to the directory that has the cloned amazon\-efs\-utils package from GitHub\.

1. Build the package with the following command\.

   ```
   make rpm
   ```
**Note**  
If you haven't already, install the rpm\-builder package with the following command\.  

   ```
   sudo yum -y install rpm-build
   ```

1. Install the package with the following command\.

   ```
   sudo yum -y install build/amazon-efs-utils*rpm
   ```

**To build and install amazon\-efs\-utils as a deb package**

1. Open a terminal on your client and navigate to the directory that has the cloned amazon\-efs\-utils package from GitHub\.

1. Build the package with the following command\.

   ```
   ./build-deb.sh
   ```

1. Install the package with the following command\.

   ```
   sudo apt-get install build/amazon-efs-utils*deb
   ```

After the package is installed, configure amazon\-efs\-utils for use in your AWS Region with AWS Direct Connect or VPN\.

**To configure amazon\-efs\-utils for use in your AWS Region**

1. Using your text editor of choice, open `/etc/amazon/efs/amazon-efs-utils.conf` for editing\.

1. Find the line `“dns_name_format = {fs_id}.efs.{region}.amazonaws.com”`\.

1. Change `{region}` with the ID for your AWS Region, for example `us-west-2`\.

To mount the EFS file system on your on\-premises client, first open a terminal on your on\-premises Linux client\. To mount the system, you need the file system ID, the mount target IP address for one of your mount targets, and the file system's AWS Region\. If you created multiple mount targets for your file system, then you can choose any one of these\. 

When you have that information, you can mount your file system in three steps:

**To create a mount directory**

1.  Make a directory for the mount point with the following command\.  
**Example**  

   ```
   mkdir ~/efs
   ```

1. Choose your preferred IP address of the mount target in the Availability Zone\. You can measure the latency from your on\-premises Linux clients\. To do so, use a terminal\-based tool like `ping` against the IP address of your EC2 instances in different Availability Zones to find the one with the lowest latency\.

**To update `/etc/hosts`**
+ Add an entry to your local `/etc/hosts` file with the file system ID and the mount target IP address, in the following format\.

  ```
  mount-target-IP-Address file-system-ID.efs.region.amazonaws.com
  ```  
**Example**  

  ```
  192.0.2.0 fs-12345678.efs.us-west-2.amazonaws.com
  ```

**To make a mount directory**

1.  Make a directory for the mount point with the following command\.  
**Example**  

   ```
   mkdir ~/efs
   ```

1. Run the mount command to mount the file system\.  
**Example**  

   ```
   sudo mount -t efs fs-12345678 ~/efs
   ```

   If you want to use encryption of data in transit, your mount command looks something like the following\.  
**Example**  

   ```
   sudo mount -t efs -o tls fs-12345678 ~/efs
   ```