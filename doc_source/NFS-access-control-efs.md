# Controlling Network Access to Amazon EFS File Systems for NFS Clients<a name="NFS-access-control-efs"></a>

You can control access by NFS clients to Amazon EFS file systems using network layer security and EFS file system policies\. You can use the network layer security mechanisms available with Amazon EC2, such as VPC security group rules and network ACLs\. You can also use AWS IAM to control NFS access with an EFS file system policy and identity\-based policies\.

**Topics**
+ [Using Security Groups for Amazon EC2 Instances and Mount Targets](network-access.md)
+ [Source Ports for Working with EFS](source-ports.md)
+ [Security Considerations for Network Access](sg-information.md)
+ [Working with Interface VPC Endpoints in Amazon EFS](efs-vpc-endpoints.md)