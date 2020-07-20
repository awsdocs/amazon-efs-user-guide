# EFS storage classes<a name="storage-classes"></a>

Amazon EFS file systems have two storage classes available:
+ **Infrequent Access** – The Infrequent Access \(IA\) storage class is a lower\-cost storage class that's designed for storing long\-lived, infrequently accessed files cost\-effectively\.
+ **Standard** – The Standard storage class is used to store frequently accessed files\.

The EFS IA storage class reduces storage costs for files that are not accessed every day\. It does this without sacrificing the high availability, high durability, elasticity, and POSIX file system access that EFS provides\. We recommend EFS IA storage if you need your full dataset to be readily accessible and want to automatically save on storage costs for files that are less frequently accessed\. Examples include keeping files accessible to satisfy audit requirements, performing historical analysis, or performing backup and recovery\.

EFS IA storage is compatible with all EFS features, and is available in all AWS Regions where Amazon EFS is available\.

 EFS IA billing is based on the amount of data stored in IA and for data access used to read files that are stored in IA\. You incur access charges when files in IA storage are read, and when files are transitioned to IA storage from Standard storage\. To view how much data you have in each storage class, see [Pricing and billing](lifecycle-management-efs.md#billing)\. For storage pricing information, see [Amazon EFS Pricing](https://aws.amazon.com/efs/pricing)\. 

First\-byte latency when reading from or writing to the IA storage class is higher than that for the Standard storage class\.  For file systems using Bursting Throughput, the allowed throughput is determined based on the amount of the data stored in the Standard storage class only\. For file systems using Provisioned Throughput mode, you're billed for the throughput provisioned above what you are provided based on the amount of data that is in the Standard storage class\. For more information on EFS performance, see [Throughput Modes](performance.md#throughput-modes)\. 

## Using storage classes<a name="manage-storage-classes-efs"></a>

To use the IA storage class, enable the EFS lifecycle management feature\. When enabled, lifecycle management automates moving files from Standard storage to IA storage\. To learn more, see [EFS lifecycle management](lifecycle-management-efs.md)\.