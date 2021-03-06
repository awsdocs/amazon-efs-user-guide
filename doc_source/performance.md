# Amazon EFS performance<a name="performance"></a>

Following, you can find an overview of Amazon EFS performance, with a discussion of the available performance and throughput modes and some useful performance tips\.

## Performance overview<a name="performance-overview"></a>

Amazon EFS file systems are distributed across an unconstrained number of storage servers\. This distributed data storage design enables file systems to grow elastically to petabyte scale\. It also enables massively parallel access from compute instances, including Amazon EC2, Amazon ECS, and AWS Lambda, to your data\. The Amazon EFS distributed design avoids the bottlenecks and constraints inherent to traditional file servers\.

This distributed data storage design means that multithreaded applications and applications that concurrently access data from multiple compute instances can drive substantial levels of aggregate throughput and IOPS\. Big data and analytics workloads, media processing workflows, content management, and web serving are examples of these applications\.

In addition, for Amazon EFS file systems using Regional storage classes, data is distributed across multiple *Availability Zones* in an AWS Region, providing the highest level of durability and availability\. The following tables compare high\-level performance and storage characteristics for Amazon EFS and Amazon Elastic Block Store \(Amazon EBS\) cloud storage services\.


**Performance comparison: Amazon EFS and Amazon EBS**  

|  | Amazon EFS | Amazon EBS Provisioned IOPS | 
| --- | --- | --- | 
| Per\-operation latency | Low, consistent latency\. | Lowest, consistent latency\. | 
| Throughput scale | 10\+ GB per second\. | Up to 2 GB per second\. | 


**Storage characteristics comparison: Amazon EFS and Amazon EBS**  

|  | Amazon EFS file systems using One Zone storage classes | Amazon EFS file systems using Regional storage classes | Amazon EBS Provisioned IOPS | 
| --- | --- | --- | --- | 
| Availability and durability | Data is stored redundantly in a single Availability Zone\. | Data is stored redundantly across multiple Availability Zones\. | Data is stored redundantly in a single Availability Zone\. | 
| Access | Up to thousands of compute instances, from multiple Availability Zones, can connect concurrently to a file system\. | Up to thousands of compute instances, from multiple Availability Zones, can connect concurrently to a file system\. | A single compute instance in a single Availability Zone can connect to a file system\. | 
| Use cases | Big data and analytics, media processing workflows, content management, web serving, and home directories that do not require Multi\-AZ resilience\. | Big data and analytics, media processing workflows, content management, web serving, and home directories\. | Boot volumes, transactional and NoSQL databases, data warehousing, and ETL\. | 

The distributed nature of Amazon EFS file systems enables high levels of availability, durability, and scalability\. EFS file systems using Regional storage classes store data redundantly across multiple Availability Zones and offer the highest levels of availability and durability\. This distributed architecture results in a small latency overhead for each file operation\. Due to this per\-operation latency, overall throughput generally increases as the average I/O size increases, because the overhead is amortized over a larger amount of data\. Amazon EFS supports highly parallelized workloads \(for example, using concurrent operations from multiple threads and multiple Amazon EC2 instances\), which enables high levels of aggregate throughput and operations per second\.

## Understanding metered throughput<a name="read-write-throughput"></a>

All Amazon EFS file systems have an associated metered throughput that is determined by either the amount of provisioned throughput that you configured or the amount of data stored in the Standard storage class\. Read requests and write requests are metered at different rates\. EFS file systems meter read requests at one\-third the rate of other requests\.

For example, if you are driving 30 MiB/s of both read throughput and write throughput, the read portion counts as 10MiB/s of metered throughput, the write portion counts as 30 MiB/s, and the combined metered throughput is 40 MiB/s\. This combined throughput adjusted for consumption rates is reflected in the `MeteredIOBytes` CloudWatch metric\. For more information, see [Amazon CloudWatch metrics for Amazon EFS](efs-metrics.md)\.

