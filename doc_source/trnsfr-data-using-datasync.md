# Using AWS DataSync to transfer data in Amazon EFS<a name="trnsfr-data-using-datasync"></a>

 AWS DataSync is an online data transfer service that simplifies, automates, and accelerates moving and replicating data between on\-premises storage systems, and also between AWS storage services\. DataSync can copy data between Network File System \(NFS\), Server Message Block \(SMB\) file servers, self\-managed object storage, AWS Snowcone, Amazon S3 buckets, Amazon EFS file systems, and FSx for Windows File Server file systems\.

 You can also use DataSync to transfer files between two EFS file systems, including file systems in different AWS Regions and file systems owned by different AWS accounts\. Using DataSync to copy data between EFS file systems, you can perform one\-time data migrations, periodic data ingestion for distributed workloads, and automate replication for data protection and recovery\.

 To simplify transfer files between two EFS file systems using DataSync, you can use the [AWS DataSync In\-Cloud QuickStart and Scheduler](https://github.com/aws-samples/amazon-efs-tutorial/tree/master/in-cloud-transfer)\.

 For more information, see [Getting started with Amazon Elastic File System](getting-started.md) and the [AWS DataSync User Guide](https://docs.aws.amazon.com/datasync/latest/userguide/)\.