# Mounting EFS file systems<a name="mounting-fs"></a>

In the following section, you can learn how to mount your Amazon EFS file system on a Linux instance using the Amazon EFS mount helper\. In addition, you can find how to use the file `fstab` to automatically remount your file system after any system restarts\. To learn more about the Amazon EFS mount helper, see [EFS Mount Helper](efs-mount-helper.md)\.

Before the Amazon EFS mount helper was available, we recommended mounting your Amazon EFS file systems using the standard Linux NFS client\. For more information on those changes, see [Mounting File Systems Without the EFS Mount Helper](mounting-fs-old.md)\.

**Note**  
You can configure a new Amazon EC2 Linux instance to mount an Amazon EFS file system when it launches\. However, before you mount a file system, you must create, configure, and launch your related AWS resources\. For detailed instructions, see [Getting Started with Amazon Elastic File System](getting-started.md)\.

**Topics**
+ [Troubleshooting AMI and kernel versions](#ami-kernel-versions-troubleshooting)
+ [Installing the amazon\-efs\-utils package](#mounting-fs-install-amazon-efs-utils)
+ [Mounting with the EFS mount helper](#mounting-fs-mount-helper)
+ [Mounting your Amazon EFS file system automatically](mount-fs-auto-mount-onreboot.md)
+ [Mounting EFS file systems from another account or VPC](manage-fs-access-vpc-peering.md)
+ [Additional mounting considerations](mounting-fs-mount-cmd-general.md)

## Troubleshooting AMI and kernel versions<a name="ami-kernel-versions-troubleshooting"></a>

To troubleshoot issues related to certain Amazon Machine Image \(AMI\) or kernel versions when using Amazon EFS from an Amazon EC2 instance, see [Troubleshooting AMI and Kernel Issues](troubleshooting-efs-ami-kernel.md)\.

## Installing the amazon\-efs\-utils package<a name="mounting-fs-install-amazon-efs-utils"></a>

To mount your Amazon EFS file system on your Amazon EC2 instance, we recommend that you use the mount helper in the amazon\-efs\-utils package\. The amazon\-efs\-utils package is an open\-source collection of Amazon EFS tools\. For more information, see [Installing the amazon\-efs\-utils Package on Amazon Linux](installing-amazon-efs-utils.md)\.

## Mounting with the EFS mount helper<a name="mounting-fs-mount-helper"></a>

You can mount an Amazon EFS file system on a number of clients using the Amazon EFS mount helper\. The following sections, you can find the mount helper process for the different types of clients\.

**Topics**
+ [Mounting on Amazon EC2 with the EFS mount helper](#mounting-fs-mount-helper-ec2)
+ [Mounting with IAM authorization](#mounting-IAM-option)
+ [Mounting with EFS access points](#mounting-access-points)
+ [Mounting automatically with EFS mount helper](#mount-helper-automount)
+ [Mounting on your on\-premises Linux client with the EFS mount helper over AWS Direct Connect and VPN](#mounting-fs-mount-helper-direct)

### Mounting on Amazon EC2 with the EFS mount helper<a name="mounting-fs-mount-helper-ec2"></a>

You can mount an Amazon EFS file system on an Amazon EC2 instance using the Amazon EFS mount helper\. For more information on the mount helper, see [EFS Mount Helper](efs-mount-helper.md)\. To use the mount helper, you need the following:
+ **The file system ID of the EFS file system that you want to mount ** – After you create an Amazon EFS file system, you can get that file system's ID from the console or programmatically through the Amazon EFS API\. The ID is in this format: `fs-12345678`\.
+ **An Amazon EFS mount target** – You create mount targets in your virtual private cloud \(VPC\)\. If you create your file system in the console, you create your mount targets at the same time\. For instructions to create mount targets using the EFS console, see [Step 2: Configure network access](creating-using-create-fs.md#configure-efs-network-access)\.
+ **An Amazon EC2 instance running a supported distribution of Linux** – The supported Linux distributions for mounting your file system with the mount helper are the following:
  + Amazon Linux 2 
  + Amazon Linux 2017\.09 and newer 
  + Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer 
  + Ubuntu 16\.04 LTS and newer
+ **The Amazon EFS mount helper installed** – The mount helper is a tool in amazon\-efs\-utils\. For information on how to install amazon\-efs\-utils, see [Installing the amazon\-efs\-utils Package on Amazon Linux](installing-amazon-efs-utils.md)\.

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

### Mounting with IAM authorization<a name="mounting-IAM-option"></a>

To mount your Amazon EFS file system on Linux instances using AWS Identity and Access Management \(IAM\) authorization, you use the EFS mount helper\. For more information about IAM authorization for NFS clients, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\.

#### Mounting with IAM using an EC2 instance profile<a name="mount-iam-ec2-profile"></a>

If you are mounting with IAM authorization to an Amazon EC2 instance with an instance profile, use the `tls` and `iam` mount options, shown following\.

```
$ sudo mount -t efs -o tls,iam file-system-id efs-mount-point
```

To automatically mount with IAM authorization to an Amazon EC2 instance that has an instance profile, add the following line to the `/etc/fstab` file on the EC2 instance\.

```
file-system-id:/ efs-mount-point efs _netdev,tls,iam 0 0
```

#### Mounting with IAM using a named profile<a name="mount-iam-creds-file"></a>

You can mount with IAM authorization using the IAM credentials located in the AWS CLI credentials file `~/.aws/credentials`, or the AWS CLI config file `~/.aws/config`\. If `"awsprofile"` is not specified, the "default" profile is used\.

To mount with IAM authorization to a Linux instance using a credentials file, use the `tls`, `awsprofile`, and `iam` mount options, shown following\.

```
$ sudo mount -t efs -o tls,iam,awsprofile=namedprofile file-system-id efs-mount-point/
```

To automatically mount with IAM authorization to a Linux instance using a credentials file, add the following line to the `/etc/fstab` file on the EC2 instance\.

```
file-system-id:/ efs-mount-point efs _netdev,tls,iam,awsprofile=namedprofile 0 0
```

### Mounting with EFS access points<a name="mounting-access-points"></a>

You can mount an EFS file system using an EFS access point\. To do this, use the EFS mount helper\. 

When you mount a file system using an access point, the mount command includes the `access-point-id` and the `tls` mount option in addition to the regular mount options\. An example is shown following\. 

```
$ sudo mount -t efs -o tls,accesspoint=access-point-id file-system-id efs-mount-point
```

To automatically mount a file system using an access point, add the following line to the `/etc/fstab` file on the EC2 instance\.

```
file-system-id efs-mount-point efs _netdev,tls,accesspoint=access-point-id 0 0
```

For more information about EFS access points, see [Working with Amazon EFS Access Points](efs-access-points.md)\.

### Mounting automatically with EFS mount helper<a name="mount-helper-automount"></a>

You also have the option of mounting automatically by adding an entry to your `/etc/fstab` file\. When you mount automatically using `/etc/fstab`, you must add the `_netdev` mount option\. For more information, see [Using /etc/fstab to mount automatically](mount-fs-auto-mount-onreboot.md#mount-fs-auto-mount-update-fstab)\.

**Note**  
Mounting with the mount helper automatically uses the following mount options that are optimized for Amazon EFS:  
`nfsvers=4.1`
`rsize=1048576`
`wsize=1048576`
`hard`
`timeo=600`
`retrans=2`
`noresvport`

To use the `mount` command, the following must be true:
+ The connecting EC2 instance must be in a virtual private cloud \(VPC\) based on the Amazon VPC service\. It also must be configured to use the DNS server provided by Amazon\. For information about the Amazon DNS server, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\. 
+ The VPC of the connecting EC2 instance must have DNS hostnames enabled\. For more information, see [Viewing DNS Hostnames for Your EC2 Instance](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-viewing) in the *Amazon VPC User Guide*\. 

**Note**  
We recommend that you wait 90 seconds after creating a mount target before you mount your file system\. This wait lets the DNS records propagate fully in the AWS Region where the file system is\.

### Mounting on your on\-premises Linux client with the EFS mount helper over AWS Direct Connect and VPN<a name="mounting-fs-mount-helper-direct"></a>

You can mount your Amazon EFS file systems on your on\-premises data center servers when connected to your Amazon VPC with AWS Direct Connect or VPN\. Mounting your Amazon EFS file systems with amazon\-efs\-utils also makes mounting simpler with the mount helper and allows you to enable encryption of data in transit\. 

To see how to use amazon\-efs\-utils with AWS Direct Connect and VPN to mount Amazon EFS file systems onto on\-premises Linux clients, see [Walkthrough: Create and Mount a File System On\-Premises with AWS Direct Connect and VPN](efs-onpremises.md)\.