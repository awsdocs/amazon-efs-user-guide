# EFS storage classes<a name="storage-classes"></a>

First\-byte latency when reading from or writing to the IA storage class is higher than that for the Standard storage class\.  For file systems using Bursting Throughput, the allowed throughput is determined based on the amount of the data stored in the Standard storage class only\. For file systems using Provisioned Throughput mode, you're billed for the throughput provisioned above what you are provided based on the amount of data that is in the Standard storage class\. For more information on EFS performance, see [Throughput modes](performance.md#throughput-modes)\.

Amazon EFS offers a range of storage classes that are designed for different use cases\. These include EFS Standard, EFS Standard–Infrequent Access \(Standard\-IA\), EFS One Zone, and EFS One Zone–Infrequent Access \(EFS One Zone\-IA\)\. The following sections provide details of these storage classes\.

## Amazon EFS Standard and Standard–IA storage classes<a name="regional-sc"></a>

EFS Standard and Standard\-IA storage classes are regional storage classes that are designed to provide continuous availability to data, even when one or more Availability Zones in an AWS Region are unavailable\. They offer the highest levels of availability and durability by storing file system data and metadata redundantly across multiple geographically separated Availability Zones within a Region\.

The EFS Standard storage class is used for frequently accessed files\. It is the storage class to which customer data is initially written for Standard storage classes\.

The Standard–IA storage class reduces storage costs for files that are not accessed every day\. It does this without sacrificing the high availability, high durability, elasticity, and POSIX file system access that Amazon EFS provides\. We recommend Standard\-IA storage if you need your full dataset to be readily accessible and want to automatically save on storage costs for files that are less frequently accessed\. Examples include keeping files accessible to satisfy audit requirements, performing historical analysis, or performing backup and recovery\. Standard\-IA storage is compatible with all Amazon EFS features, and is available in all AWS Regions where Amazon EFS is available\.

## Amazon EFS One Zone and EFS One Zone–IA storage classes<a name="one-zone-sc"></a>

EFS One Zone and One Zone–IA storage classes are designed to provide continuous availability to data within a single Availability Zone\. The EFS One Zone storage classes store file system data and metadata redundantly within a single Availability Zone in an AWS Region\. Because they store data in a single AWS Availability Zone, data that is stored in these storage classes might be lost in the event of a disaster or other fault that affects all copies of the data within the Availability Zone, or in the event of Availability Zone destruction\. 

For added data protection, Amazon EFS automatically backs up file systems using One Zone storage classes with AWS Backup\. You can restore file system backups to any operational Availability Zone within an AWS Region, or you can restore them to a different Region\. Amazon EFS file system backups that are created and managed using AWS Backup are replicated to three Availability Zones and are designed for 11 9’s durability\. For more information, see [Resilience in AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/disaster-recovery-resiliency.html)\.

EFS One Zone–Standard is used for frequently accessed files\. It is the storage class to which customer data is initially written for One Zone storage classes\.

The EFS One Zone–IA storage class reduces storage costs for files that are not accessed every day\. We recommend EFS One Zone–IA storage if you need your full dataset to be readily accessible and want to automatically save on storage costs for files that are less frequently accessed\. One Zone\-IA storage is compatible with all Amazon EFS features, and is available in all AWS Regions where Amazon EFS is available\.

## Infrequent access performance<a name="infreq-access-perf"></a>

First\-byte latency when reading from or writing to either of the IA storage classes is higher than that for the EFS Standard or EFS One Zone storage classes\.

 You can move files from an IA storage class to a frequently accessed storage class by copying them to another location on your file system\. If you want your files to remain in the frequently accessed storage class, stop Lifecycle Management on the file system, and then copy your files\.

## Comparing Amazon EFS storage classes<a name="sc-compare"></a>

The following table compares the storage classes, including their availability, durability, minimum storage duration, and other considerations\.


