# Amazon EFS performance<a name="performance"></a>

Amazon EFS provides a serverless, set\-and\-forget elastic file system that you can access from any compute service within AWS and on\-premises, including:
+ Amazon Elastic Compute Cloud \(Amazon EC2\)
+ Amazon Elastic Container Service \(Amazon ECS\)
+ Amazon Elastic Kubernetes Service \(Amazon EKS\)
+ AWS Fargate
+ AWS Lambda

Amazon EFS delivers more than 10 gibibytes per second \(GiBps\) of throughput over 500,000 IOPS, and sub\-millisecond or low single digit millisecond latencies\. 

The following sections provide an overview of Amazon EFS performance, and describe how your file system configuration impacts key performance dimensions\. We also provide some important tips and recommendations for optimizing the performance of your file system\.

## Performance summary<a name="performance-overview"></a>

File system performance is typically measured using the dimensions of latency, throughput, and I/O operations per second \(IOPS\)\. Amazon EFS performance across these dimensions depends on your file system's configuration\. The following configurations impact the performance of an Amazon EFS file system:
+ **Storage class** – EFS One Zone or EFS Standard
+ **Performance mode** – General Purpose or Max I/O
+ **Throughput mode** – Bursting or Provisioned

The following table illustrates Amazon EFS file system performance for the available combinations of storage class and performance mode settings\.


**File system performance for storage class and performance mode combinations**  

|  | Latency1 | Maximum IOPS | Maximum throughput | 
| --- |--- |--- |--- |
| **File system configuration – Storage class and performance mode** | **Read operations** | **Write operations** | **Read operations** | **Write operations** | **Per\-file\-system read**2 | **Per\-file\-system write**2 | **Per\-client read/write** | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| **One Zone storage and General Purpose** | As low as 600 microseconds \(µs\) | Low single\-digit milliseconds | 35,000 | 7,000 | 3 – 5 GiBps | 1 – 3 GiBps | 512 MiBps | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| **Standard storage and General Purpose** | As low as 600 µs | Low single\-digit milliseconds | 35,000 | 7,000 | 3 – 5 GiBps | 1 – 3 GiBps | 512 MiBps | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| **Standard storage and Max I/O** | Single\-digit milliseconds | Single\-digit to double\-digit milliseconds | >500,000 | >100,000 | 3 – 5 GiBps | 1 – 3 GiBps | 512 MiBps | 
| --- |--- |--- |--- |--- |--- |--- |--- |

**Note**  
Footnotes:  
Latencies for file data reads and writes to the cost\-optimized storage classes \(Standard\-IA and One Zone\-IA\) are double\-digit milliseconds\.
Maximum read and write throughput depend on the AWS Region and on the file system's throughput mode \(Bursting or Provisioned\)\. For more information, see the table of [default throughput quotas](limits.md#soft-limits) for Bursting and Provisioned Throughput modes\.  
Throughput in excess of an AWS Region's maximum throughput requires a throughput quota increase\. Any request for additional throughput is considered on a case\-by\-case basis by the Amazon EFS service team\. Approval might depend on your type of workload\. To learn more about requesting quota increases, see [Amazon EFS quotas and limits](limits.md)\.

## Storage classes and performance<a name="storage-perf"></a>

Amazon EFS uses two types of storage classes: 
+ **EFS One Zone storage classes** – EFS One Zone and EFS One Zone\-Infrequent Access \(EFS One Zone\-IA\)\. The EFS One Zone storage classes replicate data within a single Availability Zone\.
+ **EFS Standard storage classes** – EFS Standard and EFS Standard\-IA\. The EFS Standard storage classes replicate data across multiple Availability Zones \(Multi\-AZ\)\.

First\-byte latency when reading from or writing to either of the IA storage classes is higher than that for the EFS Standard or EFS One Zone storage classes\.

For more information about EFS storage classes, see [EFS storage classes](storage-classes.md)\. 

## Performance modes<a name="performancemodes"></a>

Amazon EFS offers two performance modes, General Purpose and Max I/O:
+ **General Purpose mode** supports up to 35,000 IOPS and has the lowest per\-operation latency\. File systems with EFS One Zone storage classes always use General Purpose performance mode\. For file systems with EFS Standard storage classes, you can use either the default General Purpose performance mode or the Max I/O performance mode\.
+ **Max I/O mode** supports 500,000\+ IOPS and has higher per\-operation latencies when compared to General Purpose mode\. 

You set the performance mode when you create a file system, and you can't change it after it is created\.

