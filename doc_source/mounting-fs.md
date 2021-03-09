# Mounting EFS file systems<a name="mounting-fs"></a>

In the following section, you can learn how to mount your Amazon EFS file system on an Amazon EC2 Linux instance using the Amazon EFS mount helper\. In addition, you can find how to use the file `fstab` to automatically remount your file system after any system restarts\. Using the EFS mount helper, you have the following options for mounting your Amazon EFS file system:
+ Mounting on supported EC2 instances
+ Mounting with IAM authorization
+ Mounting with Amazon EFS access points
+ Mounting with an on\-premise Linux client
+ Auto\-mounting when an EC2 instance reboots
+ Mounting a file system when a new EC2 instance launches

The EFS mount helper is part of the `amazon-efs-utils` package\. The `amazon-efs-utils` package is an open\-source collection of Amazon EFS tools\. For more information, see [Manually installing the Amazon EFS client](installing-amazon-efs-utils.md)\.

**Note**  
Amazon EFS does not support mounting from Amazon EC2 Windows instances\.

Before the Amazon EFS mount helper was available, we recommended mounting your Amazon EFS file systems using the standard Linux NFS client\. For more information, see [Mounting file systems without the EFS mount helper](mounting-fs-old.md)\.

**Topics**
+ [Mounting with the EFS mount helper](mounting-fs-mount-helper.md)
+ [Mounting your Amazon EFS file system automatically](mount-fs-auto-mount-onreboot.md)
+ [Mounting EFS to multiple EC2 instances using AWS Systems Manager](mount-multiple-ec2-instances.md)
+ [Mounting EFS file systems from another account or VPC](manage-fs-access-vpc-peering.md)
+ [Additional mounting considerations](mounting-fs-mount-cmd-general.md)
+ [Troubleshooting AMI and kernel versions](ami-kernel-versions-troubleshooting.md)