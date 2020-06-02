# Overview<a name="overview-amazon-efs-utils"></a>

The amazon\-efs\-utils package is an open\-source collection of Amazon EFS tools\. There's no additional cost to use amazon\-efs\-utils, and you can download these tools from GitHub here: [https://github\.com/aws/efs\-utils](https://github.com/aws/efs-utils)\. The amazon\-efs\-utils package is available in the Amazon Linux package repositories, and you can build and install the package on other Linux distributions\.

The amazon\-efs\-utils package comes with a mount helper and tooling that makes it easier to perform encryption of data in transit for Amazon EFS\. A *mount helper* is a program that you use when you mount a specific type of file system\. We recommend that you use the mount helper included in amazon\-efs\-utils to mount your Amazon EFS file systems\.

The following dependencies exist for amazon\-efs\-utils and are installed when you install the amazon\-efs\-utils package:
+ NFS client \(the nfs\-utils package\)
+ Network relay \(stunnel package, version 4\.56 or later\)
+ Python \(version 2\.7 or later\)
+ OpenSSL 1\.0\.2 or newer

**Note**  
By default, when using the Amazon EFS mount helper with Transport Layer Security \(TLS\), the mount helper enforces certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux versions, mounting an Amazon EFS file system using TLS fails\.  
When you've installed the amazon\-efs\-utils package, to upgrade your system's version of stunnel, see [Upgrading Stunnel](using-amazon-efs-utils.md#upgrading-stunnel)\.  
For issues with encryption, see [Troubleshooting Encryption](troubleshooting-efs-encryption.md)\.

The following Linux distributions support amazon\-efs\-utils:
+ Amazon Linux 2
+ Amazon Linux
+ Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer
+ Ubuntu 16\.04 LTS and newer

In the following sections, you can find out how to install amazon\-efs\-utils on your Linux instances\.