# What Is Amazon Elastic File System?<a name="whatisefs"></a>

Amazon Elastic File System \(Amazon EFS\) provides a simple, scalable, fully managed elastic NFS file system for use with AWS Cloud services and on\-premises resources\. It is built to scale on demand to petabytes without disrupting applications, growing and shrinking automatically as you add and remove files, eliminating the need to provision and manage capacity to accommodate growth\. Amazon EFS has a simple web services interface that allows you to create and configure file systems quickly and easily\. The service manages all the file storage infrastructure for you, meaning that you can avoid the complexity of deploying, patching, and maintaining complex file system configurations\. 

Amazon EFS supports the Network File System version 4 \(NFSv4\.1 and NFSv4\.0\) protocol, so the applications and tools that you use today work seamlessly with Amazon EFS\. Multiple Amazon EC2 instances can access an Amazon EFS file system at the same time, providing a common data source for workloads and applications running on more than one instance or server\.

With Amazon EFS, you pay only for the storage used by your file system and there is no minimum fee or setup cost\. Amazon EFS oﬀers two storage classes, Standard and Infrequent Access\. The Standard storage class is used to store frequently accessed files\. The Infrequent Access \(IA\) storage class is a lower\-cost storage class that's designed for storing long\-lived, infrequently accessed ﬁles cost\-eﬀectively\. For more information, see [EFS storage classes](storage-classes.md)\. Costs related to Provisioned Throughput are determined by the throughput values you specify\. For more information, see [Amazon EFS Pricing](https://aws.amazon.com/efs/pricing)\.

The service is designed to be highly scalable, highly available, and highly durable\. Amazon EFS file systems store data and metadata across multiple Availability Zones in an AWS Region\. EFS file systems can grow to petabyte scale, drive high levels of throughput, and allow massively parallel access from Amazon EC2 instances to your data\.

Amazon EFS provides file system access semantics, such as strong data consistency and file locking\. For more information, see [Data Consistency in Amazon EFS](how-it-works.md#consistency)\. Amazon EFS also enables you to control access to your file systems through Portable Operating System Interface \(POSIX\) permissions\. For more information, see [Security in Amazon EFS](security-considerations.md)\.

Amazon EFS supports authentication, authorization, and encryption capabilities to help you meet your security and compliance requirements\. Amazon EFS supports two forms of encryption for file systems, encryption in transit and encryption at rest\. You can enable encryption at rest when creating an Amazon EFS file system\. If you do, all your data and metadata is encrypted\. You can enable encryption in transit when you mount the file system\. NFS client access to EFS is controlled by both AWS Identity and Access Management \(IAM\) policies and network security policies like security groups\. For more information, see [Data Encryption in EFS](encryption.md), [Identity and Access Management for Amazon EFS](auth-and-access-control.md), and [Controlling Network Access to Amazon EFS File Systems for NFS Clients](NFS-access-control-efs.md)\.

Amazon EFS is designed to provide the throughput, IOPS, and low latency needed for a broad range of workloads\. With Amazon EFS, you can choose from two performance modes and two throughput modes:
+ The default General Purpose performance mode is ideal for latency\-sensitive use cases, like web serving environments, content management systems, home directories, and general file serving\. File systems in the Max I/O mode can scale to higher levels of aggregate throughput and operations per second with a tradeoff of slightly higher latencies for file metadata operations\. For more information, see [Performance Modes](performance.md#performancemodes)\.
+ Using the default Bursting Throughput mode, throughput scales as your file system grows\. Using Provisioned Throughput mode, you can specify the throughput of your file system independent of the amount of data stored\. For more information, see [Amazon EFS Performance](performance.md)\.

**Note**  
Using Amazon EFS with Microsoft Windows–based Amazon EC2 instances is not supported\.

## Are You a First\-Time User of Amazon EFS?<a name="welcome-first-time-user"></a>

 If you are a first\-time user of Amazon EFS, we recommend that you read the following sections in order:

1. For an Amazon EFS product and pricing overview, see [Amazon EFS](https://aws.amazon.com/efs/)\.

1. For an Amazon EFS technical overview, see [Amazon EFS: How It Works](how-it-works.md)\. 

1. Try the introductory exercises:
   + [Getting Started](getting-started.md)
   + [Walkthroughs](walkthroughs.md)

If you want to learn more about Amazon EFS, the following topics discuss the service in greater detail:
+ [Working with Amazon EFS resources](creating-using.md)
+ [Managing Amazon EFS file systems](managing.md)
+ [Amazon EFS API](api-reference.md)