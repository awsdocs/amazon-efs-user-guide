# Amazon EFS Performance<a name="performance"></a>

This topic provides an overview of Amazon EFS performance, discusses the two performance modes \(General Purpose and Max I/O\) available in Amazon EFS, reviews the Amazon EFS bursting model, and outlines some useful performance tips\.

## Performance Overview<a name="performance-overview"></a>

Amazon EFS file systems are distributed across an unconstrained number of storage servers, enabling file systems to grow elastically to petabyte scale and allowing massively parallel access from Amazon EC2 instances to your data\. The distributed design of Amazon EFS avoids the bottlenecks and constraints inherent to traditional file servers\.

This distributed data storage design means that multithreaded applications and applications that concurrently access data from multiple Amazon EC2 instances can drive substantial levels of aggregate throughput and IOPS\. Big data and analytics workloads, media processing workflows, content management, and web serving are examples of these applications\.

In addition, Amazon EFS data is distributed across multiple Availability Zones \(AZs\), providing a high level of durability and availability\. The following tables compare high\-level performance and storage characteristics for Amazon’s file and block cloud storage services\.


**Performance Comparison – Amazon EFS and Amazon EBS**  

|  | Amazon EFS | Amazon EBS Provisioned IOPS | 
| --- | --- | --- | 
| Per\-operation latency | Low, consistent latency\. | Lowest, consistent latency\. | 
| Throughput scale | 10\+ GB per second\. | Up to 2 GB per second\. | 


**Storage Characteristics Comparison – Amazon EFS and Amazon EBS**  

|  | Amazon EFS | Amazon EBS Provisioned IOPS | 
| --- | --- | --- | 
| Availability and durability | Data is stored redundantly across multiple AZs\. | Data is stored redundantly in a single AZ\. | 
| Access | Up to thousands of Amazon EC2 instances, from multiple AZs, can connect concurrently to a file system\. | A single Amazon EC2 instance in a single AZ can connect to a file system\. | 
| Use cases | Big data and analytics, media processing workflows, content management, web serving, and home directories\. | Boot volumes, transactional and NoSQL databases, data warehousing, and ETL\. | 

The distributed nature of Amazon EFS enables high levels of availability, durability, and scalability\. This distributed architecture results in a small latency overhead for each file operation\. Due to this per\-operation latency, overall throughput generally increases as the average I/O size increases, because the overhead is amortized over a larger amount of data\. Amazon EFS supports highly parallelized workloads \(for example, using concurrent operations from multiple threads and multiple Amazon EC2 instances\), which enables high levels of aggregate throughput and operations per second\.

## Amazon EFS Use Cases<a name="performance-usecases"></a>

Amazon EFS is designed to meet the performance needs of the following use cases\.

### Big Data and Analytics<a name="perf-bigdata"></a>

Amazon EFS provides the scale and performance required for big data applications that require high throughput to compute nodes coupled with read\-after\-write consistency and low\-latency file operations\.

### Media Processing Workflows<a name="perf-media"></a>

Media workflows like video editing, studio production, broadcast processing, sound design, and rendering often depend on shared storage to manipulate large files\. A strong data consistency model with high throughput and shared file access can cut the time it takes to perform these jobs and consolidate multiple local file repositories into a single location for all users\.

### Content Management and Web Serving<a name="perf-contentserve"></a>

Amazon EFS provides a durable, high throughput file system for content management systems that store and serve information for a range of applications like websites, online publications, and archives\.

### Home Directories<a name="perf-directories"></a>

Amazon EFS can provide storage for organizations that have many users that need to access and share common data sets\. An administrator can use Amazon EFS to create a file system accessible to people across an organization and establish permissions for users and groups at the file or directory level\.

### File System Syncing to Amazon EFS<a name="perf-file-sync"></a>

Amazon EFS File Sync provides efficient, high\-performance parallel data sync that is tolerant to unreliable and high latency networks\. Using this data sync, you can easily and efficiently sync files from an existing file system into Amazon EFS\. For more information, see [Amazon EFS File Sync](get-started-file-sync.md)\. 

## Performance Modes<a name="performancemodes"></a>

