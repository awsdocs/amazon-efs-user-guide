# Managing file system network accessibility<a name="manage-fs-access"></a>

You mount your file system on Amazon EC2 or other AWS compute instance in your virtual private cloud \(VPC\) using a mount target that you create for the file system\. Managing file system network accessibility refers to managing a file system's mount targets\. 

The following illustration shows how EC2 instances in a VPC access an Amazon EFS file system using a mount target\. 

![\[Diagram showing 3 Availability Zones in a VPC, containing EC2 instances and mount targets, and a mounted EFS file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/efs-manage-mount-targets.png)

The illustration shows three EC2 instances launched in different VPC subnets accessing an Amazon EFS file system\. The illustration also shows one mount target in each of the Availability Zones \(regardless of the number of subnets in each Availability Zone\)\.

You can create only one mount target per Availability Zone\. If an Availability Zone has multiple subnets, as shown in one of the zones in the illustration, you create a mount target in only one of the subnets\. As long as you have one mount target in an Availability Zone, the EC2 instances launched in any of its subnets can share the same mount target\.

Managing mount targets refers to these activities:
+ **Creating and deleting mount targets in a VPC** – At a minimum, you should create a mount target in each Availability Zone from which you want to access the file system\. 
**Note**  
We recommend that you create mount targets in all the Availability Zones\. If you do, you can easily mount the file system on EC2 instances that you might launch in any of the Availability Zones\.

  If you delete a mount target, the operation forcibly breaks any mounts of the file system, which might disrupt instances or applications using those mounts\. To avoid application disruption, stop applications and unmount the file system before deleting the mount target\.

  You can use a file system only in one VPC at a time\. That is, you can create mount targets for the file system in one VPC at a time\. If you want to access the file system from another VPC, first delete the mount targets from the current VPC\. Then create new mount targets in another VPC\. 
+ **Updating the mount target configuration** – When you create a mount target, you associate security groups with the mount target\. A security group acts as a virtual firewall that controls the traffic to and from the mount target\. You can add inbound rules to control access to the mount target, and thus the file system\. After creating a mount target, you might want to modify the security groups assigned to them\.

  Each mount target also has an IP address\. When you create a mount target, you can choose an IP address from the subnet where you are placing the mount target\. If you omit a value, Amazon EFS selects an unused IP address from that subnet\.

  There is no Amazon EFS operation to change the IP address after creating a mount target\. Thus, you can't change the IP address programmatically or by using the AWS CLI\. But the console enables you to change the IP address\. Behind the scenes, the console deletes the mount target and creates the mount target again\. 
**Warning**  
If you change the IP address of a mount target, you break any existing file system mounts and you need to remount the file system\.

None of the configuration changes to file system network accessibility affects the file system itself\. Your file system and data remain unchanged\. 

The following sections provide information about managing network accessibility of your file system\. 

**Topics**
+ [Creating or deleting mount targets in a VPC](#manage-fs-access-create-delete-mount-targets)
+ [Changing the VPC for your mount target](#manage-fs-access-change-vpc)
+ [Updating the mount target configuration](#manage-fs-access-update-mount-target-config)

## Creating or deleting mount targets in a VPC<a name="manage-fs-access-create-delete-mount-targets"></a>

To access an Amazon EFS file system in a VPC, you need mount targets\. For an Amazon EFS file system, the following is true:
+ You can create one mount target in each Availability Zone\. 
+ If the VPC has multiple subnets in an Availability Zone, you can create a mount target in only one of those subnets\. All EC2 instances in the Availability Zone can share the single mount target\. 

**Note**  
We recommend that you create a mount target in each of the Availability Zones\. There are cost considerations for mounting a file system on an EC2 instance in an Availability Zone through a mount target created in another Availability Zone\. For more information, see [Amazon EFS](https://aws.amazon.com/efs/)\. In addition, by always using a mount target local to the instance's Availability Zone, you eliminate a partial failure scenario\. If the mount target's zone goes down, you can't access your file system through that mount target\. 

You can delete mount targets\. A mount target deletion forcibly breaks any mounts of the file system using that mount target, which might disrupt instances or applications using those mounts\. For more information, see [Creating and managing mount targets](accessing-fs.md)\.

**Note**  
Before deleting a mount target, first unmount the file system\. For more information, see [Unmounting file systems](mounting-fs-mount-cmd-general.md#unmounting-fs)\.

Using the AWS Management Console, the AWS CLI, and the API, you can create and manage mount targets on file systems\. For existing mount targets, you can add and remove security groups, or delete the mount target\. For more information, see [Creating and managing mount targets](accessing-fs.md)\.

## Changing the VPC for your mount target<a name="manage-fs-access-change-vpc"></a>

You can use an Amazon EFS file system in one VPC based on the Amazon VPC service at a time\. That is, you create mount targets in a VPC for your file system, and use those mount targets to provide access to the file system\.

You can mount the Amazon EFS file system from these targets: 
+ Amazon EC2 instances in the same VPC
+ EC2 instances in a VPC connected by VPC peering
+ On\-premises servers by using AWS Direct Connect
+ On\-premises servers over an AWS virtual private network \(VPN\) by using Amazon VPC 

A *VPC peering connection* is a networking connection between two VPCs that enables you to route traffic between them\. The connection can use private Internet Protocol version 4 \(IPv4\) or Internet Protocol version 6 \(IPv6\) addresses\. For more information on how Amazon EFS works with VPC peering, see [Mounting EFS file systems from another AWS account or VPC](efs-mount-helper.md#manage-fs-access-vpc-peering)\.

To access the file system from EC2 instances in another VPC, you have to:
+ Delete the current mount targets\.
+ Change the VPC\.
+ Create new mount targets\.

For more information on performing these steps in the AWS Management Console, see [To change the VPC for an Amazon EFS file system \(console\)](accessing-fs.md#change-vpc-console2)\.

### Using the CLI<a name="manage-fs-access-change-vpc-using-cli"></a>

To use a file system in another VPC, first delete any mount targets that you previously created in a VPC\. Then create new mount targets in another VPC\. For example AWS CLI commands, see [Managing mount targets using the AWS CLI](accessing-fs.md#create-mount-target-cli)\.

## Updating the mount target configuration<a name="manage-fs-access-update-mount-target-config"></a>

After you create a mount target for your file system, you might want to update the security groups that are in effect\. You can't change the IP address of an existing mount target\. To change an IP address, delete the mount target and create a new one with the new address\. Deleting a mount target breaks any existing file system mounts\. 

**Note**  
Before deleting a mount target, first unmount the file system\.

### Modifying a security group<a name="manage-fs-access-update-mount-target-config-sg"></a>

Security groups define inbound and outbound access\. When you change security groups associated with a mount target, make sure that you authorize necessary inbound and outbound access\. Doing so enables your EC2 instance to communicate with the file system\. 

For more information about security groups, see [Amazon EC2 Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\.

To modify a mount target's security group using the AWS Management Console, see [Managing mount targets using the Amazon EFS console](accessing-fs.md#console2-create-mount-target-existing-fs)\.

To modify a mount target's security group using the AWS CLI, see [Managing mount targets using the AWS CLI](accessing-fs.md#create-mount-target-cli)\.