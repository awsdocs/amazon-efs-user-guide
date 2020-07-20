# Transferring data into Amazon EFS<a name="transfer-data-to-efs"></a>

We recommend using AWS DataSync to transfer data into Amazon EFS\. DataSync is a data transfer service that simplifies, automates, and accelerates moving and replicating data between on\-premises storage systems and AWS storage services over the internet or AWS Direct Connect\. DataSync can transfer your file system data and metadata, such as ownership, timestamps, and access permissions\.

 You can also use DataSync to transfer files between two EFS file systems, including file systems in different AWS Regions and file systems owned by different AWS accounts\. Using DataSync to copy data between EFS file systems, you can perform one\-time data migrations, periodic data ingestion for distributed workloads, and automate replication for data protection and recovery\.

 To simplify transferring files between two EFS file systems using DataSync, you can use the [AWS DataSync In\-Cloud QuickStart and Scheduler](https://github.com/aws-samples/amazon-efs-tutorial/tree/master/in-cloud-transfer)\.

 For more information, see [Getting Started with Amazon Elastic File System](getting-started.md) and the [https://docs.aws.amazon.com/datasync/latest/userguide/](https://docs.aws.amazon.com/datasync/latest/userguide/)\.