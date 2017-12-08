# Amazon EFS File Sync<a name="get-started-file-sync"></a>

Using Amazon EFS File Sync, you can easily and efficiently sync files from an existing source file system into a destination Amazon EFS file system\. The source file system can be on\-premises or in the cloud\. With EFS File Sync, you can migrate file\-based applications to Amazon EC2 and sync existing datasets into Amazon EFS\. EFS File Sync copies your file data, and file system metadata such as ownership, timestamps, and access permissions\. 



EFS File Sync provides the following benefits:

+ Efficient high\-performance parallel data transfer that tolerates unreliable and high\-latency networks\.

+ Encryption of data transferred from your IT environment to AWS\.

+ Data transfer rate up to five times faster than standard Linux copy tools\.

The following diagram shows the EFS File Sync architecture\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-file-sync-architecture.png)

To sync your existing file system to Amazon EFS with EFS File Sync, you do the following:

1. Deploy a sync agent that can mount your source file system\. The sync agent is available on the Amazon EFS console as a downloadable virtual machine \(VM\) image for VMware ESXi or an Amazon Machine Image \(AMI\) for Amazon EC2\. 

1. Create a sync task and configure the source and destination file systems\. EFS File Sync accesses your source file system through the agent by using the Network File System version 3\.0 or 4\.0 \(NFSv4 or v3\) protocol\.

1. Start your sync task to begin syncing files from the source file system to the destination Amazon EFS file system\.

1. Monitor the progress of your sync task on the Amazon EFS Management Console or from Amazon CloudWatch\.

**EFS File Sync endpoints**  
The following endpoints are required by EFS File Sync\.

```
cp-sync.$region.amazonaws.com
api-sync.$region.amazonaws.com        
activation-sync.$region.amazonaws.com
```

For information about supported AWS Regions, see [Amazon Elastic File System](http://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem-region) in the AWS General Reference\.