To support a wide variety of cloud storage workloads, Amazon EFS offers two performance modes\. You select a file system's performance mode when you create it\.

The two performance modes have no additional costs, so your Amazon EFS file system is billed and metered the same, regardless of your performance mode\. For information about file system limits, see [Limits for Amazon EFS File Systems](limits.md#limits-fs-specific)\.

**Note**  
An Amazon EFS file system's performance mode can't be changed after the file system has been created\.

### General Purpose Performance Mode<a name="generalpurpose"></a>

We recommend the General Purpose performance mode for the majority of your Amazon EFS file systems\. General Purpose is ideal for latency\-sensitive use cases, like web serving environments, content management systems, home directories, and general file serving\. If you don't choose a performance mode when you create your file system, Amazon EFS selects the General Purpose mode for you by default\.

### Max I/O Performance Mode<a name="maxio"></a>

File systems in the Max I/O mode can scale to higher levels of aggregate throughput and operations per second with a tradeoff of slightly higher latencies for file operations\. Highly parallelized applications and workloads, such as big data analysis, media processing, and genomics analysis, can benefit from this mode\.

### Using the Right Performance Mode<a name="using-perfmode"></a>

Our recommendation for determining which performance mode to use is as follows:

1. [Create a new file system](gs-step-two-create-efs-resources.md) using the default General Purpose performance mode\.

1. Run your application \(or a use case similar to your application\) for a period of time to test its performance\.

1. Monitor the [PercentIOLimit](monitoring-cloudwatch.md#efs-metrics) Amazon CloudWatch metric for Amazon EFS during the performance test\. For more information about accessing this and other metrics, see [Amazon CloudWatch Metrics](monitoring_overview.md)\.

If the `PercentIOLimit` percentage returned was at or near 100 percent for a significant amount of time during the test, your application should use the Max I/O performance mode\. Otherwise, it should use the default General Purpose mode\.

## Throughput Scaling in Amazon EFS<a name="bursting"></a>

Throughput on Amazon EFS scales as a file system grows\. Because file\-based workloads are typically spiky—driving high levels of throughput for short periods of time, and low levels of throughput the rest of the time—Amazon EFS is designed to burst to high throughput levels for periods of time\.

All file systems, regardless of size, can burst to 100 MiB/s of throughput, and those over 1 TiB large can burst to 100 MiB/s per TiB of data stored in the file system\. For example, a 10 TiB file system can burst to 1,000 MiB/s of throughput \(`10 TiB x 100 MiB/s/TiB`\)\. The portion of time a file system can burst is determined by its size, and the bursting model is designed so that typical file system workloads will be able to burst virtually any time they need to\.

Amazon EFS uses a credit system to determine when file systems can burst\. Each file system earns credits over time at a baseline rate that is determined by the size of the file system, and uses credits whenever it reads or writes data\. The baseline rate is 50 MiB/s per TiB of storage \(equivalently, 50 KiB/s per GiB of storage\)\.

Accumulated burst credits give the file system permission to drive throughput above its baseline rate\. A file system can drive throughput continuously at its baseline rate, and whenever it's inactive or driving throughput below its baseline rate, the file system accumulates burst credits\.

For example, a 100 GiB file system can burst \(at 100 MiB/s\) for 5 percent of the time if it's inactive for the remaining 95 percent\. Over a 24\-hour period, the file system earns 432,000 MiBs worth of credit, which can be used to burst at 100 MiB/s for 72 minutes\. 

File systems larger than 1 TiB can always burst for up to 50 percent of the time if they are inactive for the remaining 50 percent\.

 The following table provides examples of bursting behavior\.


****  

| File System Size | Aggregate Read/Write Throughput | 
| --- | --- | 
| A 100 GiB file system can\.\.\. |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  | 
| A 1 TiB file system can\.\.\. |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  | 
| A 10 TiB file system can\.\.\. |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  | 
| Generally, a larger file system can\.\.\. |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/performance.html)  | 

**Note**  
The minimum file system size used when calculating the baseline rate is 1 GiB, so all file systems have a baseline rate of at least 50 KiB/s\.
The file system size used when determining the baseline rate and burst rate is the same as the metered size available through the `DescribeFileSystems` operation\.
File systems can earn credits up to a maximum credit balance of 2\.1 TiB for file systems smaller than 1 TiB, or 2\.1 TiB per TiB stored for file systems larger than 1 TiB\. This implies that file systems can accumulate enough credits to burst for up to 12 hours continuously\.
Newly created file systems begin with an initial credit balance of 2\.1 TiB, which enables them to add data at the 100 MiB/s burst rate until they are large enough to run at 100 MiB/s continuously \(that is, 2 TiB\)\.

The following table provides more detailed examples of bursting behavior for file systems of different sizes\.


****  

|  File System Size \(GiB\)  | Baseline Aggregate Throughput \(MiB/s\) | Burst Aggregate Throughput \(MiB/s\)  | Maximum Burst Duration \(Min/Day\)  |  % of Time File System Can Burst \(Per Day\) | 
| --- | --- | --- | --- | --- | 
| 10 | 0\.5 | 100 | 7\.2 | 0\.5% | 
| 256 | 12\.5 | 100 | 180 | 12\.5% | 
| 512 | 25\.0 | 100 | 360 | 25\.0% | 
| 1024 | 50\.0 | 100 | 720  | 50\.0% | 
| 1536 | 75\.0 | 150 | 720  | 50\.0% | 
| 2048 | 100\.0 | 200 | 720  | 50\.0% | 
| 3072 | 150\.0 | 300 | 720  | 50\.0% | 
| 4096 | 200\.0 | 400 | 720  | 50\.0% | 

**Note**  
As previously mentioned, new file systems have an initial burst credit balance of 2\.1 TiB\. With this starting balance, you can burst at 100 MB/s for 6\.12 hours \(which is calculated by `2.1 x 1024 x (1024/100/3600)` to get 6\.116 hours, rounded up to 6\.12\) without spending any credits that you’re earning from your storage\.

### Managing Burst Credits<a name="managecredits"></a>

When a file system has a positive burst credit balance, it can burst\. You can see the burst credit balance for a file system by viewing the `BurstCreditBalance` Amazon CloudWatch metric for Amazon EFS\. For more information about accessing this and other metrics, see [Monitoring Amazon EFS](monitoring_overview.md)\.

The bursting capability \(both in terms of length of time and burst rate\) of a file system is directly related to its size\. Larger file systems can burst at larger rates for longer periods of time\. Therefore, if your application needs to burst more \(that is, if you find that your file system is running out of burst credits\), you should increase the size of your file system\.

**Note**  
There’s no provisioning with Amazon EFS, so to make your file system larger you need to add more data to it\.

Use your historical throughput patterns to calculate the file system size you need to sustain your desired level of activity\. The following steps outline how to do this:

1. Identify your throughput needs by looking at your historical usage\. From the [Amazon CloudWatch console](https://console.aws.amazon.com/cloudwatch), check the `sum` statistic of the `TotalIOBytes` metric with daily aggregation, for the past 14 days\. Identify the day with the largest value for `TotalIOBytes`\.

1. Divide this number by 24 hours, 60 minutes, 60 seconds, and 1024 bytes to get the average KiB/second your application required for that day\.

1. Calculate the file system size \(in GB\) required to sustain this average throughput by dividing the average throughput number \(in KB/s\) by the baseline throughput number \(50 KB/s/GiB\) that EFS provides\.

## On\-Premises Performance Considerations<a name="performance-onpremises"></a>

The throughput bursting model for Amazon EFS file systems remains the same whether accessed from your on\-premises servers or your Amazon EC2 instances\. However, when accessing Amazon EFS file data from your on\-premises servers, the maximum throughput is also constrained by the bandwidth of the AWS Direct Connect connection\.

Because of the propagation delay tied to data traveling over long distances, the network latency of an AWS Direct Connect connection between your on\-premises data center and your Amazon VPC can be tens of milliseconds\. If your file operations are serialized, the latency of the AWS Direct Connect connection directly impacts your read and write throughput\. In essence, the volume of data you can read or write during a period of time is bounded by the amount of time it takes for each read and write operation to complete\. To maximize your throughput, parallelize your file operations so that multiple reads and writes are processed by Amazon EFS concurrently\. Standard tools like [GNU parallel](https://www.gnu.org/software/parallel/) enable you to parallelize the copying of file data\.

### Architecting for High Availability<a name="onpremises-availability"></a>

To ensure continuous availability between your on\-premises data center and your Amazon VPC, we recommend configuring two AWS Direct Connect connections\. For more information, see [Step 4: Configure Redundant Connections with AWS Direct Connect](http://docs.aws.amazon.com/directconnect/latest/UserGuide/getstarted.html#RedundantConnections) in the AWS Direct Connect User Guide\.

To ensure continuous availability between your application and Amazon EFS, we recommend that your application be designed to recover from potential connection interruptions\. In general, there are two scenarios for on\-premises applications connected to an Amazon EFS file system; highly available and not highly available\.

If your application is Highly Available \(HA\) and uses multiple on\-premises servers in its HA cluster, ensure that each on\-premises server in the HA cluster connects to a mount target in a different Availability Zone \(AZ\) in your Amazon VPC\. If your on\-premises server can’t access the mount target because the AZ in which the mount target exists becomes unavailable, your application should failover to a server with an available mount target\.

If your application is not highly available, and your on\-premises server can’t access the mount target because the AZ in which the mount target exists becomes unavailable, your application should implement restart logic and connect to a mount target in a different AZ\.

## Amazon EFS Performance Tips<a name="performance-tips"></a>

When using Amazon EFS, keep the following performance tips in mind:
+ **Average I/O Size** – The distributed nature of Amazon EFS enables high levels of availability, durability, and scalability\. This distributed architecture results in a small latency overhead for each file operation\. Due to this per\-operation latency, overall throughput generally increases as the average I/O size increases, because the overhead is amortized over a larger amount of data\.
+ **Simultaneous Connections** – Amazon EFS file systems can be mounted on up to thousands of Amazon EC2 instances concurrently\. If you can parallelize your application across more instances, you can drive higher throughput levels on your file system in aggregate across instances\.
+ **Request Model** – By enabling asynchronous writes to your file system, pending write operations are buffered on the Amazon EC2 instance before they are written to Amazon EFS asynchronously\. Asynchronous writes typically have lower latencies\. When performing asynchronous writes, the kernel uses additional memory for caching\. A file system that has enabled synchronous writes, or one that opens files using an option that bypasses the cache \(for example, O\_DIRECT\), will issue synchronous requests to Amazon EFS and every operation will go through a round trip between the client and Amazon EFS\.
**Note**  
Your chosen request model will have tradeoffs in consistency \(if you're using multiple Amazon EC2 instances\) and speed\.
+ **NFS Client Mount Settings** – Verify that you’re using the recommended mount options as outlined in [Mounting File Systems](mounting-fs.md) and in [Additional Mounting Considerations](mounting-fs-mount-cmd-general.md)\. Amazon EFS supports the Network File System versions 4\.0 and 4\.1 \(NFSv4\) and NFSv4\.0 protocols when mounting your file systems on Amazon EC2 instances\. NFSv4\.1 provides better performance\.
**Note**  
You might want to increase the size of the read and write buffers for your NFS client to 1 MB when you mount your file system\.
+ **Amazon EC2 Instances** – Applications that perform a large number of read and write operations likely need more memory or computing capacity than applications that don't\. When launching your Amazon EC2 instances, choose instance types that have the amount of these resources that your application needs\. Note that the performance characteristics of Amazon EFS file systems are not dependent on the use of EBS\-optimized instances\.
+ **Encryption** – Amazon EFS supports two forms of encryption, encryption in transit and encryption at rest\. This option is for encryption at rest\. Choosing to enable either or both types of encryption for your file system has a minimal effect on I/O latency and throughput\.

For information about the Amazon EFS limits for total file system throughput, per\-instance throughput, and operations per second in General Purpose performance mode, see [Amazon EFS Limits](limits.md)\.

## Related Topics<a name="perf-related"></a>
+ [Metering – How Amazon EFS Reports File System and Object Sizes](metered-sizes.md)
+ [Troubleshooting Amazon EFS](troubleshooting.md)