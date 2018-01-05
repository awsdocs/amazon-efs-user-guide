# Monitoring EFS File Sync with Amazon CloudWatch<a name="monitoring-file-sync"></a>

You can monitor EFS File Sync using Amazon CloudWatch, which collects and processes raw data from Amazon EFS into readable, near real\-time metrics\. These statistics are recorded for a period of 15 months, so that you can access historical information and gain a better perspective on how EFS File Sync\. By default, EFS File Sync metric data is automatically sent to CloudWatch at 5\-minute periods\. For more information about CloudWatch, see [What Are Amazon CloudWatch, Amazon CloudWatch Events, and Amazon CloudWatch Logs?](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring//WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

The AWS/FileSync namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
| `FilesTransferred` | The number of files and directories transferred from source file system to the Amazon EFS file system\. A file or directory is considered to be transferred if any aspect of the or directory required syncing\. and increments this metric\. In this case, this metric is incremented\. However, if only metadata is changed then no actual data will be transferred\.  units: Count  | 
| `PhysicalBytesTransferred` | The total number of bytes transferred over the network when the sync agent reads from the source file system to the Amazon EFS file system\. Unit: Bytes  | 
| `LogicalBytesTransferred` | The total size of the files transferred to the Amazon EFS file system\. Directories and metadata are not included in this metric\. Units: Bytes  | 

## Amazon EFS File Sync Dimensions<a name="file-sync-dimentions"></a>

EFS File Sync metrics use the AWS/FileSync namespace and provide metrics for the following dimensions\.

+ HostId—the unique ID of your host server\.

+ HostName—the name or domain of your host server\.

+ SyncSetId—the ID of the sync set\. It takes the form set\-12345678912345678