## Amazon EFS use cases<a name="performance-usecases"></a>

Amazon EFS is designed to meet the performance needs of the following use cases\.

### Big data and analytics<a name="perf-bigdata"></a>

Amazon EFS provides the scale and performance required for big data applications that require high throughput to compute nodes coupled with read\-after\-write consistency and low\-latency file operations\.

### Media processing workflows<a name="perf-media"></a>

Media workflows like video editing, studio production, broadcast processing, sound design, and rendering often depend on shared storage to manipulate large files\. A strong data consistency model with high throughput and shared file access can cut the time it takes to perform these jobs and consolidate multiple local file repositories into a single location for all users\.

### Content management and web serving<a name="perf-contentserve"></a>

Amazon EFS provides a durable, high throughput file system for content management systems that store and serve information for a range of applications like websites, online publications, and archives\.

### Home directories<a name="perf-directories"></a>

Amazon EFS can provide storage for organizations that have many users that need to access and share common datasets\. An administrator can use Amazon EFS to create a file system accessible to people across an organization and establish permissions for users and groups at the file or directory level\.

## Performance modes<a name="performancemodes"></a>

To support a wide variety of cloud storage workloads, Amazon EFS offers two performance modes, *General Purpose* mode and *Max I/O* mode\. You choose a file system's performance mode when you create it, and it cannot be changed\.

**Note**  
Max I/O performance mode is not available for file systems using One Zone storage classes\.