We recommend using General Purpose performance mode for the vast majority of applications\. If you are not sure which performance mode to use, choose the General Purpose performance mode\. To help ensure that your workload stays within the IOPS limit available to file systems using General Purpose mode, you can monitor the `PercentIOLimit` CloudWatch metric\. For more information, see [Amazon CloudWatch metrics for Amazon EFS](efs-metrics.md)\.

Applications can scale their IOPS elastically up to the limit associated with the performance mode\. You are not billed separately for IOPS; they are included in a file system's throughput accounting\. Every Network File System \(NFS\) request is accounted for as 4 KB of throughput, or its actual request and response size, whichever is larger\. For example, a file system that can drive 100 MBps of throughput can drive up to 25,600 4\-KB writes per second \(100 MBps divided by 4 KB per request = 25,600 requests per second\)\.

## Throughput modes<a name="throughput-modes"></a>

A file system's throughput mode determines the throughput available to your file system\. Amazon EFS offers two throughput modes, Bursting Throughput and Provisioned Throughput\. Read throughput is discounted to allow you to drive higher read throughput than write throughput\. Depending on the AWS Region, the discount for reads is between 1\.66 and 3x\. For more information, see the table of [default throughput quotas](limits.md#soft-limits) for Bursting and Provisioned Throughput modes\. Discounting reduces throughput metered for reads, and does not impact writes or burst credit accruals\. Furthermore, read discounting never reduces the throughput metered for a single NFS request below a minimum request size of 4 KB\.

### Understanding metered throughput<a name="read-write-throughput"></a>

All Amazon EFS file systems have an associated metered throughput\. For file systems using Provisioned Throughput mode, the metered throughput is determined by the amount of provisioned throughput\. For file systems using Bursting Throughput mode, the metered throughput is determined by the amount of data stored in the EFS Standard or EFS One Zone storage class\. 

Read requests and write requests are metered at different rates\. Amazon EFS meters read requests at one\-third the rate of other requests\.

**Example of EFS metered throughput**  
For example, if you are driving 30 mebibytes per second \(MiBps\) of both read throughput and write throughput, the read portion counts as 10 MiBps of metered throughput, the write portion counts as 30 MiBps, and the combined metered throughput is 40 MiBps\. This combined throughput adjusted for metering rates is reflected in the `MeteredIOBytes` Amazon CloudWatch metric\. For more information, see [Amazon CloudWatch metrics for Amazon EFS](efs-metrics.md)\.

### Bursting Throughput mode<a name="bursting"></a>

Bursting Throughput mode is the default Amazon EFS throughput mode\. It is a good fit for traditional applications that have a bursty throughput pattern\. When throughput is low, Bursting Throughput mode uses burst buckets to save burst credits\. When throughput is higher, it uses burst credits\.

In Bursting Throughput mode, file system throughput is proportional to the file system size, up to a maximum that depends on the Amazon EFS Region\. For more information about per\-Region limits, see the table of [default throughput quotas](limits.md#soft-limits) for Bursting and Provisioned Throughput modes\. 

When burst credits are available, a file system can drive up to 100 MBps per terabyte \(TB\) of storage, with a minimum of 100 MBps\. If no burst credits are available, a file system can drive up to 50 MBps per TB of storage with a minimum of 1 MBps\.

Read and write throughput are metered, and burst credits are deducted from the burst credit balance for metered throughput\. Burst credits accrue in proportion to the file system's size at the file system's base rate\. 50 MBps of burst credits are accrued for every TB of storage\. Whenever a file system consumes less than its base rate, it accrues burst credits\. Whenever a file system consumes more than its base rate, it consumes burst credits\. The metered size of [`ValueInStandard`](metered-sizes.md#metered-sizes-fs) is used to determine your I/O throughput baseline and burst rates\.

### Provisioned Throughput mode<a name="provisioned-throughput"></a>

For applications that have a relatively constant throughput, we recommend Provisioned Throughput mode\. In Provisioned Throughput mode, you specify a level of throughput, and your file system is able to drive this level of throughput independent of the file system's size or burst credit balance\. You are charged for the configured amount of throughput, but only in excess of the base rate that you would get based on the amount of storage that you have if your file system were using Bursting Throughput mode\.

#### Switching throughput modes<a name="switch-throughput-mode"></a>

You can switch an existing file system's throughput mode, with the restriction that you can make only one restricted change in a 24\-hour period\. The following are considered restricted changes:
+ Changing a file system's throughput mode \(from Provisioned Throughput to Bursting Throughput, or from Bursting Throughput to Provisioned Throughput\)\.
+ Reducing the amount of provisioned throughput in Provisioned Throughput mode\.