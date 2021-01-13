# Installing the amazon\-efs\-utils package on other Linux distributions<a name="installing-other-distro"></a>

If you don't want to get the `amazon-efs-utils` package from Amazon Linux or Amazon Linux 2 AMIs, the amazon\-efs\-utils package is also available on GitHub\.

After you clone the package, you can build and install amazon\-efs\-utils using one of the following methods, depending on the package type supported by your Linux distribution:
+ **RPM** – This package type is supported by Amazon Linux, Red Hat Linux, CentOS, and similar\.
+ **DEB** – This package type is supported by Ubuntu, Debian, and similar\.

## To build and install `amazon-efs-utils` as an RPM package<a name="build-efs-utils-rpm"></a>

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

## To build and install amazon\-efs\-utils as a Debian package<a name="build-efs-utils-deb"></a>

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