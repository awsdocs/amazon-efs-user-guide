# Working with Amazon EFS resources<a name="creating-using"></a>

Amazon EFS provides elastic, shared file storage that is POSIX\-compliant\. The file system you create supports concurrent read and write access from multiple Amazon EC2 instances and is accessible from all of the Availability Zones in the AWS Region where it is created\.

You can mount an Amazon EFS file system on EC2 instances in your virtual private cloud \(VPC\) based on Amazon VPC using the Network File System versions 4\.0 and 4\.1 protocol \(NFSv4\)\. For more information, see [Amazon EFS: How It Works](how-it-works.md)\.

As an example, suppose that you have one or more EC2 instances launched in your VPC\. Now you want to create and use a file system on these instances\. Following are the typical steps you need to perform to use Amazon EFS file systems in the VPC:
+ **Create an Amazon EFS file system** – When creating a file system, we recommend that you consider using the **Name** tag because the **Name** tag value appears in the console and makes it easier to identify the file system\. You can also add other optional tags to the file system\. 
+ **Create mount targets for the file system** – To access the file system in your VPC and mount the file system to your Amazon EC2 instance, you must create mount targets in the VPC subnets\.
+ **Create security groups** – Both an Amazon EC2 instance and a mount target need to have associated security groups\. These security groups act as a virtual firewall that controls the traffic between them\. You can use the security group you associated with the mount target to control inbound traffic to your file system by adding an inbound rule to the mount target security group that allows access from a specific EC2 instance\. Then, you can mount the file system only on that EC2 instance\.

If you are new to Amazon EFS, we recommend that you try the following exercises that provide a first\-hand, end\-to\-end experience of using an Amazon EFS file system:
+ [Getting Started](getting-started.md) – The Getting Started exercise provides a console\-based end\-to\-end setup in which you create a file system, mount it on an EC2 instance, and test the setup\. The console takes care of many things for you and helps you set up the end\-to\-end experience quickly\.
+ [Walkthrough: Create an Amazon EFS File System and Mount It on an Amazon EC2 Instance Using the AWS CLI](wt1-getting-started.md) – The walkthrough is similar to the Getting Started exercise, but it uses the AWS Command Line Interface \(AWS CLI\) to perform most of the tasks\. Because the AWS CLI commands closely map to the Amazon EFS API, the walkthrough can help you familiarize yourself with the Amazon EFS API operations\. 

For more information about creating EFS resources and accessing a file system, see the following topics\.

**Topics**
+ [Creating Amazon EFS file systems](creating-using-create-fs.md)
+ [Deleting an Amazon EFS file system](delete-efs-fs.md)
+ [Creating mount targets](accessing-fs.md)
+ [Creating security groups](accessing-fs-create-security-groups.md)
+ [Creating file system policies](create-file-system-policy.md)
+ [Creating and deleting access points](create-access-point.md)