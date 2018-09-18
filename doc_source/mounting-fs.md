# Mounting File Systems<a name="mounting-fs"></a>

In the following section, you can learn how to mount your Amazon EFS file system on a Linux instance using the Amazon EFS mount helper\. In addition, you can find how to use the file `fstab` to automatically remount your file system after any system restarts\.

Before the Amazon EFS mount helper was available, we recommended mounting your Amazon EFS file systems using the standard Linux NFS client\. For more information on those changes, see [Mounting File Systems Without the EFS Mount Helper](mounting-fs-old.md)\.

**Note**  
Before you can mount a file system, you must create, configure, and launch your related AWS resources\. For detailed instructions, see [Getting Started with Amazon Elastic File System](getting-started.md)\.

**Topics**
+ [Troubleshooting AMI and Kernel Versions](#ami-kernel-versions-troubleshooting)
+ [Installing the amazon\-efs\-utils Package](#mounting-fs-install-amazon-efs-utils)
+ [Mounting with the EFS Mount Helper](#mounting-fs-mount-helper)
+ [Mounting Your Amazon EFS File System Automatically](mount-fs-auto-mount-onreboot.md)
+ [Additional Mounting Considerations](mounting-fs-mount-cmd-general.md)

## Troubleshooting AMI and Kernel Versions<a name="ami-kernel-versions-troubleshooting"></a>

To troubleshoot issues related to certain Amazon Machine Image \(AMI\) or kernel versions when using Amazon EFS from an Amazon EC2 instance, see [Troubleshooting AMI and Kernel Issues](troubleshooting.md#troubleshooting-efs-ami-kernel)\.

## Installing the amazon\-efs\-utils Package<a name="mounting-fs-install-amazon-efs-utils"></a>

To mount your Amazon EFS file system on your Amazon EC2 instance, we recommend that you use the mount helper in the amazon\-efs\-utils package\. The amazon\-efs\-utils package is an open\-source collection of Amazon EFS tools\. For more information, see [Installing the amazon\-efs\-utils Package on Amazon Linux](using-amazon-efs-utils.md#installing-amazon-efs-utils)\.

## Mounting with the EFS Mount Helper<a name="mounting-fs-mount-helper"></a>

You can mount an Amazon EFS file system on a number of clients using the Amazon EFS mount helper\. The following sections, you can find the mount helper process for the different types of clients\.

**Topics**
+ [Mounting on Amazon EC2 with the EFS Mount Helper](#mounting-fs-mount-helper-ec2)
+ [Mounting on Your On\-Premises Linux Client with the EFS Mount Helper over AWS Direct Connect](#mounting-fs-mount-helper-direct)

### Mounting on Amazon EC2 with the EFS Mount Helper<a name="mounting-fs-mount-helper-ec2"></a>

You can mount an Amazon EFS file system on an Amazon EC2 instance using the Amazon EFS mount helper\. For more information on the mount helper, see [EFS Mount Helper](using-amazon-efs-utils.md#efs-mount-helper)\. To use the mount helper, you need the following:
+ **An Amazon EFS file system ID** – After you create an Amazon EFS file system, you can get that file system's ID from the console or programmatically through the Amazon EFS API\. This ID is in this format: `fs-12345678`\.
+ **An Amazon EFS mount target** – You create mount targets in your VPC\. If you create your file system in the console, you create your mount targets at the same time\. For more information, see [Creating a Mount Target Using the Amazon EFS console](accessing-fs.md#create-mount-target-console)\.
+ **An Amazon EC2 instance running a supported distribution of Linux** – The supported Linux distributions for mounting your file system with the mount helper are Amazon Linux 2, Amazon Linux 2017\.09 and newer, Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer, and Ubuntu 16\.04 LTS and newer\.
+ **The Amazon EFS mount helper installed** – The mount helper is a tool in amazon\-efs\-utils\. For information on how to install amazon\-efs\-utils, see [Installing the amazon\-efs\-utils Package on Amazon Linux](using-amazon-efs-utils.md#installing-amazon-efs-utils)\.

**To mount your Amazon EFS file system with the mount helper**

1. Access the terminal for your instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Run the following command to mount your file system\.

   ```
   sudo mount -t efs fs-12345678:/ /mnt/efs
   ```

   Alternatively, if you want to use encryption of data in transit, you can mount your file system with the following command\.

   ```
   sudo mount -t efs -o tls fs-12345678:/ /mnt/efs
   ```

You also have the option of mounting automatically by adding an entry to your `/etc/fstab` file\. When you mount automatically using `/etc/fstab`, you must add the `_netdev` mount option\. For more information, see [Updating an Existing EC2 Instance to Mount Automatically](mount-fs-auto-mount-onreboot.md#mount-fs-auto-mount-update-fstab)\.

**Note**  
Mounting with the mount helper automatically uses the following mount options that are optimized for Amazon EFS:  
`nfsvers=4.1`
`rsize=1048576`
`wsize=1048576`
`hard`
`timeo=600`
`retrans=2`

To use the `mount` command, the following must be true:
+ The connecting EC2 instance must be in a VPC and must be configured to use the DNS server provided by Amazon\. For information about the Amazon DNS server, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\. 
+ The VPC of the connecting EC2 instance must have DNS hostnames enabled\. For more information, see [Viewing DNS Hostnames for Your EC2 Instance](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-viewing) in the *Amazon VPC User Guide*\. 

**Note**  
We recommend that you wait 90 seconds after creating a mount target before you mount your file system\. This wait lets the DNS records propagate fully in the AWS Region where the file system is\.

### Mounting on Your On\-Premises Linux Client with the EFS Mount Helper over AWS Direct Connect<a name="mounting-fs-mount-helper-direct"></a>

You can mount your Amazon EFS file systems on your on\-premises data center servers when connected to your Amazon VPC with AWS Direct Connect\. Mounting your Amazon EFS file systems with amazon\-efs\-utils also makes mounting simpler with the mount helper and allows you to enable encryption of data in transit\. 

To see how to use amazon\-efs\-utils with AWS Direct Connect to mount Amazon EFS file systems onto on\-premises Linux clients, see [Walkthrough 5: Create and Mount a File System On\-Premises with AWS Direct Connect](efs-onpremises.md)\.