# Mounting EFS file systems<a name="mounting-fs"></a>

In the following sections you can learn how to mount your Amazon EFS file system using the Amazon EFS mount helper\. In addition, learn how to automatically remount your file system after any system restarts using the file `fstab` file\. Using the EFS mount helper, you have the following options for mounting your Amazon EFS file system:
+ Mounting on supported EC2 instances
+ Mounting with IAM authorization
+ Mounting with Amazon EFS access points
+ Mounting with an on\-premise Linux client
+ Auto\-mounting when an EC2 instance reboots
+ Mounting a file system when a new EC2 instance launches

**Note**  
Amazon EFS does not support mounting from Amazon EC2 Windows instances\.

The EFS mount helper is part of the `amazon-efs-utils` package\. The `amazon-efs-utils` package is an open\-source collection of Amazon EFS tools\. For more information, see [Manually installing the Amazon EFS client](installing-amazon-efs-utils.md)\.

Before the Amazon EFS mount helper was available, we recommended mounting your Amazon EFS file systems using the standard Linux NFS client\. For more information, see [Mounting file systems without the EFS mount helper](mounting-fs-old.md)\.

**Topics**
+ [Mounting file systems using the EFS mount helper](efs-mount-helper.md)
+ [Additional mounting considerations](mounting-fs-mount-cmd-general.md)
+ [Troubleshooting AMI and kernel versions](ami-kernel-versions-troubleshooting.md)