# Using the amazon\-efs\-utils Tools<a name="using-amazon-efs-utils"></a>

Following, you can find a description of amazon\-efs\-utils, an open\-source collection of Amazon EFS tools\.

**Topics**
+ [Overview](#overview-amazon-efs-utils)
+ [Installing the amazon\-efs\-utils Package on Amazon Linux](#installing-amazon-efs-utils)
+ [Upgrading Stunnel](#upgrading-stunnel)
+ [EFS Mount Helper](#efs-mount-helper)

## Overview<a name="overview-amazon-efs-utils"></a>

The amazon\-efs\-utils package is an open\-source collection of Amazon EFS tools\. There's no additional cost to use amazon\-efs\-utils, and you can download these tools from GitHub here: [https://github\.com/aws/efs\-utils](https://github.com/aws/efs-utils)\. The amazon\-efs\-utils package is available in the Amazon Linux package repositories, and you can build and install the package on other Linux distributions\.

The amazon\-efs\-utils package comes with a mount helper and tooling that makes it easier to perform encryption of data in transit for Amazon EFS\. A *mount helper* is a program that you use when you mount a specific type of file system\. We recommend that you use the mount helper included in amazon\-efs\-utils to mount your Amazon EFS file systems\.

The following dependencies exist for amazon\-efs\-utils and are installed when you install the amazon\-efs\-utils package:
+ NFS client \(the nfs\-utils package\)
+ Network relay \(stunnel package, version 4\.56 or later\)
+ Python \(version 2\.7 or later\)
+ OpenSSL 1\.0\.2 or newer

**Note**  
By default, when using the Amazon EFS mount helper with TLS, it enforces the use of the Online Certificate Status Protocol \(OCSP\) and certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Note that some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux versions, mounting an Amazon EFS file system using TLS will fail\.  
Once you've installed the amazon\-efs\-utils package, to upgrade your system's version of stunnel, see [Upgrading Stunnel](#upgrading-stunnel)\.  
For issues with encryption, see [ Troubleshooting Encryption    Mounting with Encryption of Data in Transit Fails  By default, when using the Amazon EFS mount helper with TLS, it enforces use of the Online Certificate Status Protocol \(OCSP\) and certificate hostname checking\. If your system does not support either of these features \(for example, when using Red Hat Enterprise Linux or CentOS\), mounting an EFS file system using TLS will fail\.   Mounting with Encryption of Data in Transit is Interrupted  It's possible, however unlikely, that your encrypted connection to your Amazon EFS file system can hang or be interrupted by client\-side events\.  Action to Take If your connection to your Amazon EFS file system with encryption of data in transit is interrupted, take the following steps:    Ensure that the stunnel service is running on the client\.   Confirm that the watchdog application `amazon-efs-mount-watchdog` is running on the client\. You can find out whether this application is running with the following command: 

   ```
   ps aux | grep [a]mazon-efs-mount-watchdog
   ```   Check your support logs\. For more information, see [Getting Support Logs](using-amazon-efs-utils.md#mount-helper-logs)\.   Optionally, you can enable your stunnel logs and check the information in those as well\. You can change the configuration of your logs in `/etc/amazon/efs/amazon-efs-utils.conf` to enable the stunnel logs\. However, doing so requires unmounting and then remounting the file system with the mount helper for the changes to take effect\.  Enabling the stunnel logs can use up a nontrivial amount of space on your file system\.    If the interruptions continue, contact AWS Support\.   Encrypted\-at\-Rest File System Can't Be Created  You've tried to create a new encrypted\-at\-rest file system\. However, you get an error message saying that AWS KMS is unavailable\.  Action to Take This error can occur in the rare case that AWS KMS becomes temporarily unavailable in your AWS Region\. If this happens, wait until AWS KMS returns to full availability, and then try again to create the file system\.     Unusable Encrypted File System  An encrypted file system consistently returns NFS server errors\. These errors can occur when EFS can't retrieve your master key from AWS KMS for one of the following reasons:   The key was disabled\.   The key was deleted\.   Permission for Amazon EFS to use the key was revoked\.   AWS KMS is temporarily unavailable\.    Action to Take First, confirm that the AWS KMS key is enabled\. You can do so by viewing the keys in the console\. For more information, see [Viewing Keys](http://docs.aws.amazon.com/kms/latest/developerguide/viewing-keys.html) in the *AWS Key Management Service Developer Guide*\.  If the key is not enabled, enable it\. For more information, see [Enabling and Disabling Keys](http://docs.aws.amazon.com/kms/latest/developerguide/enabling-keys.html) in the *AWS Key Management Service Developer Guide*\. If the key is pending deletion, then this status disables the key\. You can cancel the deletion, and re\-enable the key\. For more information, see [Scheduling and Canceling Key Deletion](http://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html#deleting-keys-scheduling-key-deletion) in the *AWS Key Management Service Developer Guide*\. If the key is enabled, and you're still experiencing an issue, or if you encounter an issue re\-enabling your key, contact AWS Support\.  ](troubleshooting-efs-encryption.md)\.

The following Linux distributions support amazon\-efs\-utils:
+ Amazon Linux 2
+ Amazon Linux
+ Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer
+ Ubuntu 16\.04 LTS and newer

In the following sections, you can find out how to install amazon\-efs\-utils on your Linux instances\.

## Installing the amazon\-efs\-utils Package on Amazon Linux<a name="installing-amazon-efs-utils"></a>

The amazon\-efs\-utils package is available for installation in Amazon Linux and the Amazon Machine Images \(AMIs\) for Amazon Linux 2\.

**Note**  
If you're using AWS Direct Connect, you can find installation instructions in [Walkthrough 5: Create and Mount a File System On\-Premises with AWS Direct Connect](efs-onpremises.md)\.

**Installing amazon\-efs\-utils Package**

1. Make sure that you've created an Amazon Linux or Amazon Linux 2 EC2 instance\. For information on how to do this, see [Step 1: Launch an Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the* Amazon EC2 User Guide for Linux Instances\.*

1. Access the terminal for your instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to Your Linux Instance Using SSH](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Run the following command to install amazon\-efs\-utils\.

   ```
   sudo yum install -y amazon-efs-utils
   ```

### Installing the amazon\-efs\-utils Package on Other Linux Distributions<a name="installing-other-distro"></a>

If you're not using Amazon Linux, the amazon\-efs\-utils package is also available on GitHub\. 

**Cloning amazon\-efs\-utils from GitHub**

1. Make sure that you've created an EC2 instance of the supported AMI type\. For more information on how to do this, see [Step 1: Launch an Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Access the terminal for your instance through SSH, and log in with the appropriate user name\. For more information on how to do this, see [Connecting to Your Linux Instance Using SSH](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. From the terminal, clone the amazon\-efs\-utils tool from GitHub to a directory of your choice, with the following command\. 

   ```
   git clone https://github.com/aws/efs-utils
   ```

Once you clone the package, you can build and install amazon\-efs\-utils using one of the following methods, depending on the package type supported by your Linux distribution:
+ **RPM** – This package type is supported by Amazon Linux, Red Hat Linux, CentOS, and similar\.
+ **DEB** – This package type is supported by Ubuntu, Debian, and similar\.

**To build and install amazon\-efs\-utils as an RPM package**

1. Open a terminal on your client and navigate to the directory that has the cloned amazon\-efs\-utils package from GitHub\.

1. Build the package with the following command\.

   ```
   make rpm
   ```
**Note**  
If you haven't already, you'll need to install the rpm\-builder package with the following command:  

   ```
   sudo yum -y install rpm-build
   ```

1. Install the package with the following command\.

   ```
   sudo yum -y install build/amazon-efs-utils*rpm
   ```

**To build and install amazon\-efs\-utils as a DEB package**

1. Open a terminal on your client and navigate to the directory that has the cloned amazon\-efs\-utils package from GitHub\.

1. Build the package with the following command\.

   ```
   ./build-deb.sh
   ```

1. Install the package with the following command\.

   ```
   sudo apt-get install build/amazon-efs-utils*deb
   ```

## Upgrading Stunnel<a name="upgrading-stunnel"></a>

>Using encryption of data in transit with the Amazon EFS mount helper requires OpenSSL version 1\.0\.2 or newer, and a version of stunnel that supports both OSCP and certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Note that some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux versions, mounting an Amazon EFS file system using TLS will fail\.

After installing the Amazon EFS mount helper, you can upgrade your system's version of stunnel with the following instructions\.

1. Open a terminal on your Linux client, and run the following commands in order\.

1. `sudo yum install -y gcc openssl-devel tcp_wrappers-devel`

1. `curl -o stunnel-5.44.tar.gz https://www.stunnel.org/downloads/stunnel-5.44.tar.gz`

1. `tar xvfz stunnel-5.44.tar.gz`

1. `cd stunnel-5.44/`

1. `./configure`

1. `make`

1. `sudo make install` 

1. 

   ```
   if [[-f /bin/stunnel ]]; then
   sudo mv /bin/stunnel /root
   fi
   ```

1. `sudo ln -s /usr/local/bin/stunnel /bin/stunnel`

Once you've installed a version of stunnel with the required features, you can mount your file system using TLS with the recommended settings\.

If you are unable to install the required dependencies, you can optionally disable OCSP and certificate hostname checking inside the Amazon EFS mount helper configuration\. We do not recommend that you disable these features in production environments\. To do so:

1. Using your text editor of choice, open the `/etc/amazon/efs/efs-utils.conf` file\.

1. Set the `stunnel_check_cert_hostname` value to false\.

1. Set the `stunnel_check_cert_validity` value to false\.

1. Save the changes to the file and close it\.

For more information on using encryption of data in transit, see [Mounting File Systems](mounting-fs.md)\.

## EFS Mount Helper<a name="efs-mount-helper"></a>

The Amazon EFS mount helper simplifies mounting your file systems\. It includes the Amazon EFS recommended mount options by default\. Additionally, the mount helper has built\-in logging for troubleshooting purposes\. If you encounter an issue with your Amazon EFS file system, you can share these logs with AWS Support\. 

### How It Works<a name="efs-mount-helper-how"></a>

The mount helper defines a new network file system type, called `efs`, which is fully compatible with the standard `mount` command in Linux\. The mount helper also supports mounting an Amazon EFS file system at instance boot time automatically by using entries in the `/etc/fstab` configuration file\.

**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting-efs-mounting.md#automount-fails)\.

When encryption of data in transit is declared as a mount option for your Amazon EFS file system, the mount helper initializes a client stunnel process, and a supervisor process called `amazon-efs-mount-watchdog`\. Stunnel is an multipurpose network relay that is open\-source\. The client stunnel process listens on a local port for inbound traffic, and the mount helper redirects NFS client traffic to this local port\. The mount helper uses Transport Layer Security \(TLS\) version 1\.2 to communicate with your file system\.

Using TLS requires certificates, and these certificates are signed by a trusted Amazon Certificate Authority\. For more information on how encryption works, see [Encrypting Data and Metadata in EFS](encryption.md)\.

### Using the EFS Mount Helper<a name="using-efs-mount-helper"></a>

The mount helper helps you mount your EFS file systems on your Linux EC2 instances\. For more information, see [Mounting File Systems](mounting-fs.md)\. 

### Getting Support Logs<a name="mount-helper-logs"></a>

The mount helper has built\-in logging for your Amazon EFS file system\. You can share these logs with AWS Support for troubleshooting purposes\. 

You can find the logs stored in `/var/log/amazon/efs` for systems with the mount helper installed\. These logs are for the mount helper, the stunnel process itself, and for the `amazon-efs-mount-watchdog` process that monitors the stunnel process\.

**Note**  
The watchdog process ensures that each mount's stunnel process is running, and stops the stunnel when the Amazon EFS file system is unmounted\. If for some reason a stunnel process is terminated unexpectedly, the watchdog process restarts it\.

You can change the configuration of your logs in `/etc/amazon/efs/amazon-efs-utils.conf`\. However, doing so requires unmounting and then remounting the file system with the mount helper for the changes to take effect\. Log capacity for the mount helper and watchdog logs is limited to 20 MiB\. Logs for the stunnel process are disabled by default\.

**Important**  
You can enable logging for the stunnel process logs\. However, enabling the stunnel logs can use up a nontrivial amount of space on your file system\.

### Using amazon\-efs\-utils with AWS Direct Connect<a name="amazon-efs-utils-direct"></a>

You can mount your Amazon EFS file systems on your on\-premises data center servers when connected to your Amazon VPC with AWS Direct Connect\. Using amazon\-efs\-utils also makes mounting simpler with the mount helper and allows you to enable encryption of data in transit\. To see how to use amazon\-efs\-utils with AWS Direct Connect to mount Amazon EFS file systems onto on\-premises Linux clients, see [Walkthrough 5: Create and Mount a File System On\-Premises with AWS Direct Connect](efs-onpremises.md)\.

### Related Topics<a name="amazon-efs-utils-related"></a>

For more information on the Amazon EFS mount helper, see these related topics:
+ [Encrypting Data and Metadata in EFS](encryption.md)
+ [Mounting File Systems](mounting-fs.md)