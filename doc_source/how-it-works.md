# Amazon EFS: How It Works<a name="how-it-works"></a>

Following, you can find a description about how Amazon EFS works, its implementation details, and security considerations\.

**Topics**
+ [Overview](#how-it-works-conceptual)
+ [How Amazon EFS Works with Amazon EC2](#how-it-works-ec2)
+ [How Amazon EFS Works with AWS Direct Connect and AWS Managed VPN](#how-it-works-direct-connect)
+ [How Amazon EFS Works with AWS Backup](#how-it-works-backups)
+ [Implementation Summary](#how-it-works-implementation)
+ [Authentication and Access Control](#auth-access-intro)
+ [Data Consistency in Amazon EFS](#consistency)
+ [Storage Classes and Lifecycle Management](#how-it-works-storage-classes)

## Overview<a name="how-it-works-conceptual"></a>

Amazon EFS provides file storage in the AWS Cloud\. With Amazon EFS, you can create a file system, mount the file system on an Amazon EC2 instance, and then read and write data to and from your file system\. You can mount an Amazon EFS file system in your VPC, through the Network File System versions 4\.0 and 4\.1 \(NFSv4\) protocol\. We recommend using a current generation Linux NFSv4\.1 client, such as those found in the latest Amazon Linux, Redhat, and Ubuntu AMIs, in conjunction with the Amazon EFS Mount Helper\. For instructions, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\.

For a list of Amazon EC2 Linux Amazon Machine Images \(AMIs\) that support this protocol, see [NFS Support](mounting-fs-old.md#mounting-fs-nfs-info)\. For some AMIs, you'll need to install an NFS client to mount your file system on your Amazon EC2 instance\. For instructions, see [Installing the NFS Client](mounting-fs-old.md#mounting-fs-install-nfsclient)\.

 You can access your Amazon EFS file system concurrently from multiple NFS clients, so applications that scale beyond a single connection can access a file system\. Amazon EC2 instances running in multiple Availability Zones within the same AWS Region can access the file system, so that many users can access and share a common data source\.

For a list of AWS Regions where you can create an Amazon EFS file system, see the [Amazon Web Services General Reference](https://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem_region)\. 

To access your Amazon EFS file system in a VPC, you create one or more mount targets in the VPC\. A *mount target* provides an IP address for an NFSv4 endpoint at which you can mount an Amazon EFS file system\. You mount your file system using its Domain Name Service \(DNS\) name, which resolves to the IP address of the EFS mount target in the same Availability Zone as your EC2 instance\. You can create one mount target in each Availability Zone in an AWS Region\. If there are multiple subnets in an Availability Zone in your VPC, you create a mount target in one of the subnets\. Then all EC2 instances in that Availability Zone share that mount target\.

**Note**  
An Amazon EFS file system can only have mount targets in one VPC at a time\.

Mount targets themselves are designed to be highly available\. As you design for high availability and failover to other Availability Zones \(AZs\), keep in mind that while the IP addresses and DNS for your mount targets in each AZ are static, they are redundant components backed by multiple resources\.

After mounting the file system by using its DNS name, you use it like any other POSIX\-compliant file system\. For information about NFS\-level permissions and related considerations, see [Working with Users, Groups, and Permissions at the Network File System \(NFS\) Level](accessing-fs-nfs-permissions.md)\. 

You can mount your Amazon EFS file systems on your on\-premises data center servers when connected to your Amazon VPC with AWS Direct Connect or AWS VPN You can mount your EFS file systems on on\-premises servers to migrate datasets to EFS, enable cloud bursting scenarios, or backup your on\-premises data to EFS\. 

## How Amazon EFS Works with Amazon EC2<a name="how-it-works-ec2"></a>

The following illustration shows an example VPC accessing an Amazon EFS file system\. Here, EC2 instances in the VPC have file systems mounted\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/overview-flow.png)

In this illustration, the VPC has three Availability Zones, and each has one mount target created in it\. We recommend that you access the file system from a mount target within the same Availability Zone\. One of the Availability Zones has two subnets\. However, a mount target is created in only one of the subnets\. Creating this setup works as follows:

1. Create your Amazon EC2 resources and launch your Amazon EC2 instance\. For more information on Amazon EC2, see [Amazon EC2 \- Virtual Server Hosting](https://aws.amazon.com/ec2/)\.

1. Create your Amazon EFS file system\.

1. Connect to your Amazon EC2 instance, and mount the Amazon EFS file system\.

For detailed steps, see [Getting Started with Amazon Elastic File System](getting-started.md)\.

## How Amazon EFS Works with AWS Direct Connect and AWS Managed VPN<a name="how-it-works-direct-connect"></a>

By using an Amazon EFS file system mounted on an on\-premises server, you can migrate on\-premises data into the AWS Cloud hosted in an Amazon EFS file system\. You can also take advantage of bursting\. In other words, you can move data from your on\-premises servers into Amazon EFS and analyze it on a fleet of Amazon EC2 instances in your Amazon VPC\. You can then store the results permanently in your file system or move the results back to your on\-premises server\.

Keep the following considerations in mind when using Amazon EFS with an on\-premises server:
+ Your on\-premises server must have a Linux\-based operating system\. We recommend Linux kernel version 4\.0 or later\.
+ For the sake of simplicity, we recommend mounting an Amazon EFS file system on an on\-premises server using a mount target IP address instead of a DNS name\.

There is no additional cost for on\-premises access to your Amazon EFS file systems\. You are charged for the AWS Direct Connect connection to your Amazon VPC\. For more information, see [AWS Direct Connect Pricing](https://aws.amazon.com/directconnect/pricing/)\.

The following illustration shows an example of how to access an Amazon EFS file system from on\-premises \(the on\-premises servers have the file systems mounted\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/onprem-overview-flow.png)

You can use any mount target in your VPC if you can reach that mount target's subnet by using an AWS Direct Connect connection between your on\-premises server and VPC\. To access Amazon EFS from an on\-premises server, add a rule to your mount target security group to allow inbound traffic to the NFS port \(2049\) from your on\-premises server\.

To create a setup like this, you do the following:

1. Establish an AWS Direct Connect connection between your on\-premises data center and your Amazon VPC\. For more information on AWS Direct Connect, see [AWS Direct Connect](https://aws.amazon.com/directconnect)\.

1. Create your Amazon EFS file system\.

1. Mount the Amazon EFS file system on your on\-premises server\.

For detailed steps, see [Walkthrough: Create and Mount a File System On\-Premises with AWS Direct Connect and VPN](efs-onpremises.md)\.

## How Amazon EFS Works with AWS Backup<a name="how-it-works-backups"></a>

For a comprehensive backup implementation for your file systems, you can use Amazon EFS with AWS Backup\. AWS Backup is a fully managed backup service that makes it easy to centralize and automate data backup across AWS services in the cloud and on\-premises\. Using AWS Backup, you can centrally configure backup policies and monitor backup activity for your AWS resources\. Amazon EFS always prioritizes file system operations over backup operations\. To learn more about backing up EFS file systems using AWS Backup, see [Using AWS Backup with Amazon EFS](awsbackup.md)\.

## Implementation Summary<a name="how-it-works-implementation"></a>

In Amazon EFS, a file system is the primary resource\. Each file system has properties such as ID, creation token, creation time, file system size in bytes, number of mount targets created for the file system, and the file system lifecycle state\. For more information, see [CreateFileSystem](API_CreateFileSystem.md)\.

Amazon EFS also supports other resources to configure the primary resource\. These include mount targets and access points:
+ **Mount target** – To access your file system, you must create mount targets in your VPC\. Each mount target has the following properties: the mount target ID, the subnet ID in which it is created, the file system ID for which it is created, an IP address at which the file system may be mounted, VPC security groups, and the mount target state\. You can use the IP address or the DNS name in your `mount` command\. 

  Each file system has a DNS name of the following form\.

  ```
  file-system-id.efs.aws-region.amazonaws.com 
  ```

  You can specify this DNS name in your `mount` command to mount the Amazon EFS file system\. Suppose you create an `efs-mount-point` subdirectory off of your home directory on your EC2 instance or on\-premises server\. Then, you can use the mount command to mount the file system\. For example, on an Amazon Linux AMI, you can use the following `mount` command\.

  ```
  $ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport file-system-DNS-name:/ ~/efs-mount-point 
  ```

  For more information, see [Creating mount targets](accessing-fs.md)\. First, you need to install the NFS client on your EC2 instance\. The [Getting Started](getting-started.md) exercise provides step\-by\-step instructions\.
+ **Access Points** – An access point applies an operating system user, group, and file system path to any file system request made using the access point\. The access point's operating system user and group override any identity information provided by the NFS client\. The file system path is exposed to the client as the access point's root directory\. This ensures that each application always uses the correct operating system identity and the correct directory when accessing shared file\-based datasets\. Applications using the access point can only access data in its own directory and below\. For more information, see [Working with Amazon EFS Access Points](efs-access-points.md)\.

Mount targets and tags are *subresources* that are associated with a file system\. You can only create them within the context of an existing file system\. 

Amazon EFS provides API operations for you to create and manage these resources\. In addition to the create and delete operations for each resource, Amazon EFS also supports a describe operation that enables you to retrieve resource information\. You have the following options for creating and managing these resources:
+ Use the Amazon EFS console – For an example, see [Getting Started](getting-started.md)\.
+ Use the Amazon EFS command line interface \(CLI\) – For an example, see [Walkthrough: Create an Amazon EFS File System and Mount It on an Amazon EC2 Instance Using the AWS CLI](wt1-getting-started.md)\.
+ You can also manage these resources programmatically as follows:
  + Use the AWS SDKs – The AWS SDKs simplify your programming tasks by wrapping the underlying Amazon EFS API\. The SDK clients also authenticate your requests by using access keys that you provide\. For more information, see [Sample Code and Libraries](https://aws.amazon.com/code)\.
  + Call the Amazon EFS API directly from your application – If you cannot use the SDKs for some reason, you can make the Amazon EFS API calls directly from your application\. However, you need to write the necessary code to authenticate your requests if you use this option\. For more information about the Amazon EFS API, see [Amazon EFS API](api-reference.md)\.

## Authentication and Access Control<a name="auth-access-intro"></a>

You must have valid credentials to make Amazon EFS API requests, such as create a file system\. In addition, you must also have permissions to create or access resources\. By default, when you use the root account credentials of your AWS account you can create and access resources owned by that account\. However, we don't recommend using root account credentials\. In addition, any AWS Identity and Access Management \(IAM\) users and roles that you create in your account must be granted permissions to create or access resources\. For more information about permissions, see [Identity and Access Management for Amazon EFS](auth-and-access-control.md)\.

IAM authorization for NFS clients is an additional security option for Amazon EFS that uses IAM to simplify access management for Network File System \(NFS\) clients at scale\. With IAM authorization for NFS clients, you can use IAM to manage access to an EFS file system in an inherently scalable way\. IAM authorization for NFS clients is also optimized for cloud environments\. For more information on using IAM authorization for NFS clients, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\.

## Data Consistency in Amazon EFS<a name="consistency"></a>

Amazon EFS provides the close\-to\-open consistency semantics that applications expect from NFS\.

 In Amazon EFS, write operations are durably stored across Availability Zones in these situations:
+ An application performs a synchronous write operation \(for example, using the `open` Linux command with the `O_DIRECT` flag, or the `fsync` Linux command\)\.
+ An application closes a file\.

Depending on the access pattern, Amazon EFS can provide stronger consistency guarantees than close\-to\-open semantics\. Applications that perform synchronous data access and perform nonappending writes have read\-after\-write consistency for data access\.

## Storage Classes and Lifecycle Management<a name="how-it-works-storage-classes"></a>

With Amazon EFS, you can use two storage classes for your file systems:
+  **Infrequent Access** – The Infrequent Access \(IA\) storage class is a lower\-cost storage class that's designed for storing long\-lived, infrequently accessed files cost\-effectively\. 
+  **Standard** – The Standard storage class is used to store frequently accessed files\. 

The EFS IA storage class reduces storage costs for files that aren't accessed every day\. It does this without sacrificing the high availability, high durability, elasticity, and POSIX file system access that EFS provides\. We recommend EFS IA storage if you need your full dataset to be readily accessible and want to automatically save on storage costs for files that are less frequently accessed\. Examples include keeping files accessible to satisfy audit requirements, perform historical analysis, or perform backup and recovery\. To learn more about EFS storage classes, see [EFS storage classes](storage-classes.md)\. 

Amazon EFS lifecycle management automatically manages cost\-effective file storage for your file systems\. When enabled, lifecycle management migrates files that haven't been accessed for a set period of time to the Infrequent Access \(IA\) storage class\. You define that period of time by using a *lifecycle policy\. * To learn more about lifecycle management, see [EFS lifecycle management](lifecycle-management-efs.md)\. 