The two performance modes have no additional costs, so your Amazon EFS file system is billed and metered the same, regardless of your performance mode\. For information about file system quotas, see [Quotas for Amazon EFS file systems](limits.md#limits-fs-specific)\.

**Note**  
An Amazon EFS file system's performance mode can't be changed after the file system is created\.

### General Purpose performance mode<a name="generalpurpose"></a>

We recommend the General Purpose performance mode for the majority of your Amazon EFS file systems\. General Purpose is ideal for latency\-sensitive use cases, like web serving environments, content management systems, home directories, and general file serving\. If you don't choose a performance mode when you create your file system, Amazon EFS selects the General Purpose mode for you by default\.

### Max I/O performance mode<a name="maxio"></a>

 File systems in the Max I/O mode can scale to higher levels of aggregate throughput and operations per second\. This scaling is done with a tradeoff of slightly higher latencies for file metadata operations\. Highly parallelized applications and workloads, such as big data analysis, media processing, and genomic analysis, can benefit from this mode\. 

**Note**  
Max I/O performance mode is not available for file systems using One Zone storage classes\.

### Using the right performance mode<a name="using-perfmode"></a>

Our recommendation for determining which performance mode to use is as follows:

1. [Create a new file system](gs-step-two-create-efs-resources.md) using the default General Purpose performance mode\.

1. Run your application \(or a use case similar to your application\) for a period of time to test its performance\.

1. Monitor the [PercentIOLimit](efs-metrics.md) Amazon CloudWatch metric for Amazon EFS during the performance test\. For more information about accessing this and other metrics, see [Amazon CloudWatch Metrics](monitoring_overview.md)\.

If the `PercentIOLimit` percentage returned was at or near 100 percent for a significant amount of time during the test, your application should use the Max I/O performance mode\. Otherwise, it should use the default General Purpose mode\. 

To move to a different performance mode, migrate the data to a different file system that was created in the other performance mode\. You can use DataSync to transfer files between two EFS file systems\. To learn more, see [Transferring data into and out of Amazon EFS](transfer-data-to-efs.md)\.

Some latency\-sensitive workloads require the higher I/O levels provided by Max I/O performance mode and the lower latency provided by General Purpose performance mode\. For this type of workload, we recommend creating multiple General Purpose performance mode file systems\. In this case, we recommend then spreading the application workload across all these file systems, as long as the workload and applications can support it\. 

By taking this approach, you can create a logical file system and shard data across multiple EFS file systems\. Each file system is mounted as a subdirectory, and your application can access these subdirectories in parallel\. This approach allows latency\-sensitive workloads to scale to higher levels of file system operations per second, aggregated across multiple file systems\. At the same time, these workloads can take advantage of the lower latencies offered by General Purpose performance mode file systems\. 

## Throughput modes<a name="throughput-modes"></a>

There are two throughput modes to choose from for your file system, Bursting Throughput and Provisioned Throughput\. With *Bursting Throughput* mode, throughput on Amazon EFS scales as the size of your file system in the standard storage class grows\. For more information about EFS storage classes, see [Managing EFS storage classes](storage-classes.md)\. With *Provisioned Throughput *mode, you can instantly provision the throughput of your file system \(in MiB/s\) independent of the amount of data stored\.

**Note**  
You can decrease your file system throughput in Provisioned Throughput mode as long as it’s been more than 24 hours since the last decrease\. Additionally, you can change between Provisioned Throughput mode and the default Bursting Throughput mode as long as it’s been more than 24 hours since the last throughput mode change\.

### Throughput scaling with bursting mode<a name="bursting"></a>

With Bursting Throughput mode, a file system's throughput scales as the amount of data stored in the standard storage class grows\. File\-based workloads are typically spiky, driving high levels of throughput for short periods of time, and low levels of throughput the rest of the time\. To accommodate this, Amazon EFS is designed to burst to high throughput levels for periods of time\.

All EFS file systems, regardless of size, can burst to 100 MiB/s of metered throughput\. When calculating a file system's [metered throughput](#read-write-throughput), Amazon EFS meters read requests at one\-third the rate of write requests\. File systems that have more than 1 TiB in the standard storage class can burst to 100 MiB/s per TiB of data stored in the file system\. For example, a 10 TiB file system can burst to 1,000 MiB/s of metered throughput \(`10 TiB x 100 MiB/s/TiB`\)\. 

The portion of time that a file system can burst is determined by its size\. The bursting model is designed so that typical file system workloads can burst virtually anytime they need to\. For file systems using Bursting Throughput mode, the allowed throughput is determined based on the amount of the data stored in the Standard storage class only\. For more information about EFS storage classes, see [Managing EFS storage classes](storage-classes.md)\. 

#### Amazon EFS burst credits<a name="efs-burst-credits"></a>

Amazon EFS uses a credit system to determine when file systems can burst\. Each file system earns credits over time at a baseline rate that is determined by the size of the file system that is stored in the standard storage class\. A file system uses credits whenever it reads or writes data\. The baseline rate is 50 MiB/s per TiB of storage \(equivalently, 50 KiB/s per GiB of storage\)\. Because Amazon EFS meters read operations at a one\-third the rate of other operations toward the baseline rate, an EFS file system can drive up to 150 KiB/s per GiB of read throughput, or 50 KiB/s per GiB of write throughput at this baseline rate\.

A file system can drive throughput at its baseline metered rate continuously\. A file system accumulates burst credits whenever it is inactive or driving throughput below its baseline metered rate\. Accumulated burst credits give the file system the ability to drive throughput above its baseline rate\.

For example, a 100 GiB file system can burst \(at 100 MiB/s\) for 5 percent of the time if it's inactive for the remaining 95 percent\. Over a 24\-hour period, the file system earns 432,000 MiB worth of credit, which can be used to burst at 100 MiB/s for 72 minutes\. 

File systems larger than 1 TiB can always burst for up to 50 percent of the time if they are inactive for the remaining 50 percent\.

 The following table provides examples of bursting behavior\.


****  

| File system size | Burst throughput | Baseline throughput | 
| --- | --- | --- | 
| A 100\-GiB file system can\.\.\. |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  | 
| A 1\-TiB file system can\.\.\. |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  | 
| A 10\-TiB file system can\.\.\. |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  | 
| Generally, a larger file system can\.\.\. |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  | 

**Note**  
Amazon EFS gives a [metered throughput](#read-write-throughput) of 1 MiB/s to all file systems, even if the baseline rate is lower\.  
The file system size used to determine the baseline and burst rates is the metered size available through the `DescribeFileSystems` operation\.  
File systems can earn credits up to a maximum credit balance of 2\.1 TiB for file systems smaller than 1 TiB, or 2\.1 TiB per TiB stored for file systems larger than 1 TiB\. This approach implies that file systems can accumulate enough credits to burst for up to 12 hours continuously\.

The following table provides more detailed examples of bursting behavior for file systems of different sizes\.


****  

|  File system size \(GiB\)  | Baseline [metered throughput](#read-write-throughput) \(MiB/s\) | Burst metered throughput \(MiB/s\)  | Maximum burst duration \(Min/Day\)  |  % of Time file system can burst \(per day\) | 
| --- | --- | --- | --- | --- | 
| 10 | 0\.5\* | 100 | 7\.2 | 0\.5% | 
| 256 | 12\.5 | 100 | 180 | 12\.5% | 
| 512 | 25\.0 | 100 | 360 | 25\.0% | 
| 1024 | 50\.0 | 100 | 720  | 50\.0% | 
| 1536 | 75\.0 | 150 | 720  | 50\.0% | 
| 2048 | 100\.0 | 200 | 720  | 50\.0% | 
| 3072 | 150\.0 | 300 | 720  | 50\.0% | 
| 4096 | 200\.0 | 400 | 720  | 50\.0% | 

**Note**  
\* For file systems smaller than 20 GiB, minimum [metered throughput](#read-write-throughput) is 1 MiB/s\.  
As previously mentioned, new file systems have an initial burst credit balance of 2\.1 TiB\. With this starting balance, you can burst at 100 MB/s metered throughput for 6\.12 hours without spending any credits that you're earning from your storage\. This starting formula is calculated as `2.1 x 1024 x (1024/100/3600)` to get 6\.116 hours, rounded up to 6\.12\. 

##### Managing burst credits<a name="managecredits"></a>

When a file system has a positive burst credit balance, it can drive its burst rate\. You can see the burst credit balance for a file system by viewing the `BurstCreditBalance` Amazon CloudWatch metric for Amazon EFS\. For more information about accessing this and other metrics, see [Monitoring Amazon EFS](monitoring_overview.md)\.

The bursting capability \(both in terms of length of time and burst rate\) of a file system is directly related to its size\. Larger file systems can burst at larger rates for longer periods of time\. In some cases, your application might need to burst more \(that is, you might find that your file system is running out of burst credits\. In these cases, you should increase the size of your file system, or switch to Provisioned Throughput mode\.

Use your historical throughput patterns to calculate the file system size you need to sustain the level of activity that you want\. The following steps outline how to do this\.

**To calculate the file system size that you need to sustain the level activity that you want**

1. Identify your throughput needs by looking at your historical usage\. From the [Amazon CloudWatch console](https://console.aws.amazon.com/cloudwatch), check the `sum` statistic of the `TotalIOBytes` metric with daily aggregation, for the past 14 days\. Identify the day with the largest value for `TotalIOBytes`\.

1. Divide this number by 24 hours, 60 minutes, 60 seconds, and 1024 bytes to get the average KiB/second your application required for that day\.

1. Calculate the file system size \(in GiB\) required to sustain this average throughput by dividing the average throughput number \(in KiB/s\) by the baseline throughput number \(50 KiB/s/GiB\) that EFS provides\.

### Specifying throughput with Provisioned mode<a name="provisioned-throughput"></a>

Provisioned Throughput mode is available for applications with high throughput to storage \(MiB/s per TiB\) ratios, or with requirements greater than those allowed by the Bursting Throughput mode\. For example, say you're using Amazon EFS for development tools, web serving, or content management applications where the amount of data in your file system is low relative to throughput demands\. Your file system can now get the high levels of throughput your applications require without having to pad your file system\.

Additional charges are associated with using Provisioned Throughput mode\. Using Provisioned Throughput mode, you're billed for the storage that you use and for the throughput that you provision above what you're provided\. The amount of throughput that you are provided is based on the amount of data stored in the Standard storage class\. For more information about EFS storage classes, see [Managing EFS storage classes](storage-classes.md)\. For more information on pricing, see [Amazon EFS Pricing](https://aws.amazon.com/efs/pricing/)\. 

Throughput limits remain the same, regardless of the throughput mode you choose\. For more information on these limits, see [Amazon EFS quotas that you can increase](limits.md#soft-limits)\.

If your file system is in the Provisioned Throughput mode, you can increase the Provisioned Throughput of your file system as often as you want\. You can decrease your file system throughput in Provisioned Throughput mode as long as it's been more than 24 hours since the last decrease\. Additionally, you can change between Provisioned Throughput mode and the default Bursting Throughput mode as long as it’s been more than 24 hours since the last throughput mode change\.

If your file system's metered size provides a higher baseline rate than the amount of throughput you provisioned, your file system follows the default Amazon EFS Bursting Throughput model\. You don't incur charges for Provisioned Throughput below your file system's entitlement in Bursting Throughput mode\. For more information, see [Throughput scaling with bursting mode](#bursting)\. 

### Using the right throughput mode<a name="using-throughputmode"></a>

By default, we recommend that you run your application in the Bursting Throughput mode\. If you experience performance issues, check the `BurstCreditBalance` CloudWatch metric\. If the value of the `BurstCreditBalance` metric is either zero or steadily decreasing, Provisioned Throughput is right for your application\.

If you're planning to migrate large amounts of data into your file system, consider switching to Provisioned Throughput mode\. In this case, you can provision a higher throughput beyond your allotted burst capability to accelerate loading data\. Following the migration, consider lowering the amount of provisioned throughput or switch to Bursting Throughput mode for normal operations\. 

Compare the [metered throughput](#read-write-throughput) that you are driving your file system at, measured by the `MeteredIOBytes` CloudWatch metric, to the `PermittedThroughput` metric\. If the calculated average throughput that you're driving the file system to \(`MeteredIOBytes`\) is less than the `PermittedThroughput`, consider lowering the amount of provisioned throughput to lower costs\. For more information about monitoring your file system using EFS CloudWatch metrics, see [Amazon CloudWatch metrics for Amazon EFS](efs-metrics.md)\.

In some cases, your calculated average throughput during normal operations might be at or below the ratio of metered throughput to storage capacity ratio for Bursting Throughput mode\. That ratio is 50 MiB/s per TiB of data stored\. In such cases, consider switching to Bursting Throughput mode\. In other cases, the calculated average throughput during normal operations might be above this ratio\. In these cases, consider lowering the provisioned throughput to a point between your current provisioned throughput and the calculated average throughput during normal operations\. 

You can change the throughput mode of your file system using the AWS Management Console, the AWS CLI, or the EFS API\. With the CLI, use the `update-file-system` action\. With the EFS API, use the [UpdateFileSystem](API_UpdateFileSystem.md) operation\. 

**Note**  
As previously mentioned, new file systems have an initial burst credit balance of 2\.1 TB\. With this starting balance, you can burst at 100 MB/s for 6\.12 hours without spending any credits that you're earning from your storage\. This starting formula is calculated as `2.1 x 1024 x (1024/100/3600)` to get 6\.116 hours, rounded up to 6\.12\. 

## Related topics<a name="perf-related"></a>
+ [On\-premises performance considerations](performance-onpremises.md)
+ [Amazon EFS performance tips](performance-tips.md)
+ [Metering: How Amazon EFS reports file system and object sizes](metered-sizes.md)
+ [Troubleshooting Amazon EFS](troubleshooting.md)