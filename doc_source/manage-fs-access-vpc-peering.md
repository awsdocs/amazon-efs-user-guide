# Mounting EFS File Systems from Another Account or VPC<a name="manage-fs-access-vpc-peering"></a>

You can mount your Amazon EFS file system from Amazon EC2 instances that are in a different account or virtual private cloud \(VPC\)\.

## Mounting from Another Account in the Same VPC<a name="mount-fs-diff-account-same-vpc"></a>

Using shared VPCs, you can mount an Amazon EFS file system that is owned by one account from Amazon EC2 instances that are owned by a different account\. For more information about setting up a shared VPC, see [Working with Shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html) in the *Amazon VPC Peering Guide*\. After you set up VPC sharing, the EC2 instances can mount the EFS file system using Domain Name System \(DNS\) name resolution or the EFS mount helper\.

## Mounting from Another VPC<a name="mount-fs-different-vpc"></a>

When you use a VPC peering connection or transit gateway to connect VPCs, Amazon EC2 instances that are in one VPC can access EFS file systems in another VPC, even if the VPCs belong to different accounts\.

A *transit gateway *is a network transit hub that you can use to interconnect your VPCs and on\-premises networks\. For more information about using VPC transit gateways, see [Getting Started with Transit Gateways](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html) in the *Amazon VPC Transit Gateways Guide*\.

A *VPC peering connection* is a networking connection between two VPCs that enables you to route traffic between them using private Internet Protocol version 4 \(IPv4\) or Internet Protocol version 6 \(IPv6\) addresses\. You can use VPC peering to connect VPCs within the same region or between regions\. For more information on VPC peering, see [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon VPC Peering Guide*\.

For information about how to mount your EFS file system after you set up a transit gateway or VPC peering connection, see [Walkthrough: Mount a File System from a Different VPC ](efs-different-vpc.md)\.

You can't use DNS name resolution for EFS mount points in another VPC\. To mount your EFS file system, use the IP address of the mount points in the corresponding Availability Zone\. Alternatively, you can use Amazon Route 53 as your DNS service\. In Route 53, you can resolve the EFS mount target IP addresses from another VPC by creating a private hosted zone and resource record set\. For more information on how to do so, see [Working with Private Hosted Zones](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html) and [Working with Records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/rrsets-working-with.html) in the *Amazon Route 53 Developer Guide*\.