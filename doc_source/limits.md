# Amazon EFS Limits<a name="limits"></a>

Following, you can find out about limitations when working with Amazon EFS\.

**Topics**
+ [Amazon EFS Limits That You Can Increase](#soft-limits)
+ [Resource Limits](#limits-efs-resources-per-account-per-region)
+ [Limits for NFS Clients](#limits-client-specific)
+ [Limits for Amazon EFS File Systems](#limits-fs-specific)
+ [Unsupported NFSv4 Features](#nfs4-unsupported-features)
+ [Additional Considerations](#limits-additional-considerations)

## Amazon EFS Limits That You Can Increase<a name="soft-limits"></a>

You can increase the following Amazon EFS limit by contacting AWS Support\.


| Resource | Default Limit | 
| --- | --- | 
| Number of file systems for each customer account in an AWS Region | 1,000 | 

You can work with the following throughput limits by using the throughput mode that you choose for your file system, Bursting or Provisioned\. For more information on these different modes, see [Amazon EFS Performance](performance.md)\.


| Resource | Default Limit | 
| --- | --- | 
| Total bursting throughput for all connected clients |  US East \(Ohio\) Region – 3 GB/s US East \(N\. Virginia\) Region – 3 GB/s US West \(N\. California\) Region – 1 GB/s US West \(Oregon\) Region – 3 GB/s Asia Pacific \(Seoul\) – 1 GB/s Asia Pacific \(Singapore\) – 1 GB/s Asia Pacific \(Tokyo\) – 1 GB/s EU \(Frankfurt\) Region – 1 GB/s EU \(Ireland\) Region – 3 GB/s Asia Pacific \(Sydney\) Region – 3 GB/s  | 
| Total provisioned throughput for all connected clients |  All AWS Regions – 1 GB/s  | 

You can take the following steps to request an increase for these limits\. These increases are not granted immediately, so it might take a couple of days for your increase to become effective\.

**To request a limit increase**

1. Open the [AWS Support Center](https://console.aws.amazon.com/support/home#/) page, sign in, if necessary, and then choose **Create Case**\.

1. Under **Regarding**, choose **Service Limit Increase**\.

1. Under **Limit Type**, choose the type of limit to increase, fill in the necessary fields in the form, and then choose your preferred method of contact\.

## Resource Limits<a name="limits-efs-resources-per-account-per-region"></a>

Following are the limits on Amazon EFS resources for each customer account in an AWS Region\. 


| Resource | Limit | 
| --- | --- | 
| Number of mount targets for each file system in an Availability Zone | 1 | 
| Number of mount targets for each VPC | 400 | 
| Number of security groups for each mount target | 5 | 
| Number of tags for each file system | 50 | 
| Number of VPCs for each file system | 1 | 

## Limits for NFS Clients<a name="limits-client-specific"></a>

The following limits for NFS clients apply, assuming a Linux NFSv4\.1 client:
+ The maximum throughput you can drive for each NFS client is 250 MB/s\.
+ Up to 128 active user accounts for each client can have files open at the same time\. Each user account represents one local user logged in to the instance\. A user account that is logged in multiple times counts as one active user\.
+ Up to 32,768 files open at the same time on the instance\. Listing directory contents does not count as opening a file\.
+ Each unique mount on the client can acquire up to a total of 8,192 locks across a maximum of 256 unique file/process pairs\. For example, a single process can acquire one or more locks on 256 separate files, or 8 processes can each acquire one or more locks on 32 files\.
+ When connecting to EFS, NFS clients located on\-premises or in another AWS region can observe lower throughput than when connecting to EFS from the same AWS region due to increased network latency\. Network latency of 1 ms or less is required to achieve maximum per\-client throughput\. Use the AWS DataSync data migration service when migrating large data sets from on\-premises servers to Amazon EFS\.
+ When connecting to EFS, NFS clients located on\-premises or in another AWS region can observe lower throughput than when connecting to EFS from the same AWS region due to increased network latency\. Network latency of 1 ms or less is required to achieve maximum per\-client throughput\. Use the EFS File Sync data migration service when migrating large data sets from on\-premises servers to Amazon EFS\.
+ Using Amazon EFS with Microsoft Windows is not supported\.

## Limits for Amazon EFS File Systems<a name="limits-fs-specific"></a>

The following are limits specific to the Amazon EFS file systems:
+ Maximum name length: 255 bytes\.
+ Maximum symbolic link \(symlink\) length: 4080 bytes\.
+ Maximum number of hard links to a file: 177\.
+ Maximum size of a single file: 52,673,613,135,872 bytes \(47\.9 TiB\)\.
+ Maximum directory depth: 1000 levels deep\.
+ Any one particular file can have up to 512 locks across all instances connected and users accessing the file\.
+ In General Purpose mode, there is a limit of 7000 file system operations per second\. This operations limit is calculated for all clients connected to a single file system\.

## Unsupported NFSv4 Features<a name="nfs4-unsupported-features"></a>

Although Amazon Elastic File System does not support NFSv2, or NFSv3, Amazon EFS supports both NFSv4\.1 and NFSv4\.0, except for the following features:
+ pNFS
+ Client delegation or callbacks of any type
  + Operation OPEN always returns `OPEN_DELEGATE_NONE` as the delegation type\. 
  + The operation OPEN returns `NFSERR_NOTSUPP` for the `CLAIM_DELEGATE_CUR` and `CLAIM_DELEGATE_PREV` claim types\.
+ Mandatory locking

  All locks in Amazon EFS are advisory, which means that READ and WRITE operations do not check for conflicting locks before the operation is executed\. 
+ Deny share

  NFS supports the concept of a share deny, primarily used by Windows clients for users to deny others access to a particular file that has been opened\. Amazon EFS does not support this, and returns the NFS error `NFS4ERR_NOTSUPP` for any OPEN commands specifying a share deny value other than `OPEN4_SHARE_DENY_NONE`\. Linux NFS clients do not use anything other than `OPEN4_SHARE_DENY_NONE`\.
+ Access control lists \(ACL\)
+ Amazon EFS does not update the `time_access` attribute on file reads\. Amazon EFS updates `time_access` in the following events:
  + When a file is created \(an inode is created\)\.
  + When an NFS client makes an explicit `setattr` call\. 
  + On a write to the inode caused by, for example, file size changes or file metadata changes\.
  + Any inode attribute is updated\.
+ Namespaces
+ Persistent reply cache
+ Kerberos based security
+ NFSv4\.1 data retention
+ SetUID on directories
+ Unsupported file types when using the CREATE operation: Block devices \(NF4BLK\), character devices \(NF4CHR\), attribute directory \(NF4ATTRDIR\), and named attribute \(NF4NAMEDATTR\)\.
+ Unsupported attributes: FATTR4\_ARCHIVE, FATTR4\_FILES\_AVAIL, FATTR4\_FILES\_FREE, FATTR4\_FILES\_TOTAL, FATTR4\_FS\_LOCATIONS, FATTR4\_MIMETYPE, FATTR4\_QUOTA\_AVAIL\_HARD, FATTR4\_QUOTA\_AVAIL\_SOFT, FATTR4\_QUOTA\_USED, FATTR4\_TIME\_BACKUP, and FATTR4\_ACL\.

   An attempt to set these attributes results in an `NFS4ERR_ATTRNOTSUPP` error that is sent back to the client\. 

## Additional Considerations<a name="limits-additional-considerations"></a>

In addition, note the following:
+ For a list of AWS Regions where you can create Amazon EFS file systems, see the [AWS General Reference](https://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem_region)\.
+ Some AWS accounts created before 2012 might have access to Availability Zones in us\-east\-1 that don't support creating mount targets\. If you can't create a mount target in one of these AWS Regions, try a different Availability Zone in that AWS Region\. However, there are cost considerations for mounting a file system on an EC2 instance in an Availability Zone through a mount target created in another Availability Zone\. 
+ You mount your file system from EC2 instances in your VPC by using the mount targets you create in the VPC\. You can also mount your file system on your EC2\-Classic instances \(which are not in the VPC\), but you must first link them to your VPC by using ClassicLink\. For more information about using ClassicLink, see [ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ You can mount an Amazon EFS file system from on\-premises data center servers using AWS Direct Connect and VPN\.
+ VPC peering within a single AWS Region when using [certain Amazon EC2 instance types](manage-fs-access-vpc-peering.md) is supported\. Inter\-region VPC peering is supported for all instance types\.