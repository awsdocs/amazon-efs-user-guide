# Changing the VPC for your mount target<a name="manage-fs-access-change-vpc"></a>

You can use an Amazon EFS file system in one VPC based on the Amazon VPC service at a time\. That is, you create mount targets in a VPC for your file system, and use those mount targets to provide access to the file system\.

You can mount the Amazon EFS file system from these targets: 
+ Amazon EC2 instances in the same VPC
+ EC2 instances in a VPC connected by VPC peering
+ On\-premises servers by using AWS Direct Connect
+ On\-premises servers over an AWS virtual private network \(VPN\) by using Amazon VPC 

A *VPC peering connection* is a networking connection between two VPCs that enables you to route traffic between them\. The connection can use private Internet Protocol version 4 \(IPv4\) or Internet Protocol version 6 \(IPv6\) addresses\. For more information on how Amazon EFS works with VPC peering, see [Mounting EFS file systems from another account or VPC](manage-fs-access-vpc-peering.md)\.

To access the file system from EC2 instances in another VPC, you have to:
+ Delete the current mount targets\.
+ Change the VPC\.
+ Create new mount targets\.

For more information on performing these steps in the AWS Management Console, see [To change the VPC for an Amazon EFS file system \(console\)](accessing-fs.md#change-vpc-console2)\.

## Using the CLI<a name="manage-fs-access-change-vpc-using-cli"></a>

To use a file system in another VPC, first delete any mount targets that you previously created in a VPC\. Then create new mount targets in another VPC\. For example AWS CLI commands, see [Managing mount targets using the AWS CLI](accessing-fs.md#create-mount-target-cli)\.