# Mounting from Another Account or VPC<a name="manage-fs-access-vpc-peering"></a>

You can mount your Amazon EFS file system from Amazon EC2 instances in a different account or VPC using an Amazon VPC peering connection\. A *VPC peering connection *is a networking connection between two VPCs that enables you to route traffic between them using private Internet Protocol version 4 \(IPv4\) or Internet Protocol version 6 \(IPv6\) addresses\. For more information on VPC peering, see [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon VPC Peering Guide\.*

To mount EFS file systems in another account or VPC in another AWS Region, you can use a VPC peering connection with any EC2 instance type\. To mount EFS file systems in another account or VPC in the same AWS Region, you can use a VPC peering connection if you use one of the following EC2 instance types: 
+ T3
+ C5
+ C5d
+ I3\.metal
+ M5
+ M5d
+ R5
+ R5d
+ z1d

For more details on how to do so, see [Walkthrough: Mount a File System from a Different VPC ](efs-different-vpc.md)\.

You can't use Domain Name System \(DNS\) name resolution for EFS mount points in peered VPCs\. To mount your EFS file system, use the IP address of the mount points in the corresponding Availability Zone\. Alternatively, you can use Amazon Route 53 as your DNS service\. In Route 53, you can resolve the EFS mount target IP addresses from another VPC by creating a private hosted zone and resource record set\. For more information on how to do so, see [Working with Private Hosted Zones](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html) and [Working with Records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/rrsets-working-with.html) in the *Amazon Route 53 Developer Guide\.*