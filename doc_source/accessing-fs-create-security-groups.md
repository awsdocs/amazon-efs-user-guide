# Creating security groups<a name="accessing-fs-create-security-groups"></a>

**Note**  
The following section is specific to Amazon EC2 and discusses how to create security groups so you can use Secure Shell \(SSH\) to connect to any instances that have mounted Amazon EFS file systems\. If you're not using SSH to connect to your Amazon EC2 instances, you can skip this section\. 

Both an Amazon EC2 instance and a mount target have associated security groups\. These security groups act as a virtual firewall that controls the traffic between them\. If you don't provide a security group when creating a mount target, Amazon EFS associates the default security group of the VPC with it\.

Regardless, to enable traffic between an EC2 instance and a mount target \(and thus the file system\), you must configure the following rules in these security groups:
+ The security groups you associate with a mount target must allow inbound access for the TCP protocol on the NFS port from all EC2 instances on which you want to mount the file system\.
+ Each EC2 instance that mounts the file system must have a security group that allows outbound access to the mount target on the NFS port\. 

For more information about security groups, see [Amazon EC2 Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\. 

## Creating security groups using the AWS Management Console<a name="create-security-groups-console"></a>

You can use the AWS Management Console to create security groups in your VPC\. To connect your Amazon EFS file system to your Amazon EC2 instance, you must create two security groups: one for your Amazon EC2 instance and another for your Amazon EFS mount target\.

1. Create two security groups in your VPC\. For instructions, see [Creating a Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#CreatingSecurityGroups) in the *Amazon VPC User Guide*\.

1. In the VPC console, verify the default rules for these security groups\. Both security groups should have only an outbound rule that allows traffic to leave\.

1.  You need to authorize additional access to the security groups as follows:

   1. Add a rule to the EC2 security group to allow SSH access to the instance on port 22 as shown following\. This is useful if you're planning on using an SSH client like PuTTY to connect to and administer your EC2 instance through a terminal interface\. Optionally, you can restrict the **Source** address\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/ec2-inbound-sg-ssh-rule.png)

      For instructions, see [Adding, removing, and updating rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules) in the *Amazon VPC User Guide*\.

   1. Add a rule to the mount target security group to allow inbound access from the EC2 security group, as shown following \(where the EC2 security group is identified as the source\)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/mnt-tgt-sg-inbound-rules.png)
**Note**  
You don't need to add an outbound rule because the default outbound rule allows all traffic to leave \(otherwise, you will need to add an outbound rule to open TCP connection on the NFS port, identifying the mount target security group as the destination\)\.

1. Verify that both security groups now authorize inbound and outbound access as described in this section\.

## Creating security groups using the AWS CLI<a name="create-security-groups-cli"></a>

For an example that shows how to create security groups using the AWS CLI, see [Step 1: Create Amazon EC2 Resources](wt1-create-ec2-resources.md)\.