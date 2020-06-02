# Using Security Groups for Amazon EC2 Instances and Mount Targets<a name="network-access"></a>

When using Amazon EFS, you specify Amazon EC2 security groups for your EC2 instances and security groups for the EFS mount targets associated with the file system\. A security group acts as a firewall, and the rules that you add define the traffic flow\. In the Getting Started exercise, you created one security group when you launched the EC2 instance\. You then associated another with the EFS mount target \(that is, the default security group for your default VPC\)\. That approach works for the Getting Started exercise\. However, for a production system, you should set up security groups with minimal permissions for use with EFS\.

You can authorize inbound and outbound access to your EFS file system\. To do so, you add rules that allow your EC2 instance to connect to your Amazon EFS file system through the mount target using the Network File System \(NFS\) port\. Take the following steps to create and update your security groups\.

**To create security groups for EC2 instances and mount targets**

1. Create two security groups in your VPC\.

   For instructions, see the procedure "To create a security group" in [Creating a Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#CreatingSecurityGroups) in the *Amazon VPC User Guide*\. 

1. Open the Amazon VPC Management Console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/), and verify the default rules for these security groups\. Both security groups should have only an outbound rule that allows traffic to leave\. 

**To update the necessary access for your security groups**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Add a rule for your EC2 security group to allow inbound access using Secure Shell \(SSH\) from any host\. Optionally, restrict the **Source** address\. 

   You don't need to add an outbound rule, because the default outbound rule allows all traffic to leave\. If this were not the case, you'd need to add an outbound rule to open the TCP connection on the NFS port, identifying the mount target security group as the destination\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-ec2-resources-100.png)

   For instructions, see [Adding and Removing Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules) in the *Amazon VPC User Guide*\. 

1. Add a rule for the mount target security group to allow inbound access from the EC2 security group as shown following\. The EC2 security group is identified as the source\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-ec2-resources-120.png)

1. Verify that both security groups now authorize inbound and outbound access\.

For more information about security groups, see [Security Groups for EC2\-VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#vpc-security-groups) in the *Amazon EC2 User Guide for Linux Instances*\. 