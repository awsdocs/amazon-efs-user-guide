# Manually installing the Amazon EFS client<a name="installing-amazon-efs-utils"></a>

You can manually install the Amazon EFS client on your Amazon EC2 Linux instances running Amazon Linux and Amazon Linux 2, and EC2 Mac instances running macOS Big Sur, and other supported Linux distributions\. The installation procedures are described in the following sections\.

**Topics**
+ [Installing the Amazon EFS client on Amazon Linux and Amazon Linux 2](#installing-efs-utils-amzn-linux)
+ [Installing the Amazon EFS client on other Linux distributions](#installing-other-distro)
+ [Installing the Amazon EFS client on EC2 Mac instances running macOS Big Sur](#install-efs-utils-macBigSur)

## Installing the Amazon EFS client on Amazon Linux and Amazon Linux 2<a name="installing-efs-utils-amzn-linux"></a>

The `amazon-efs-utils` package is available for installation in Amazon Linux and Amazon Linux 2 EC2 instances\. To install the Amazon EFS client on other Linux distributions, see [Installing the Amazon EFS client on other Linux distributions](#installing-other-distro)\.

**Note**  
If you're using AWS Direct Connect, you can find installation instructions in [Walkthrough: Create and Mount a File System On\-Premises with AWS Direct Connect and VPN](efs-onpremises.md)\.

**To install the amazon\-efs\-utils package**

1. Make sure that you've created an Amazon Linux or Amazon Linux 2 EC2 instance\. For information on how to do this, see [Step 1: Launch an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the* Amazon EC2 User Guide for Linux Instances\.*

1. Access the terminal for your instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to your Linux instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Run the following command to install amazon\-efs\-utils\.

   ```
   sudo yum install -y amazon-efs-utils
   ```

## Installing the Amazon EFS client on other Linux distributions<a name="installing-other-distro"></a>

If you don't want to get the `amazon-efs-utils` package from Amazon Linux or Amazon Linux 2 AMIs, the amazon\-efs\-utils package is also available on GitHub\.

After you clone the package, you can build and install amazon\-efs\-utils using one of the following methods, depending on the package type supported by your Linux distribution:
+ **RPM** – This package type is supported by Amazon Linux, Red Hat Linux, CentOS, and similar\.
+ **DEB** – This package type is supported by Ubuntu, Debian, and similar\.

### To build and install `amazon-efs-utils` as an RPM package<a name="build-efs-utils-rpm"></a>

1. Connect to the EC2 instance using Secure Shell \(SSH\), and log in with the appropriate user name\. For more information, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Install `git` using the following command:

   ```
   sudo yum -y install git
   ```

1. Install the `rpm-build` package if it's not already installed using the following command:

   ```
   sudo yum -y install rpm-build
   ```

1. Clone `amazon-efs-utils` from GitHub using the following command\.

   ```
   git clone https://github.com/aws/efs-utils
   ```

1. Open a terminal on your client and navigate to the directory that contains the `amazon-efs-utils` package\.

   ```
   cd /path/efs-utils
   ```

1. Install the bash `make` command if your operating system doesn't already have it as follows\.

   ```
   sudo yum -y install make
   ```

1. Install the `rpm-build` package if it's not already installed using the following command:

   ```
   sudo yum -y install rpm-build
   ```

1. Build the `amazon-efs-utils` package using the following command:

   ```
   sudo make rpm
   ```

1. Install the `amazon-efs-utils` package with the following command\.

   ```
   sudo yum -y install ./build/amazon-efs-utils*rpm
   ```

### To build and install amazon\-efs\-utils as a Debian package<a name="build-efs-utils-deb"></a>

1. Connect to the EC2 instance using Secure Shell \(SSH\), and log in with the appropriate user name\. For more information, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. \(Optional\) Apply updates before installing the package with the following command:

   ```
   sudo apt-get update
   ```

   Install updates as needed\.

1. Install `git` and `binutils`, using the following command\. `binutils` is required for building DEB packages,

   ```
   sudo apt-get -y install git binutils
   ```

1. Clone `amazon-efs-utils` from GitHub using the following command\.

   ```
   git clone https://github.com/aws/efs-utils
   ```

1. Navigate to the directory that contains the `amazon-efs-utils` package\.

   ```
   cd /path/efs-utils
   ```

1. Build `amazon-efs-utils` using the following command:

   ```
   ./build-deb.sh
   ```

1. Install the package with the following command\.

   ```
   sudo apt-get -y install ./build/amazon-efs-utils*deb
   ```

## Installing the Amazon EFS client on EC2 Mac instances running macOS Big Sur<a name="install-efs-utils-macBigSur"></a>

The `amazon-efs-utils` package is available for installation on EC2 Mac instances running macOS Big Sur\.

**To install the amazon\-efs\-utils package**

1. Make sure that you've created an EC2 Mac instance running macOS Big Sur \. For information on how to do this, see [Step 1: Launch an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-mac-instances.html) in the *Amazon EC2 User Guide for Mac Instances*\.

1. Access the terminal for your instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to your instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-mac-instances.html#mac-instance-ssh) in the *Amazon EC2 User Guide for Mac Instances*\.

1. Run the following command to install `amazon-efs-utils`\.

   ```
   brew install amazon-efs-utils
   ```

   The system responds with instructions to follow to complete the installation\.

   ```
   Perform below actions to start using efs:
         sudo mkdir -p /Library/Filesystems/efs.fs/Contents/Resources
         sudo ln -s /usr/local/bin/mount.efs /Library/Filesystems/efs.fs/Contents/Resources/mount_efs
         
    To enable watchdog for using TLS mounts:
         sudo cp /usr/local/Cellar/amazon-efs-utils/<version>/libexec/amazon-efs-mount-watchdog.plist /Library/LaunchAgents
         sudo launchctl load /Library/LaunchAgents/amazon-efs-mount-watchdog.plist
   ```

1. In order mount an EFS file system, you need to ensure that the EFS mount helper in `amazon-efs-utils` is accessible by the mount command\. To do so, run following commands:

   ```
   sudo mkdir -p /Library/Filesystems/efs.fs/Contents/Resources
   sudo ln -s /usr/local/bin/mount.efs /Library/Filesystems/efs.fs/Contents/Resources/mount_efs
   ```

1. Run the following commands to enable the watchdog process \(`amazon-efs-mount-watchdog`\) that monitors the health of TLS mounts on your EFS file system\.

   ```
   sudo cp /usr/local/Cellar/amazon-efs-utils/<version>/libexec/amazon-efs-mount-watchdog.plist /Library/LaunchAgents
   sudo launchctl load /Library/LaunchAgents/amazon-efs-mount-watchdog.plist
   ```