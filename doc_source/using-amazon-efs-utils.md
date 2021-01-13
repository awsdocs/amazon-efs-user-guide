# Using the amazon\-efs\-utils Tools<a name="using-amazon-efs-utils"></a>

Following, you can find a description of the Amazon EFS client, which is installed using the amazon\-efs\-utils package, an open\-source collection of Amazon EFS tools\.

**Topics**
+ [Overview](#overview-amazon-efs-utils)
+ [Using AWS Systems Manager to automatically install or update Amazon EFS clients](manage-efs-utils-with-aws-sys-manager.md)
+ [Installing the amazon\-efs\-utils Package on Amazon Linux](installing-amazon-efs-utils.md)
+ [Installing the amazon\-efs\-utils package on other Linux distributions](installing-other-distro.md)
+ [Upgrading Stunnel](upgrading-stunnel.md)
+ [EFS Mount Helper](efs-mount-helper.md)

## Overview<a name="overview-amazon-efs-utils"></a>

The Amazon EFS client \(amazon\-efs\-utils package\) is an open\-source collection of Amazon EFS tools\. There's no additional cost to use the Amazon EFS client, which you can download from GitHub here: [https://github\.com/aws/efs\-utils](https://github.com/aws/efs-utils)\. The amazon\-efs\-utils package is available in the Amazon Linux package repositories, and you can build and install the package on other Linux distributions\. You can also use AWS Systems Manager to automatically install or update the package\. For more information, see [Using AWS Systems Manager to automatically install or update Amazon EFS clients](manage-efs-utils-with-aws-sys-manager.md)\.

The Amazon EFS client includes a mount helper and tooling that makes it easier to perform encryption of data in transit for Amazon EFS file systems\. A *mount helper* is a program that you use when you mount a specific type of file system\. We recommend that you use the mount helper included with the Amazon EFS client to mount your Amazon EFS file systems\.

The following dependencies exist for amazon\-efs\-utils and are installed when you install the amazon\-efs\-utils package:
+ NFS client
  + `nfs-utils` for RHEL, CentOS, Amazon Linux, and Fedora distributions
  + `nfs-common` for Debian and Ubuntu distributions
+ Network relay \(stunnel package, version 4\.56 or later\)
+ Python \(version 2\.7 or later\)
+ OpenSSL 1\.0\.2 or newer

**Note**  
By default, when using the Amazon EFS mount helper with Transport Layer Security \(TLS\), the mount helper enforces certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux versions, mounting an Amazon EFS file system using TLS fails\.  
When you've installed the amazon\-efs\-utils package, to upgrade your system's version of stunnel, see [Upgrading Stunnel](upgrading-stunnel.md)\.  
You can use AWS Systems Manager to manage Amazon EFS clients and automate the tasks required to install or update the amazon\-efs\-utils package on your EC2 instances\. For more information, see [Using AWS Systems Manager to automatically install or update Amazon EFS clients](manage-efs-utils-with-aws-sys-manager.md)\.  
For issues with encryption, see [Troubleshooting Encryption](troubleshooting-efs-encryption.md)\.

The following Linux distributions support `amazon-efs-utils:`
+ Amazon Linux 2
+ Amazon Linux
+ Debian version 9 and newer
+ Fedora version 28 and newer
+ Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer
+ Ubuntu 16\.04 LTS and newer

In the following sections, you can find out how to install amazon\-efs\-utils on your Linux instances\.