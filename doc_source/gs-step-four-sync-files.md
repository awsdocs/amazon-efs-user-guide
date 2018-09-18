# Step 4: Sync Files from Existing File Systems to Amazon EFS Using EFS File Sync<a name="gs-step-four-sync-files"></a>

Now that you have created a functioning Amazon EFS file system, you can use EFS File Sync to sync files from an existing file system to Amazon EFS\. EFS File Sync can sync your file data, and also file system metadata such as ownership, time stamps, and access permissions\.

In this step, we assume that you have the following:
+ A source NFS file system that you can sync from\. This source system needs to be accessible over NFS version 3 or version 4\. The source file system can be on\-premises or on Amazon EC2\. 
+ A destination Amazon EFS file system to sync to\. If you don't have an Amazon EFS file system, create one\. For more information, see [Getting Started with Amazon Elastic File System](getting-started.md)\.
+ DNS access as detailed following:
  + For sync agents hosted on Amazon EC2, your sync agent requires access to a DNS server configured in your Amazon VPC\. This server can be the default Amazon DNS server\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the *Amazon VPC User Guide*\.
  + For sync agents hosted on\-premises, your sync agent requires access to a functioning DNS server that can communicate with AWS\.

**To sync files from an existing file system to Amazon EFS**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Download and deploy a sync agent\. For on\-premises deployment, the sync agent is provided as a virtual machine \(VM\) image for VMware ESXi\. For an AWS Cloud deployment, you can create an Amazon EC2 instance from the community Amazon Machine Image \(AMI\)\.

1. Create a sync task and configure your source and destination file systems\.

1. Start your sync task to begin syncing files from the source file system to the Amazon EFS file system\.

1. Monitor your sync task on the Amazon EFS console or from Amazon CloudWatch\. For more information, see [Monitoring EFS File Sync with Amazon CloudWatch](monitoring-file-sync.md)\.

For more details on the EFS File Sync process, see the following:
+ For information about how to sync files from an on\-premises file system to Amazon EFS, see [Walkthrough 7: Sync Files from an On\-Premises File System to Amazon EFS by Using EFS File Sync](walkthrough-file-sync-onpremise.md)\.
+ For information about how to sync files from Amazon EC2 to Amazon EFS, see [Walkthrough 8: Sync a File System from Amazon EC2 to Amazon EFS Using EFS File Sync](walkthrough-file-sync-ec2.md)\.