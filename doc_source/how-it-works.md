# Amazon EFS: How it works<a name="how-it-works"></a>

Following, you can find a description about how Amazon EFS works, its implementation details, and security considerations\.

**Topics**
+ [Overview](#how-it-works-conceptual)
+ [How Amazon EFS works with Amazon EC2 and other supported compute instances](#how-it-works-ec2)
+ [How Amazon EFS works with AWS Direct Connect and AWS Managed VPN](#how-it-works-direct-connect)
+ [How Amazon EFS works with AWS Backup](#how-it-works-backups)
+ [Implementation summary](#how-it-works-implementation)
+ [Authentication and access control](#auth-access-intro)
+ [Data consistency in Amazon EFS](#consistency)
+ [Storage classes](#how-it-works-storage-classes)

## Overview<a name="how-it-works-conceptual"></a>



Amazon EFS provides a simple, serverless, set\-and\-forget elastic file system\. With Amazon EFS, you can create a file system, mount the file system on an Amazon EC2 instance, and then read and write data to and from your file system\. You can mount an Amazon EFS file system in your virtual private cloud \(VPC\), through the Network File System versions 4\.0 and 4\.1 \(NFSv4\) protocol\. We recommend using a current generation Linux NFSv4\.1 client, such as those found in the latest Amazon Linux, Amazon Linux 2, Red Hat, Ubuntu, and macOS Big Sur AMIs, in conjunction with the Amazon EFS mount helper\. For instructions, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\.

For a list of Amazon EC2 Linux and macOS Amazon Machine Images \(AMIs\) that support this protocol, see [NFS support](mounting-fs-old.md#mounting-fs-nfs-info)\. For some AMIs, you must install an NFS client to mount your file system on your Amazon EC2 instance\. For instructions, see [Installing the NFS client](mounting-fs-old.md#mounting-fs-install-nfsclient)\.

 You can access your Amazon EFS file system concurrently from multiple NFS clients, so applications that scale beyond a single connection can access a file system\. Amazon EC2 and other AWS compute instances running in multiple Availability Zones within the same AWS Region can access the file system, so that many users can access and share a common data source\.

For a list of AWS Regions where you can create an Amazon EFS file system, see the [Amazon Web Services General Reference](https://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem_region)\. 

To access your Amazon EFS file system in a VPC, you create one or more mount targets in the VPC\.
+ For file systems using Standard storage classes, you can create a mount target in each availability Zone in the AWS Region\.
+ For file systems using One Zone storage classes, you create only a single mount target that is in the same Availability Zone as the file system\. 

For more information, see [EFS storage classes](storage-classes.md)\.

A *mount target* provides an IP address for an NFSv4 endpoint at which you can mount an Amazon EFS file system\. You mount your file system using its Domain Name Service \(DNS\) name, which resolves to the IP address of the EFS mount target in the same Availability Zone as your EC2 instance\. You can create one mount target in each Availability Zone in an AWS Region\. If there are multiple subnets in an Availability Zone in your VPC, you create a mount target in one of the subnets\. Then all EC2 instances in that Availability Zone share that mount target\.

**Note**  
An Amazon EFS file system can only have mount targets in one VPC at a time\.

Mount targets themselves are designed to be highly available\. As you design for high availability and failover to other Availability Zones, keep in mind that while the IP addresses and DNS for your mount targets in each Availability Zone are static, they are redundant components backed by multiple resources\.

After mounting the file system by using its DNS name, you use it like any other POSIX\-compliant file system\. For information about NFS\-level permissions and related considerations, see [Working with users, groups, and permissions at the Network File System \(NFS\) Level](accessing-fs-nfs-permissions.md)\. 

You can mount your Amazon EFS file systems on your on\-premises data center servers when connected to your Amazon VPC with AWS Direct Connect or AWS VPN You can mount your EFS file systems on on\-premises servers to migrate datasets to EFS, enable cloud bursting scenarios, or backup your on\-premises data to Amazon EFS\.

## How Amazon EFS works with Amazon EC2 and other supported compute instances<a name="how-it-works-ec2"></a>

This section explains how Amazon EFS file systems that use Standard and One Zone storage classes are mounted to EC2 instances in an Amazon VPC\. 

### Amazon EFS with Standard storage classes<a name="efs-regional-ec2"></a>

The following illustration shows multiple EC2 instances accessing an Amazon EFS file system that is configured with Standard storage classes from multiple Availability Zones in an AWS Region\.

![\[Diagram showing 3 Availability Zones in a VPC, containing EC2 instances and mount targets, and a mounted EFS file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/efs-ec2-how-it-works-Regional.png)

In this illustration, the Amazon Virtual Private Cloud \(VPC\) has three Availability Zones\. Because the file system uses Standard storage classes, a mount target was created in each Availability Zone\. We recommend that you access the file system from a mount target within the same Availability Zone for performance and cost reasons\. One of the Availability Zones has two subnets\. However, a mount target is created in only one of the subnets\. Creating this setup works as follows:

1. Create your Amazon EC2 resources and launch your Amazon EC2 instance\. For more information on Amazon EC2, see [Amazon EC2 \- Virtual Server Hosting](https://aws.amazon.com/ec2/)\.

1. Choose **Regional** durability and availability when creating your Amazon EFS file system\.

1. Connect to each of your Amazon EC2 instances, and mount the Amazon EFS file system\.

For detailed steps, see [Getting started with Amazon Elastic File System](getting-started.md)\.

### Amazon EFS with One Zone storage classes<a name="efs-onezone-ec2"></a>

The following illustration shows multiple EC2 instances that are accessing an Amazon EFS file system\. This file system is configured with One Zone storage from multiple Availability Zones in an AWS Region\.

![\[Diagram showing 2 Availability Zones in a VPC, containing EC2 instances, only one mount target, and a mounted EFS One Zone file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/efs-ec2-how-it-works-OneZone.png)

In this illustration, the VPC has two Availability Zones, each with one subnet\. The file system uses One Zone storage classes, so it can only have a single mount target\. For better performance and cost, we recommend that you access the file system from a mount target in the same Availability Zone as the EC2 instance that you're mounting it on\.

In this example, the EC2 instance in the us\-west\-2c Availability Zone will pay EC2 data access charges for accessing a mount target in a different Availability Zone\. Creating this setup works as follows:

1. Create your Amazon EC2 resources and launch your Amazon EC2 instance\. For more information about Amazon EC2, see [Amazon EC2](https://aws.amazon.com/ec2/)\.

1. Create your Amazon EFS file system with One Zone storage\.

1. Connect to each of your Amazon EC2 instances, and mount the Amazon EFS file system using the same mount target for each instance\.

## How Amazon EFS works with AWS Direct Connect and AWS Managed VPN<a name="how-it-works-direct-connect"></a>

By using an Amazon EFS file system mounted on an on\-premises server, you can migrate on\-premises data into the AWS Cloud hosted in an Amazon EFS file system\. You can also take advantage of bursting\. In other words, you can move data from your on\-premises servers into Amazon EFS and analyze it on a fleet of Amazon EC2 instances in your Amazon VPC\. You can then store the results permanently in your file system or move the results back to your on\-premises server\.

Keep the following considerations in mind when using Amazon EFS with an on\-premises server:
+ Your on\-premises server must have a Linux\-based operating system\. We recommend Linux kernel version 4\.0 or later\.
+ For the sake of simplicity, we recommend mounting an Amazon EFS file system on an on\-premises server using a mount target IP address instead of a DNS name\.

There is no additional cost for on\-premises access to your Amazon EFS file systems\. You are charged for the AWS Direct Connect connection to your Amazon VPC\. For more information, see [AWS Direct Connect pricing](https://aws.amazon.com/directconnect/pricing/)\.

The following illustration shows an example of how to access an Amazon EFS file system from on\-premises \(the on\-premises servers have the file systems mounted\)\.

![\[Diagram showing Amazon EFS works with AWS Direct Connect to mount an EFS file system on an on-premises server.\]](http://docs.aws.amazon.com/efs/latest/ug/images/efs-directconnect-how-it-works.png)

You can use any mount target in your VPC if you can reach that mount target's subnet by using an AWS Direct Connect connection between your on\-premises server and VPC\. To access Amazon EFS from an on\-premises server, add a rule to your mount target security group to allow inbound traffic to the NFS port \(2049\) from your on\-premises server\.

To create a setup like this, you do the following:

1. Establish an AWS Direct Connect connection between your on\-premises data center and your Amazon VPC\. For more information on AWS Direct Connect, see [AWS Direct Connect](https://aws.amazon.com/directconnect)\.

1. Create your Amazon EFS file system\.

1. Mount the Amazon EFS file system on your on\-premises server\.

For detailed steps, see [Walkthrough: Create and mount a file system on\-premises with AWS Direct Connect and VPN](efs-onpremises.md)\.

## How Amazon EFS works with AWS Backup<a name="how-it-works-backups"></a>

For a comprehensive backup implementation for your file systems, you can use Amazon EFS with AWS Backup\. AWS Backup is a fully managed backup service that makes it easy to centralize and automate data backup across AWS services in the cloud and on\-premises\. Using AWS Backup, you can centrally configure backup policies and monitor backup activity for your AWS resources\. Amazon EFS always prioritizes file system operations over backup operations\. To learn more about backing up EFS file systems using AWS Backup, see [Using AWS Backup to back up and restore Amazon EFS file systems](awsbackup.md)\.

## Implementation summary<a name="how-it-works-implementation"></a>

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

  For more information, see [Creating and managing mount targets](accessing-fs.md)\. First, you need to install the NFS client on your EC2 instance\. The [Getting started](getting-started.md) exercise provides step\-by\-step instructions\.
+ **Access Points** – An access point applies an operating system user, group, and file system path to any file system request made using the access point\. The access point's operating system user and group override any identity information provided by the NFS client\. The file system path is exposed to the client as the access point's root directory\. This ensures that each application always uses the correct operating system identity and the correct directory when accessing shared file\-based datasets\. Applications using the access point can only access data in its own directory and below\. For more information, see [Working with Amazon EFS Access Points](efs-access-points.md)\.

Mount targets and tags are *subresources* that are associated with a file system\. You can only create them within the context of an existing file system\. 

Amazon EFS provides API operations for you to create and manage these resources\. In addition to the create and delete operations for each resource, Amazon EFS also supports a describe operation that enables you to retrieve resource information\. You have the following options for creating and managing these resources:
+ Use the Amazon EFS console – For an example, see [Getting started](getting-started.md)\.
+ Use the Amazon EFS command line interface \(CLI\) – For an example, see [Walkthrough: Create an Amazon EFS file system and mount it on an Amazon EC2 instance using the AWS CLI](wt1-getting-started.md)\.
+ You can also manage these resources programmatically as follows:
  + Use the AWS SDKs – The AWS SDKs simplify your programming tasks by wrapping the underlying Amazon EFS API\. The SDK clients also authenticate your requests by using access keys that you provide\. For more information, see [Sample Code and Libraries](https://aws.amazon.com/code)\.
  + Call the Amazon EFS API directly from your application – If you cannot use the SDKs for some reason, you can make the Amazon EFS API calls directly from your application\. However, you need to write the necessary code to authenticate your requests if you use this option\. For more information about the Amazon EFS API, see [Amazon EFS API](api-reference.md)\.

## Authentication and access control<a name="auth-access-intro"></a>

You must have valid credentials to make Amazon EFS API requests, such as create a file system\. In addition, you must also have permissions to create or access resources\. By default, when you use the root account credentials of your AWS account you can create and access resources owned by that account\. However, we don't recommend using root account credentials\. In addition, any AWS Identity and Access Management \(IAM\) users and roles that you create in your account must be granted permissions to create or access resources\. For more information about permissions, see [Identity and access management for Amazon EFS](auth-and-access-control.md)\.

IAM authorization for NFS clients is an additional security option for Amazon EFS that uses IAM to simplify access management for Network File System \(NFS\) clients at scale\. With IAM authorization for NFS clients, you can use IAM to manage access to an EFS file system in an inherently scalable way\. IAM authorization for NFS clients is also optimized for cloud environments\. For more information on using IAM authorization for NFS clients, see [Using IAM to control file system data access](iam-access-control-nfs-efs.md)\.

## Data consistency in Amazon EFS<a name="consistency"></a>

Amazon EFS provides the close\-to\-open consistency semantics that applications expect from NFS\.

 In Amazon EFS, write operations are durably stored across Availability Zones on file systems using Standard storage classes in these situations:
+ An application performs a synchronous write operation \(for example, using the `open` Linux command with the `O_DIRECT` flag, or the `fsync` Linux command\)\.
+ An application closes a file\.

Depending on the access pattern, Amazon EFS can provide stronger consistency guarantees than close\-to\-open semantics\. Applications that perform synchronous data access and perform non\-appending writes have read\-after\-write consistency for data access\.

## Storage classes<a name="how-it-works-storage-classes"></a>

With Amazon EFS, you can choose from a range of storage classes that are designed for different use cases:
+ **EFS Standard** – A regional storage class for frequently accessed data\. It offers the highest levels of availability and durability by storing file system data redundantly across multiple Availability Zones in an AWS Region\.
+  **EFS Standard\-Infrequent Access \(Standard\-IA\)** – A regional storage class for infrequently accessed data\. It offers the highest levels of availability and durability by storing file system data redundantly across multiple Availability Zones in an AWS Region\. 
+  **EFS One Zone** – For frequently accessed files stored redundantly within a single Availability Zone in an AWS Region\. 
+ **EFS One Zone\-IA \(One Zone\-IA\)** – A lower\-cost storage class for infrequently accessed files stored redundantly within a single Availability Zone in an AWS Region\.

The EFS Standard storage classes are regional storage classes that store file system data and metadata redundantly across multiple geographically separated Availability Zones within an AWS Region\. They offer the highest levels of availability and durability, providing continuous availability to data even when one or more Availability Zones in a Region are unavailable\.

The EFS One Zone storage classes are lower cost, single Availability Zone storage classes\. They store file system data and metadata redundantly in a single Availability Zone within an AWS Region\.

Both of the IA storage classes reduce storage costs for files that aren't accessed every day\. We recommend using IA storage if you need your full dataset to be readily accessible, and you want to automatically save on storage costs for files that are less frequently accessed\. Examples include keeping files accessible to satisfy audit requirements, perform historical analysis, or perform backup and recovery\. For more information about Amazon EFS storage classes, see [EFS storage classes](storage-classes.md)\. 

### EFS lifecycle management<a name="storage-classes-lifecycle-mgnt"></a>

Amazon EFS lifecycle management automatically manages cost\-effective file storage for your file systems\. When enabled, lifecycle management migrates files that haven't been accessed for a set period of time to an infrequent access storage class, Standard\-IA or One Zone\-IA\. You define that period of time by using a *lifecycle policy*\. For more information, see [Amazon EFS lifecycle management](lifecycle-management-efs.md)\.

### EFS Intelligent‐Tiering<a name="lifecycle-mgmt-int-tiering"></a>

Amazon EFS Intelligent‐Tiering uses lifecycle management to monitor the access patterns of your workload and is designed to automatically transition files to and from your corresponding Infrequent Access \(IA\) storage class\. With intelligent tiering, files in the standard storage class \(EFS Standard or EFS One Zone\) that are not accessed for a period of time, for example 30 days, are transitioned to the corresponding Infrequent Access \(IA\) storage class\. Additionally, if access patterns change, EFS Intelligent‐Tiering automatically moves files back to the EFS Standard or EFS One Zone storage classes\. This helps to eliminate the risk of unbounded access charges, while providing consistent low latencies\. For more information, see [Amazon EFS Intelligent\-Tiering](lifecycle-management-efs.md#intelligent-tiering-efs)\.