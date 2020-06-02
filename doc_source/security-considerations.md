# Security in Amazon EFS<a name="security-considerations"></a>

Cloud security at AWS is the highest priority\. As an AWS customer, you benefit from a data center and network architecture that is built to meet the requirements of the most security\-sensitive organizations\.

Security is a shared responsibility between AWS and you\. The [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) describes this as security *of* the cloud and security *in* the cloud:
+ **Security of the cloud** – AWS is responsible for protecting the infrastructure that runs AWS services in the AWS Cloud\. AWS also provides you with services that you can use securely\. Third\-party auditors regularly test and verify the effectiveness of our security as part of the [AWS compliance programs](http://aws.amazon.com/compliance/programs/)\. To learn about the compliance programs that apply to Amazon Elastic File System, see [AWS Services in Scope by Compliance Program](http://aws.amazon.com/compliance/services-in-scope/)\.
+ **Security in the cloud** – Your responsibility is determined by the AWS service that you use\. You are also responsible for other factors including the sensitivity of your data, your company’s requirements, and applicable laws and regulations\. 

This documentation helps you understand how to apply the shared responsibility model when using Amazon EFS\. The following topics show you how to configure Amazon EFS to meet your security and compliance objectives\. You also learn how to use other AWS services that help you to monitor and secure your Amazon EFS resources\. 

Following, you can find a description of security considerations for working with Amazon EFS\. There are four levels of access control to consider for Amazon EFS file systems, with different mechanisms used for each\.

**Topics**
+ [Data Encryption in EFS](encryption.md)
+ [Identity and Access Management for Amazon EFS](auth-and-access-control.md)
+ [Controlling Network Access to Amazon EFS File Systems for NFS Clients](NFS-access-control-efs.md)
+ [Working with Users, Groups, and Permissions at the Network File System \(NFS\) Level](accessing-fs-nfs-permissions.md)
+ [Working with Amazon EFS Access Points](efs-access-points.md)
+ [Logging and Monitoring in Amazon EFS](logging-monitoring.md)
+ [Compliance Validation for Amazon Elastic File System](SERVICENAME-compliance.md)
+ [Resilience in Amazon Elastic File System](disaster-recovery-resiliency.md)
+ [Amazon Elastic File System Network Isolation](network-isolation.md)