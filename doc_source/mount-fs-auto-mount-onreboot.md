# Mounting your Amazon EFS file system automatically<a name="mount-fs-auto-mount-onreboot"></a>

You can configure an Amazon EC2 instance to automatically mount an EFS file system when it reboots in two ways:
+ When you create a new EC2 instance using the Launch Instance Wizard\.
+ Update the EC2 `/etc/fstab` file with an entry for the EFS file system\.

Both of these methods use the EFS mount helper to mount the file system\. The mount helper is part of the `amazon-efs-utils` set of tools\. 

The `amazon-efs-utils` tools are available for installation on Amazon Linux and Amazon Linux 2 Amazon Machine Images \(AMIs\)\. For more information about `amazon-efs-utils`, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\. If you are using another Linux distribution, such as Red Hat Enterprise Linux \(RHEL\), manually build and install `amazon-efs-utils`\. For more information, see [Installing the amazon\-efs\-utils Package on Other Linux Distributions](installing-other-distro.md)\.

## Configuring EC2 instances to mount an EFS file system at instance launch<a name="mount-fs-auto-mount-on-creation"></a>

When you create a new Amazon EC2 Linux instance using the EC2 Launch Instance Wizard, you can configure it to mount your Amazon EFS file system automatically\. The EC2 instance mounts the file system automatically the instance first launched and also whenever it restarts\.

Before you perform this procedure, make sure that you have created your Amazon EFS file system\. For more information, see [Step 1: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md) in the Amazon EFS Getting Started exercise\.

**Note**  
You can't use Amazon EFS with Microsoft Windows–based Amazon EC2 instances\.

