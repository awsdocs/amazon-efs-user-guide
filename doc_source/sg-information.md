# Security Considerations for Network Access<a name="sg-information"></a>

An NFS version 4\.1 \(NFSv4\.1\) client can only mount a file system if it can make a network connection to the NFS port of one of the file system's mount targets\. Similarly, an NFSv4\.1 client can only assert a user and group ID when accessing a file system if it can make this network connection\. 

Whether you can make this network connection is governed by a combination of the following:
+ **Network isolation provided by the mount targets' VPC** – File system mount targets can't have public IP addresses associated with them\. The only targets that can mount file systems are the following: 
  + Amazon EC2 instances in the local Amazon VPC
  + EC2 instances in connected VPCs
  + On\-premises servers connected to an Amazon VPC by using AWS Direct Connect and an AWS virtual private network \(VPN\)
+ **Network access control lists \(ACLs\) for the VPC subnets of the client and mount targets, for access from outside the mount target's subnets** – To mount a file system, the client must be able to make a TCP connection to the NFS port of a mount target and receive return traffic\. 
+ **Rules of the client's and mount targets' VPC security groups, for all access** – For an EC2 instance to mount a file system, the following security group rules must be in effect: 
  +  The file system must have a mount target whose network interface has a security group with a rule that enables inbound connections on the NFS port from the instance\. You can enable inbound connections either by IP address \(CIDR range\) or security group\. The source of the security group rules for the inbound NFS port on mount target network interfaces is a key element of file system access control\. Inbound rules other than the one for the NFS port, and any outbound rules, aren't used by network interfaces for file system mount targets\. 
  +  The mounting instance must have a network interface with a security group rule that enables outbound connections to the NFS port on one of the file system's mount targets\. You can enable outbound connections either by IP address \(CIDR range\) or security group\.

For more information, see [Creating mount targets](accessing-fs.md)\.