# Changing the VPC for Your Mount Target<a name="manage-fs-access-change-vpc"></a>

You can use an Amazon EFS file system in one Amazon VPC at a time\. That is, you create mount targets in a VPC for your file system, and use those mount targets to provide access to the file system\.

You can mount the Amazon EFS file system from these targets: 
+ Amazon EC2 instances in the same VPC
+ EC2 instances in a VPC connected by VPC peering
+ On\-premises servers by using AWS Direct Connect
+ On\-premises servers over an AWS virtual private network \(VPN\) by using Amazon VPC 

A *VPC peering connection* is a networking connection between two VPCs that enables you to route traffic between them using private Internet Protocol version 4 \(IPv4\) or Internet Protocol version 6 \(IPv6\) addresses\. For more information on how Amazon EFS works with VPC peering, see [Mounting from Another Account or VPC](manage-fs-access-vpc-peering.md)\.

To access the file system from EC2 instances in another VPC, you must first delete the current mount targets and then create new mount targets\. 

## Using the Console<a name="manage-fs-access-change-vpc-using-console"></a>

1. In the Amazon EFS console, select the file system, choose **Actions**, and then choose **Manage File System Access**\. 

   The console displays the **Manage File System Access** page with a list of mount targets you created for the file system in a VPC\. The following illustration shows a file system that has three mount targets, one in each Availability Zones\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/manage-fs-05.png)

1. To change the VPC, select another VPC from the **VPC** list\.

   The console clears all of the mount target information and lists only the Availability Zone\. 

1. Create mount targets in one or more Availability Zones as follows:

   1. If the Availability Zone has multiple subnets, select a subnet from the **Subnet** list\.

   1. Amazon EFS automatically selects an available IP address, or you can provide another IP address explicitly\.

   1. Choose the security groups that you want to associate\. For inter\-region VPC peering, the security groups you choose need to have a rule that allows inbound traffic over NFS \(port 2049\) from your other VPC or VPCs\.

      For information about security groups, see [Amazon EC2 Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Choose **Save**\.

   The console first deletes the mount targets from the previous VPC and then creates new mount targets in the new VPC that you selected\. 

## Using the CLI<a name="manage-fs-access-change-vpc-using-cli"></a>

To use a file system in another VPC, you must first delete any mount targets you previously created in a VPC and then create new mount targets in another VPC\. For example AWS CLI commands, see [Creating or Deleting Mount Targets in a VPC](https://docs.aws.amazon.com/efs/latest/ug/manage-fs-access-create-delete-mount-targets.html#manage-fs-create-delete-mt-cli)\.