Before you can launch and connect to an Amazon EC2 instance, you need to create a key pair, unless you already have one\. Follow the steps in [Setting Up with Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances* to create a key pair\. If you already have a key pair, you can use it for this exercise\.

**To configure your EC2 instance to mount an EFS file system automatically at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. In **Step 1: Choose an Amazon Machine Image \(AMI\)**, find an Amazon Linux AMI at the top of the list and choose **Select**\.

1. In **Step 2: Choose an Instance Type**, choose **Next: Configure Instance Details**\.

1. In **Step 3: Configure Instance Details**, provide the following information: 
   + For **Network**, choose the entry for the same VPC that the EFS file system you're mounting is in\.
   + For **Subnet**, choose a default subnet in any Availability Zone\.
   + For **File systems**, choose the EFS file system that you want to mount\. The path shown next the file system ID is the mount point that the EC2 instance will use, which you can change\.
   + Under **Advanced Details**, the **User data** is automatically generated, and includes the commands needed to mount the EFS file systems you specified under **File systems**\.

1. Choose **Next: Add Storage**\.

1. Choose **Next: Add Tags**\.

1. Name your instance and choose **Next: Configure Security Group**\.

1. In **Step 6: Configure Security Group**, set **Assign a security group** to **Select an existing security group**\. Choose the default security group to make sure that it can access your EFS file system\.

   You can't access your EC2 instance by Secure Shell \(SSH\) using this security group\. For access by SSH, later you can edit the default security and add a rule to allow SSH or a new security group that allows SSH\. You can use the following settings:
   + **Type:** SSH
   + **Protocol:** TCP
   + **Port Range:** 22
   + **Source:** Anywhere 0\.0\.0\.0/0

1. Choose **Review and Launch**\.

1. Choose **Launch**\.

1. Select the check box for the key pair that you created, and then choose **Launch Instances**\.

Your EC2 instance is now configured to mount the EFS file system at launch and whenever it's rebooted\.

## Using /etc/fstab to mount automatically<a name="mount-fs-auto-mount-update-fstab"></a>

To automatically remount your Amazon EFS file system directory when the Amazon EC2 instance reboots, use the file `/etc/fstab`\. The `/etc/fstab` file contains information about file systems\. The command `mount -a`, which runs during instance startup, mounts the file systems listed in `/etc/fstab`\. This procedure uses the EFS mount helper to mount the file system and needs to be installed on the EC2 instance\. 

The mount helper is part of the `amazon-efs-utils` set of tools, which is available for installation on Amazon Linux and Amazon Linux 2 Amazon Machine Images \(AMIs\)\. For more information about installing `amazon-efs-utils` on an Amazon Linux or Amazon Linux 2 AMI, see [Installing the amazon\-efs\-utils Package on Amazon Linux](installing-amazon-efs-utils.md)\. If you are using another Linux distribution, such as Red Hat Enterprise Linux \(RHEL\), manually build and install `amazon-efs-utils`\. For more information, see [Installing the amazon\-efs\-utils Package on Other Linux Distributions](installing-other-distro.md)\.

**Note**  
Before you can update the `/etc/fstab` file of your EC2 instance, make sure that you already created your Amazon EFS file system\. For more information, see [Step 1: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md) in the Amazon EFS Getting Started exercise\.

**To update the /etc/fstab file on your EC2 instance**

1. Connect to your EC2 instance:
   + To connect to your instance from a computer running macOS or Linux, specify the \.pem file for your SSH command\. To do this, use the `-i` option and the path to your private key\.
   + To connect to your instance from a computer running Windows, you can use either MindTerm or PuTTY\. To use PuTTY, install it and convert the \.pem file to a \.ppk file\.

   For more information, see the following topics in the *Amazon EC2 User Guide for Linux Instances*:
   +  [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)
   +  [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) 

1. Open the `/etc/fstab` file in an editor\.

1. Automatically mount your EFS file system using either IAM authorization or an EFS access point:
   + To automatically mount with IAM authorization to an Amazon EC2 instance that has an instance profile, add the following line to the `/etc/fstab` file\.

     ```
     file-system-id:/ efs-mount-point efs _netdev,tls,iam 0 0
     ```
   + To automatically mount with IAM authorization to a Linux instance using a credentials file, add the following line to the `/etc/fstab` file\.

     ```
     file-system-id:/ efs-mount-point efs _netdev,tls,iam,awsprofile=namedprofile 0 0
     ```
   + To automatically mount a file system using an EFS access point, add the following line to the `/etc/fstab` file\.

     ```
     file-system-id efs-mount-point efs _netdev,tls,accesspoint=access-point-id 0 0
     ```
**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting-efs-mounting.md#automount-fails)\.

   For more information, see [Mounting with IAM authorization](mounting-fs.md#mounting-IAM-option) and [Mounting with EFS access points](mounting-fs.md#mounting-access-points)\.

1. Save the changes to the file\.

1. Test the `fstab` entry by using the `mount` command with the `'fake'` option along with the `'all'` and `'verbose'` options\.

   ```
   $ sudo mount -fav
   home/ec2-user/efs      : successfully mounted
   ```

Your EC2 instance is now configured to mount the EFS file system whenever it restarts\.

**Note**  
In some cases, your Amazon EC2 instance might need to start regardless of the status of your mounted Amazon EFS file system\. In such cases, add the `nofail` option to your file system's entry in your `/etc/fstab` file\.

The line of code you added to the `/etc/fstab` file does the following\.


| Field | Description | 
| --- | --- | 
|  `file-system-id:/`  |  The ID for your Amazon EFS file system\. You can get this ID from the console or programmatically from the CLI or an AWS SDK\.  | 
|  `efs-mount-point`  |  The mount point for the EFS file system on your EC2 instance\.  | 
|  `efs`  |  The type of file system\. When you're using the mount helper, this type is always `efs`\.  | 
|  `mount options`  |  Mount options for the file system\. This is a comma\-separated list of the following options: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/mount-fs-auto-mount-onreboot.html)  | 
|  `0`  |  A nonzero value indicates that the file system should be backed up by `dump`\. For EFS, this value should be `0`\.  | 
|  `0`  |  The order in which `fsck` checks file systems at boot\. For EFS file systems, this value should be `0` to indicate that `fsck` should not run at startup\.  | 