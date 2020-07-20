# Managing file system network accessibility<a name="manage-fs-access"></a>

You mount your file system on an EC2 instance in your virtual private cloud \(VPC\) using a mount target that you create for the file system\. Managing file system network accessibility refers to managing the mount targets\. 

The following illustration shows how EC2 instances in a VPC access an Amazon EFS file system using a mount target\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/overview-flow.png)

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

None of the configuration changes to file system network accessibility affects the file system itself\. Your file system and data remain\. 

The following sections provide information about managing network accessibility of your file system\. 

**Topics**
+ [Creating or deleting mount targets in a VPC](manage-fs-access-create-delete-mount-targets.md)
+ [Changing the VPC for your mount target](manage-fs-access-change-vpc.md)
+ [Updating the mount target configuration](manage-fs-access-update-mount-target-config.md)