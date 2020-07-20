# Monitoring EFS with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor file systems using Amazon CloudWatch, which collects and processes raw data from Amazon EFS into readable, near real\-time metrics\. These statistics are recorded for a period of 15 months, so that you can access historical information and gain a better perspective on how your web application or service is performing\. By default, Amazon EFS metric data is automatically sent to CloudWatch at 1\-minute periods\. For more information about CloudWatch, see [What Are Amazon CloudWatch, Amazon CloudWatch Events, and Amazon CloudWatch Logs?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

## Amazon CloudWatch metrics for Amazon EFS<a name="efs-metrics"></a>

The `AWS/EFS` namespace includes the following metrics\.

**`BurstCreditBalance`**  
The number of burst credits that a file system has\. Burst credits allow a file system to burst to throughput levels above a file system’s baseline level for periods of time\. For more information, see [Throughput Scaling with Bursting Mode](performance.md#bursting)\.  
The `Minimum` statistic is the smallest burst credit balance for any minute during the period\. The `Maximum` statistic is the largest burst credit balance for any minute during the period\. The `Average` statistic is the average burst credit balance during the period\.   
Units: Bytes  
Valid statistics: `Minimum`, `Maximum`, `Average`

**`ClientConnections`**  
The number of client connections to a file system\. When using a standard client, there is one connection per mounted Amazon EC2 instance\.  
To calculate the average `ClientConnections` for periods greater than one minute, divide the `Sum` statistic by the number of minutes in the period\.
Units: Count of client connections  
Valid statistics: `Sum`

**`DataReadIOBytes`**  
The number of bytes for each file system read operation\.  
The `Sum` statistic is the total number of bytes associated with read operations\. The `Minimum` statistic is the size of the smallest read operation during the period\. The `Maximum` statistic is the size of the largest read operation during the period\. The `Average` statistic is the average size of read operations during the period\. The `SampleCount` statistic provides a count of read operations\.  
Units:  
+ Bytes for `Minimum`, `Maximum`, `Average`, and `Sum`\.
+ Count for `SampleCount`\.
Valid statistics: `Minimum`, `Maximum`, `Average`, `Sum`, `SampleCount`

**`DataWriteIOBytes`**  
The number of bytes for each file write operation\.  
The `Sum` statistic is the total number of bytes associated with write operations\. The `Minimum` statistic is the size of the smallest write operation during the period\. The `Maximum` statistic is the size of the largest write operation during the period\. The `Average` statistic is the average size of write operations during the period\. The `SampleCount` statistic provides a count of write operations\.  
Units:  
+ Bytes are the units for the `Minimum`, `Maximum`, `Average`, and `Sum` statistics\.
+ Count for `SampleCount`\.
Valid statistics: `Minimum`, `Maximum`, `Average`, `Sum`, `SampleCount`

**`MetadataIOBytes`**  
The number of bytes for each metadata operation\.  
The `Sum` statistic is the total number of bytes associated with metadata operations\. The `Minimum` statistic is the size of the smallest metadata operation during the period\. The `Maximum` statistic is the size of the largest metadata operation during the period\. The `Average` statistic is the size of the average metadata operation during the period\. The `SampleCount` statistic provides a count of metadata operations\.  
Units:  
+ Bytes are the units for the `Minimum`, `Maximum`, `Average`, and `Sum` statistics\.
+ Count for `SampleCount`\.
Valid statistics: `Minimum`, `Maximum`, `Average`, `Sum`, `SampleCount`

**`PercentIOLimit`**  
Shows how close a file system is to reaching the I/O limit of the General Purpose performance mode\. If this metric is at 100% more often than not, consider moving your application to a file system using the Max I/O performance mode\.  
This metric is only submitted for file systems using the General Purpose performance mode\.
Units:  
+ Percent

**`PermittedThroughput`**  
The maximum amount of throughput a file system is allowed\. For file systems in the Provisioned Throughput mode, if the amount of storage allows your file system to drive a higher amount of throughput than you provisioned, this metric will reflect the higher throughput instead of the provisioned amount\. For file systems in the Bursting Throughput mode, this value is a function of the file system size and `BurstCreditBalance`\. For more information, see [Amazon EFS Performance](performance.md)\.  
The `Minimum` statistic is the smallest throughput permitted for any minute during the period\. The `Maximum` statistic is the highest throughput permitted for any minute during the period\. The `Average` statistic is the average throughput permitted during the period\.   
Units: Bytes per second  
Valid statistics: `Minimum`, `Maximum`, `Average`

**`TotalIOBytes`**  
The number of bytes for each file system operation, including data read, data write, and metadata operations\.  
The `Sum` statistic is the total number of bytes associated with all file system operations\. The `Minimum` statistic is the size of the smallest operation during the period\. The `Maximum` statistic is the size of the largest operation during the period\. The `Average` statistic is the average size of an operation during the period\. The `SampleCount` statistic provides a count of all operations\.  
To calculate the average operations per second for a period, divide the `SampleCount` statistic by the number of seconds in the period\. To calculate the average throughput \(Bytes per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. 
Units:  
+ Bytes for `Minimum`, `Maximum`, `Average`, and `Sum` statistics\.
+ Count for `SampleCount`\.
Valid statistics: `Minimum`, `Maximum`, `Average`, `Sum`, `SampleCount`

## Bytes reported in CloudWatch<a name="cloudwatch-bytes"></a>

As with Amazon S3 and Amazon EBS, Amazon EFS CloudWatch metrics are reported as raw *Bytes*\. Bytes are not rounded to either a decimal or binary multiple of the unit\. Keep this in mind when calculating your burst rate using the data you get from the metrics\. For more information on bursting, see [Throughput Scaling with Bursting Mode](performance.md#bursting)\.

## Amazon EFS dimensions<a name="efs-dimensions"></a>

Amazon EFS metrics use the `EFS` namespace and provide metrics for a single dimension, `FileSystemId`\. A file system's ID can be found in the Amazon EFS console, and it takes the form of `fs-XXXXXXXX`\.