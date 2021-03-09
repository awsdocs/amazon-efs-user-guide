# Creating or deleting mount targets in a VPC<a name="manage-fs-access-create-delete-mount-targets"></a>

To access an Amazon EFS file system in a VPC, you need mount targets\. For an Amazon EFS file system, the following is true:
+ You can create one mount target in each Availability Zone\. 
+ If the VPC has multiple subnets in an Availability Zone, you can create a mount target in only one of those subnets\. All EC2 instances in the Availability Zone can share the single mount target\. 

**Note**  
We recommend that you create a mount target in each of the Availability Zones\. There are cost considerations for mounting a file system on an EC2 instance in an Availability Zone through a mount target created in another Availability Zone\. For more information, see [Amazon EFS](https://aws.amazon.com/efs/)\. In addition, by always using a mount target local to the instance's Availability Zone, you eliminate a partial failure scenario\. If the mount target's zone goes down, you can't access your file system through that mount target\. 

You can delete mount targets\. A mount target deletion forcibly breaks any mounts of the file system using that mount target, which might disrupt instances or applications using those mounts\. For more information, see [Creating and managing mount targets](accessing-fs.md)\.

**Note**  
Before deleting a mount target, first unmount the file system\. For more information, see [Unmounting file systems](mounting-fs-mount-cmd-general.md#unmounting-fs)\.

Using the AWS Management Console, the AWS CLI, and the API, you can create and manage mount targets on file systems\. For existing mount targets, you can add and remove security groups, or delete the mount target\. For more information, see [Creating and managing mount targets](accessing-fs.md)\.