| Storage class | Designed for | Durability \(designed for\) | Availability | Availability zones | Other considerations | 
| --- | --- | --- | --- | --- | --- | 
|  EFS Standard  |  Frequently accessed data requiring the highest durability and availability\.  | 99\.999999999% \(11 9's\) | 99\.99% | >=3 | None | 
|  EFS Standard–Infrequent Access \(IA\)  |  Long lived, infrequently accessed data requiring the highest durability and availability\.  | 99\.999999999% \(11 9's\) | 99\.99% | >=3 | Per GB retrieval fees apply\. | 
|  EFS One Zone  |  Frequently accessed data that doesn’t require highest levels of durability and availability\.  | 99\.999999999% \(11 9's\)\* | 99\.90% | 1 | Not resilient to the loss of the Availability Zone\. | 
|  EFS One Zone\-IA  | Long lived, infrequently accessed data that doesn’t require highest levels of durability and availability\. | 99\.999999999% \(11 9's\)\* | 99\.90% | 1 | Not resilient to the loss of the Availability Zone\. Per GB retrieval fees apply\. | 

\*Because EFS One Zone storage classes store data in a single AWS Availability Zone, data stored in these storage classes may be lost in the event of a disaster or other fault that affects all copies of the data within the Availability Zone, or in the event of Availability Zone destruction\.

## Storage class pricing<a name="billing"></a>

You are billed for the amount of data in each storage class\. You are also billed for data access when files in IA storage are read and when files are transitioned to IA storage from EFS Standard or One Zone storage\. The AWS bill displays the capacity for each storage class and the metered access against the file system's IA storage class \(Standard\-IA or One Zone\-IA\)\. To learn more, see [Amazon EFS Pricing](https://aws.amazon.com/efs/pricing)\.

For file systems using Bursting Throughput, the allowed throughput is determined based on the amount of the data stored in the EFS Standard and EFS One Zone storage classes only\. For file systems using Provisioned Throughput mode, you're billed for the throughput provisioned above what you are provided based on the amount of data that is in the EFS Standard and EFS One Zone storage classes\. For more information on EFS performance, see [Throughput modes](performance.md#throughput-modes)\. 

**Note**  
You don't incur data access charges when using AWS Backup to back up lifecycle management\-enabled EFS file systems\. To learn more about AWS Backup and EFS lifecycle management, see [EFS storage classes](awsbackup.md#backups-storage-classes)\.

## Viewing storage class size<a name="view-storage-class-size"></a>

You can view how much data is stored in each storage class of your file system using the Amazon EFS console, the AWS CLI, or the EFS API\.

### Viewing storage data size in the Amazon EFS console<a name="billing-metric"></a>

The **Metered size** tab on the **File system details** page displays the current metered size of the file system in binary multiples of bytes \(kibibytes, mebibytes, gibibytes, and tebibytes\)\. The metric is emitted every 15 minutes and lets you view your file system's metered size over time\. **Metered size** displays the following information for the file system storage size:
+ **Total size** is the size \(in binary bytes\) of data stored in the file system, including all storage classes\.
+ **Size in Standard / One Zone** is the size \(in binary bytes\) of data stored in the EFS Standard or EFS One Zone storage class\.
+ **Size in Standard\-IA / One Zone\-IA** is the size \(in binary bytes\) of data stored in the Standard\-IA or One Zone\-IA storage class, depending on whether your file system uses Standard or One Zone storage classes\.

You can also view the `Storage bytes` metric on the **Monitoring** tab on the **File system details** page in the Amazon EFS console\. For more information, see [Accessing CloudWatch metrics](accessingmetrics.md)\.

### Viewing storage data size using the AWS CLI<a name="billing-cli"></a>

You can view how much data is stored in each storage class of your file system using the AWS CLI or EFS API\. View data storage details by calling the `describe-file-systems` CLI command \(the corresponding API operation is [DescribeFileSystems](API_DescribeFileSystems.md)\)\.

```
$  aws efs describe-file-systems \
--region us-west-2 \
--profile adminuser
```

In the response, `SizeInBytes`>`ValueInIA` displays the last metered size in bytes in the file system's IA storage class, either Standard\-IA or One Zone\-IA\. `ValueInStandard` displays the last metered size in bytes in either the EFS Standard or EFS One Zone storage class, depending on the file system configuration\. Added together, they equal the size of the entire file system, displayed by `Value`\.

```
{
   "FileSystems":[
      {
         "OwnerId":"251839141158",
         "CreationToken":"MyFileSystem1",
         "FileSystemId":"fs-47a2c22e",
         "PerformanceMode" : "generalPurpose",
         "CreationTime": 1403301078,
         "LifeCycleState":"created",
         "NumberOfMountTargets":1,
         "SizeInBytes":{
            "Value": 29313417216,
            "ValueInIA": 675432,
            "ValueInStandard": 29312741784
        },
        "ThroughputMode": "bursting"
      }
   ]
}
```

For additional ways to view and measure disk usage, see [Metering Amazon EFS file system objects](metered-sizes.md#metered-sizes-fs-objects)\.

## Using Amazon EFS storage classes<a name="manage-storage-classes-efs"></a>

To use the EFS Standard and Standard–IA storage classes, create a file system that stores data redundantly across multiple Availability Zones in an AWS Region\. To do this when creating a file system using the AWS Management Console, choose **Availability and Durability**, and then choose **Regional**\.

To use the Standard–IA storage class, you must enable the Amazon EFS lifecycle management feature\. When enabled, lifecycle management automates moving files from Standard storage to IA storage\. When you create a file system using the console, **Lifecycle management** is enabled by default with a setting of **30 days since last access**\. For more information, see [Amazon EFS lifecycle management](lifecycle-management-efs.md)\. 

To use the EFS One Zone and One Zone–IA storage classes, you must create a file system that stores data redundantly within a single Availability Zone in an AWS Region\. To do this when creating a file system using the console, choose **Availability and Durability**, and then choose **One Zone**\.

To use the One Zone–IA storage class, you must enable the Amazon EFS lifecycle management feature\. When enabled, lifecycle management automates moving files from Standard storage to IA storage\. When you create a file system using the console, **Lifecycle management** is enabled by default with a setting of **30 days since last access**\. For more information, see [Amazon EFS lifecycle management](lifecycle-management-efs.md)\. 