# Amazon CloudWatch metrics for Amazon EFS<a name="efs-metrics"></a>

Amazon EFS metrics use the `EFS` namespace and provide metrics for a single dimension, `FileSystemId`\. A file system's ID can be found in the Amazon EFS console, and it takes the form of `fs-abcdef0123456789a`\.

The `AWS/EFS` namespace includes the following metrics\.

**`TimeSinceLastSync`**  
Shows the amount of time that has passed since the last successful sync to the destination file system in a replication configuration\. Any changes to data on the source file system that occurred before the `TimeSinceLastSync` value have been successfully replicated\. Any changes on the source that occurred after `TimeSinceLastSync` might not be fully replicated\. For more information, see [Amazon EFS replication](efs-replication.md)\.  
Units: Seconds

**`PercentIOLimit`**  
Shows how close a file system is to reaching the I/O limit of the General Purpose performance mode\. If this metric is at 100 percent more often than not, consider moving your application to a file system using the Max I/O performance mode\.  
This metric is only submitted for file systems using the General Purpose performance mode\.
Units: Percent

**`BurstCreditBalance`**  
The number of burst credits that a file system has\. Burst credits allow a file system to burst to throughput levels above a file system’s baseline level for periods of time\. For more information, see [Bursting Throughput mode](performance.md#bursting)\.  
The `Minimum` statistic is the smallest burst credit balance for any minute during the period\. The `Maximum` statistic is the largest burst credit balance for any minute during the period\. The `Average` statistic is the average burst credit balance during the period\.   
Units: Bytes  
Valid statistics: `Minimum`, `Maximum`, `Average`

**`PermittedThroughput`**  
The maximum amount of throughput that a file system can drive\. For file systems in the Provisioned Throughput mode, if the amount of data stored in the EFS Standard storage class allows your file system to drive a higher throughput than you provisioned, this metric reflects the higher throughput instead of the provisioned amount\. For file systems in Bursting Throughput mode, this value is a function of the file system size and `BurstCreditBalance`\. For more information, see [Amazon EFS performance](performance.md)\.  
The `Minimum` statistic is the smallest throughput permitted for any minute during the period\. The `Maximum` statistic is the highest throughput permitted for any minute during the period\. The `Average` statistic is the average throughput permitted during the period\.   
Read operations are metered at one\-third the rate of other operations\.
Units: Bytes per second  
Valid statistics: `Minimum`, `Maximum`, `Average`

**`MeteredIOBytes`**  
The number of metered bytes for each file system operation, including data read, data write, and metadata operations, with read operations metered at one\-third the rate of other operations\.  
You can create a [CloudWatch metric math expression](monitoring-metric-math.md#metric-math-throughput-utilization) that compares `MeteredIOBytes` to `PermittedThroughput`\. If these values are equal, then you are consuming the entire amount of throughput allocated to your file system\. In this situation, you might consider changing the file system's throughput mode to Provisioned Throughput to get higher throughput\.  
The `Sum` statistic is the total number of metered bytes associated with all file system operations\. The `Minimum` statistic is the size of the smallest operation during the period\. The `Maximum` statistic is the size of the largest operation during the period\. The `Average` statistic is the average size of an operation during the period\. The `SampleCount` statistic provides a count of all operations\.  
Units:  
+ Bytes for `Minimum`, `Maximum`, `Average`, and `Sum` statistics\.
+ Count for `SampleCount`\.
Valid statistics: `Minimum`, `Maximum`, `Average`, `Sum`, `SampleCount`

**`TotalIOBytes`**  
The actual number of bytes for each file system operation, including data read, data write, and metadata operations\. This is the actual amount that your application is driving, and not the throughput the file system is being metered at\. It might be higher than the numbers shown in `PermittedThroughput`\.   
The `Sum` statistic is the total number of bytes associated with all file system operations\. The `Minimum` statistic is the size of the smallest operation during the period\. The `Maximum` statistic is the size of the largest operation during the period\. The `Average` statistic is the average size of an operation during the period\. The `SampleCount` statistic provides a count of all operations\.  
To calculate the average operations per second for a period, divide the `SampleCount` statistic by the number of seconds in the period\. To calculate the average throughput \(bytes per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. 
Units:  
+ Bytes for `Minimum`, `Maximum`, `Average`, and `Sum` statistics\.
+ Count for `SampleCount`\.
Valid statistics: `Minimum`, `Maximum`, `Average`, `Sum`, `SampleCount`

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

**`ClientConnections`**  
The number of client connections to a file system\. When using a standard client, there is one connection per mounted Amazon EC2 instance\.  
To calculate the average `ClientConnections` for periods greater than one minute, divide the `Sum` statistic by the number of minutes in the period\.
Units: Count of client connections  
Valid statistics: `Sum`

**`StorageBytes`**  
The size of the file system in bytes, including the amount of data stored in the EFS Standard and EFS Standard–Infrequent Access \(EFS Standard\-IA\) storage classes\. This metric is emitted every 15 minutes to CloudWatch\. For more information about storage classes, see [EFS storage classes](storage-classes.md)\.  
The `StorageBytes` metric has three dimensions:  
+ `Total` is the latest known metered size \(in bytes\) of data stored in the file system, including both storage classes\.
+ `Standard` is the latest known metered size \(in bytes\) of data stored in the EFS Standard storage class\.
+ `IA` is the latest known metered size \(in bytes\) of data stored in the EFS Standard\-IA storage class\.
Units: Bytes  
`StorageBytes` is displayed on the Amazon EFS console **File system metrics** page using base 1024 units \(kibibytes, mebibytes, gibibytes, and tebibytes\)\.

## Bytes reported in CloudWatch<a name="cloudwatch-bytes"></a>

Amazon EFS CloudWatch metrics are reported as raw *bytes*\. Bytes are not rounded to either a decimal or binary multiple of the unit\. Keep this in mind when calculating your burst rate using the data you get from the metrics\. For more information about bursting, see [Bursting Throughput mode](performance.md#bursting)\.