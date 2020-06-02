# Installing the amazon\-efs\-utils Package on Other Linux Distributions<a name="installing-other-distro"></a>

If you don't want to get the amazon\-efs\-utils package from Amazon Linux or Amazon Linux 2 AMIs, the amazon\-efs\-utils package is also available on GitHub\.

After you clone the package, you can build and install amazon\-efs\-utils using one of the following methods, depending on the package type supported by your Linux distribution:
+ **RPM** – This package type is supported by Amazon Linux, Red Hat Linux, CentOS, and similar\.
+ **DEB** – This package type is supported by Ubuntu, Debian, and similar\.

**To clone amazon\-efs\-utils from GitHub**

1. Make sure that you've created an Amazon EC2 instance of the supported AMI type\. For more information on how to do this, see [Step 1: Launch an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Access the terminal for your instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. If you haven't done so already, install git with the following commands\.

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