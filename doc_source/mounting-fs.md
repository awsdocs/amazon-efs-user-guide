# Mounting EFS file systems<a name="mounting-fs"></a>

In the following section, you can learn how to mount your Amazon EFS file system on a Linux instance using the Amazon EFS mount helper\. In addition, you can find how to use the file `fstab` to automatically remount your file system after any system restarts\. To learn more about the Amazon EFS mount helper, see [EFS Mount Helper](efs-mount-helper.md)\.

**Note**  
Amazon EFS does not support mounting from Amazon EC2 Windows instances\.
Amazon EFS does not support mounting from Amazon EC2 Mac instances\.

Before the Amazon EFS mount helper was available, we recommended mounting your Amazon EFS file systems using the standard Linux NFS client\. For more information on those changes, see [Mounting File Systems Without the EFS Mount Helper](mounting-fs-old.md)\.

**Note**  
You can configure a new Amazon EC2 Linux instance to mount an Amazon EFS file system when it launches\. However, before you mount a file system, you must create, configure, and launch your related AWS resources\. For detailed instructions, see [Getting Started with Amazon Elastic File System](getting-started.md)\.

**Topics**
+ [Troubleshooting AMI and kernel versions](#ami-kernel-versions-troubleshooting)
+ [Installing the amazon\-efs\-utils package](#mounting-fs-install-amazon-efs-utils)
+ [Mounting with the EFS mount helper](mounting-fs-mount-helper.md)
+ [Mounting your Amazon EFS file system automatically](mount-fs-auto-mount-onreboot.md)
+ [Mounting EFS to multiple EC2 instances using AWS Systems Manager](mount-multiple-ec2-instances.md)
+ [Mounting EFS file systems from another account or VPC](manage-fs-access-vpc-peering.md)
+ [Additional mounting considerations](mounting-fs-mount-cmd-general.md)

## Troubleshooting AMI and kernel versions<a name="ami-kernel-versions-troubleshooting"></a>

To troubleshoot issues related to certain Amazon Machine Image \(AMI\) or kernel versions when using Amazon EFS from an Amazon EC2 instance, see [Troubleshooting AMI and Kernel Issues](troubleshooting-efs-ami-kernel.md)\.

**Note**  
Amazon EFS does not support mounting from Amazon EC2 Windows instances\.
Amazon EFS does not support mounting from Amazon EC2 Mac instances\.

## Installing the amazon\-efs\-utils package<a name="mounting-fs-install-amazon-efs-utils"></a>

To mount your Amazon EFS file system on your Amazon EC2 instance, we recommend that you use the mount helper in the amazon\-efs\-utils package\. The amazon\-efs\-utils package is an open\-source collection of Amazon EFS tools\. For more information, see [Installing the amazon\-efs\-utils Package on Amazon Linux](installing-amazon-efs-utils.md)\.