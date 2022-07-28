# Overview<a name="overview-amazon-efs-utils"></a>

The Amazon EFS client \(`amazon-efs-utils`\) is an open\-source collection of Amazon EFS tools\. There's no additional cost to use the Amazon EFS client, which you can download from GitHub here: [https://github\.com/aws/efs\-utils](https://github.com/aws/efs-utils)\. The `amazon-efs-utils` package is available in the Amazon Linux package repositories, and you can build and install the package on other Linux distributions\. You can also use AWS Systems Manager to automatically install or update the package\. For more information, see [Using AWS Systems Manager to automatically install or update Amazon EFS clients](manage-efs-utils-with-aws-sys-manager.md)\.

**Note**  
The `amazon-efs-utils` package comes preinstalled on Amazon Linux and Amazon Linux 2 Amazon Machine Images \(AMIs\)\.

The Amazon EFS client includes a mount helper and tooling that makes it easier to perform encryption of data in transit for Amazon EFS file systems\. A *mount helper* is a program that you use when you mount a specific type of file system\. We recommend that you use the mount helper included with the Amazon EFS client to mount your Amazon EFS file systems\. Using the Amazon EFS client simplifies mounting EFS file systems, and can provide improved file system performance\. For more information about using the EFS client and mount helper, see [Mounting EFS file systems](mounting-fs.md)\.

 

The following dependencies exist for amazon\-efs\-utils and are installed when you install the amazon\-efs\-utils package:
+ NFS client
  + `nfs-utils` for RHEL, CentOS, Amazon Linux, and Fedora distributions
  + `nfs-common` for Debian and Ubuntu distributions
+ Network relay \(stunnel package, version 4\.56 or later\)
+ Python \(version 3\.4 or later\)
+ OpenSSL 1\.0\.2 or newer

**Note**  
By default, when using the Amazon EFS mount helper with Transport Layer Security \(TLS\), the mount helper enforces certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux versions, mounting an Amazon EFS file system using TLS fails\.  
When you've installed the amazon\-efs\-utils package, to upgrade your system's version of stunnel, see [Upgrading `stunnel`](upgrading-stunnel.md)\.  
You can use AWS Systems Manager to manage Amazon EFS clients and automate the tasks required to install or update the amazon\-efs\-utils package on your EC2 instances\. For more information, see [Using AWS Systems Manager to automatically install or update Amazon EFS clients](manage-efs-utils-with-aws-sys-manager.md)\.  
For issues with encryption, see [Troubleshooting Encryption](troubleshooting-efs-encryption.md)\.

## Supported distributions<a name="efs-utils-supported-distros"></a>

The Amazon EFS client has been verified against the following Linux and Mac distributions:


| Distribution | Package type | `init` system | 
| --- | --- | --- | 
| Amazon Linux 2017\.09 | rpm | upstart | 
| Amazon Linux 2 | rpm | systemd | 
| CentOS 7, 8 | rpm | systemd | 
| Debian 9, 10 | deb | systemd | 
| Fedora 28 \- 32 | rpm | systemd | 
| macOS Big Sur |  | launchd | 
| OpenSUSE Leap, Tumbleweed | rpm | systemd | 
| Red Hat Enterprise Linux \(RHEL\) 7, 8 | rpm | systemd | 
| SUSE Linux Enterprise Server \(SLES\) 12, 15 | rpm | systemd | 
| Ubuntu 16\.04 LTS, 18\.04 LTS, 20\.04 LTS | deb | systemd | 

For a complete list of supported distributions that the package has been verified against, see the efs\-utils [README](https://github.com/aws/efs-utils/blob/master/README.md) on Github\.

In the following sections, you can learn how to install the Amazon EFS client on your EC2 Linux or Mac instances\.
