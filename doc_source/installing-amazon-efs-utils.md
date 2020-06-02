# Installing the amazon\-efs\-utils Package on Amazon Linux<a name="installing-amazon-efs-utils"></a>

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