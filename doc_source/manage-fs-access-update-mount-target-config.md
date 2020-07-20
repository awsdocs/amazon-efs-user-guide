# Updating the mount target configuration<a name="manage-fs-access-update-mount-target-config"></a>

After you create a mount target for your file system, you might want to update security groups that are in effect\. You can't change the IP address of an existing mount target\. To change an IP address, delete the mount target and create a new one with the new address\. Deleting a mount target breaks any existing file system mounts\. 

**Note**  
Before deleting a mount target, first unmount the file system\.

## Modifying a security group<a name="manage-fs-access-update-mount-target-config-sg"></a>

Security groups define inbound and outbound access\. When you change security groups associated with a mount target, make sure that you authorize necessary inbound and outbound access\. Doing so enables your EC2 instance to communicate with the file system\. 

For more information about security groups, see [Amazon EC2 Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\.

To modify a mount target's security group using the AWS Management Console, see [Managing mount targets using the Amazon EFS console](accessing-fs.md#console2-create-mount-target-existing-fs)\.

To modify a mount target's security group using the AWS CLI, see [Managing mount targets using the AWS CLI](accessing-fs.md#create-mount-target-cli)\.