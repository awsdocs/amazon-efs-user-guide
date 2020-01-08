# Using the amazon\-efs\-utils Tools<a name="using-amazon-efs-utils"></a>

Following, you can find a description of amazon\-efs\-utils, an open\-source collection of Amazon EFS tools\.

**Topics**
+ [Overview](#overview-amazon-efs-utils)
+ [Installing the amazon\-efs\-utils Package on Amazon Linux](#installing-amazon-efs-utils)
+ [Installing the amazon\-efs\-utils Package on Other Linux Distributions](#installing-other-distro)
+ [Upgrading Stunnel](#upgrading-stunnel)
+ [EFS Mount Helper](efs-mount-helper.md)

## Overview<a name="overview-amazon-efs-utils"></a>

The amazon\-efs\-utils package is an open\-source collection of Amazon EFS tools\. There's no additional cost to use amazon\-efs\-utils, and you can download these tools from GitHub here: [https://github\.com/aws/efs\-utils](https://github.com/aws/efs-utils)\. The amazon\-efs\-utils package is available in the Amazon Linux package repositories, and you can build and install the package on other Linux distributions\.

The amazon\-efs\-utils package comes with a mount helper and tooling that makes it easier to perform encryption of data in transit for Amazon EFS\. A *mount helper* is a program that you use when you mount a specific type of file system\. We recommend that you use the mount helper included in amazon\-efs\-utils to mount your Amazon EFS file systems\.

The following dependencies exist for amazon\-efs\-utils and are installed when you install the amazon\-efs\-utils package:
+ NFS client \(the nfs\-utils package\)
+ Network relay \(stunnel package, version 4\.56 or later\)
+ Python \(version 2\.7 or later\)
+ OpenSSL 1\.0\.2 or newer

**Note**  
By default, when using the Amazon EFS mount helper with Transport Layer Security \(TLS\), the mount helper enforces certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux versions, mounting an Amazon EFS file system using TLS fails\.  
When you've installed the amazon\-efs\-utils package, to upgrade your system's version of stunnel, see [Upgrading Stunnel](#upgrading-stunnel)\.  
For issues with encryption, see [Troubleshooting Encryption](troubleshooting-efs-encryption.md)\.

The following Linux distributions support amazon\-efs\-utils:
+ Amazon Linux 2
+ Amazon Linux
+ Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer
+ Ubuntu 16\.04 LTS and newer

In the following sections, you can find out how to install amazon\-efs\-utils on your Linux instances\.

## Installing the amazon\-efs\-utils Package on Amazon Linux<a name="installing-amazon-efs-utils"></a>

The amazon\-efs\-utils package is available for installation in Amazon Linux and the Amazon Machine Images \(AMIs\) for Amazon Linux 2\.

**Note**  
If you're using AWS Direct Connect, you can find installation instructions in [Walkthrough: Create and Mount a File System On\-Premises with AWS Direct Connect and VPN](efs-onpremises.md)\.

**To install the amazon\-efs\-utils package**

1. Make sure that you've created an Amazon Linux or Amazon Linux 2 EC2 instance\. For information on how to do this, see [Step 1: Launch an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the* Amazon EC2 User Guide for Linux Instances\.*

1. Access the terminal for your instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Run the following command to install amazon\-efs\-utils\.

   ```
   sudo yum install -y amazon-efs-utils
   ```

## Installing the amazon\-efs\-utils Package on Other Linux Distributions<a name="installing-other-distro"></a>

If you don't want to get the amazon\-efs\-utils package from Amazon Linux or Amazon Linux 2 AMIs, the amazon\-efs\-utils package is also available on GitHub\.

**To clone amazon\-efs\-utils from GitHub**

1. Make sure that you've created an Amazon EC2 instance of the supported AMI type\. For more information on how to do this, see [Step 1: Launch an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Access the terminal for your instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. If you haven't done so already, install git with the following command\.

   ```
   sudo yum -y install git
   ```

1. From the terminal, clone the amazon\-efs\-utils tool from GitHub to a directory of your choice, with the following command\.

   ```
   git clone https://github.com/aws/efs-utils
   ```

   Because you need the bash command `make`, you can install it with the following command if your operating system doesn't already have it\.

   ```
        sudo yum -y install make
   ```

After you clone the package, you can build and install amazon\-efs\-utils using one of the following methods, depending on the package type supported by your Linux distribution:
+ **RPM** – This package type is supported by Amazon Linux, Red Hat Linux, CentOS, and similar\.
+ **DEB** – This package type is supported by Ubuntu, Debian, and similar\.

**To build and install amazon\-efs\-utils as an RPM package**

1. Open a terminal on your client and navigate to the directory that has the cloned amazon\-efs\-utils package from GitHub \(for example "/home/centos/efs\-utils"\)\.

1. If you haven't done so already, install the rpm\-builder package with the following command\.

   ```
   sudo yum -y install rpm-build
   ```

1. Build the package with the following command\.

   ```
   sudo make rpm
   ```

1. Install the amazon\-efs\-utils package with the following command\.

   ```
   sudo yum -y install ./build/amazon-efs-utils*rpm
   ```

**To build and install amazon\-efs\-utils as a DEB package**

1. Open a terminal on your client and navigate to the directory that has the cloned amazon\-efs\-utils package from GitHub\.

1. Install the binutils package, a dependency for building DEB packages\.

   ```
   sudo apt-get -y install binutils
   ```

1. Build the package with the following command\.

   ```
   ./build-deb.sh
   ```

1. Install the package with the following command\.

   ```
   sudo apt-get -y install ./build/amazon-efs-utils*deb
   ```

## Upgrading Stunnel<a name="upgrading-stunnel"></a>

Using encryption of data in transit with the Amazon EFS mount helper requires OpenSSL version 1\.0\.2 or newer, and a version of stunnel that supports both OCSP and certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Note that some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux versions, mounting an Amazon EFS file system using TLS fails\.

After installing the Amazon EFS mount helper, you can upgrade your system's version of stunnel with the following instructions\.

**To upgrade stunnel**

1.  In a web browser, go to the stunnel downloads page [https://stunnel\.org/downloads\.html](https://www.stunnel.org/downloads.html)\. 

1.  Locate the latest stunnel version that is available in tar\.gz format\. Note the name of the file as you will need it in the following steps\. 

1. Open a terminal on your Linux client, and run the following commands in the order presented\.

1. 

   ```
   $ sudo yum install -y gcc openssl-devel tcp_wrappers-devel
   ```

1. Replace *latest\-stunnel\-version* with the name of the file you noted previously in Step 2\.

   ```
   $ sudo curl -o latest-stunnel-version.tar.gz https://www.stunnel.org/downloads/latest-stunnel-version.tar.gz
   ```

1. 

   ```
   $ sudo tar xvfz latest-stunnel-version.tar.gz
   ```

1. 

   ```
   $ cd latest-stunnel-version/
   ```

1. 

   ```
   $ sudo ./configure
   ```

1. 

   ```
   $ sudo make
   ```

1. The current amazon\-efs\-utils package is installed in `bin/stunnel`\. So that the new version can be installed, remove that directory with the following command\.

   ```
   $ sudo rm /bin/stunnel
   ```

1. 

   ```
   $ sudo make install
   ```

1. 
**Note**  
The default CentOS shell is csh, which has different syntax than the bash shell\. The following code first invokes bash, then runs\.

   ```
   bash
   ```

   ```
   if [[ -f /bin/stunnel ]]; then
   sudo mv /bin/stunnel /root
   fi
   ```

1. 

   ```
   $ sudo ln -s /usr/local/bin/stunnel /bin/stunnel
   ```

After you've installed a version of stunnel with the required features, you can mount your file system using TLS with the recommended settings\.

### Disabling Certificate Hostname Checking<a name="disable-cert-hn-checking"></a>

If you are unable to install the required dependencies, you can optionally disable certificate hostname checking inside the Amazon EFS mount helper configuration\. We do not recommend that you disable this feature in production environments\. To disable certificate host name checking, do the following:

1. Using your text editor of choice, open the `/etc/amazon/efs/efs-utils.conf` file\.

1. Set the `stunnel_check_cert_hostname` value to false\.

1. Save the changes to the file and close it\.

For more information on using encryption of data in transit, see [Mounting EFS File Systems](mounting-fs.md)\.

### Enabling Online Certificate Status Protocol<a name="tls-ocsp"></a>

 In order to maximize file system availability in the event that the CA is not reachable from your VPC, the Online Certificate Status Protocol \(OCSP\) is not enabled by default when you choose to encrypt data in transit\. Amazon EFS uses an [Amazon certificate authority](https://www.amazontrust.com) \(CA\) to issue and sign its TLS certificates, and the CA instructs the client to use OCSP to check for revoked certificates\. The OCSP endpoint must be accessible over the Internet from your Virtual Private Cloud in order to check a certificate's status\. Within the service, EFS continuously monitors certificate status, and issues new certificates to replace any revoked certificates it detects\. 

 In order to provide the strongest security possible, you can enable OCSP so that your Linux clients can check for revoked certificates\. OCSP protects against malicious use of revoked certificates, which is unlikely to within your VPC\. In the event that an EFS TLS certificate is revoked, Amazon will publish a security bulletin and release a new version of EFS mount helper that rejects the revoked certificate\. 

**To enable OCSP on your Linux client for all future TLS connections to EFS**

1. Open a terminal on your Linux client\.

1.  Using your text editor of choice, open the `/etc/amazon/efs/efs-utils.conf` file\. 

1.  Set the `stunnel_check_cert_validity` value to true\. 

1.  Save the changes to the file and close it\. 

**To enable OCSP as part of the `mount` command**
+  Use the following mount command to enable OCSP when mounting the file system\. 

  ```
         $ sudo mount -t efs -o tls,ocsp fs-12345678:/ /mnt/efs
  ```
