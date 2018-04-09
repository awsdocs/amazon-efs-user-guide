# What Is Amazon Elastic File System?<a name="whatisefs"></a>

Amazon Elastic File System \(Amazon EFS\) provides simple, scalable file storage for use with Amazon EC2\. With Amazon EFS, storage capacity is elastic, growing and shrinking automatically as you add and remove files, so your applications have the storage they need, when they need it\.

Amazon EFS has a simple web services interface that allows you to create and configure file systems quickly and easily\. The service manages all the file storage infrastructure for you, avoiding the complexity of deploying, patching, and maintaining complex file system deployments\.

Amazon EFS supports the Network File System versions 4\.0 and 4\.1 \(NFSv4\) protocol, so the applications and tools that you use today work seamlessly with Amazon EFS\. Multiple Amazon EC2 instances can access an Amazon EFS file system at the same time, providing a common data source for workloads and applications running on more than one instance or server\.

With Amazon EFS, you pay only for the storage used by your file system\. You don't need to provision storage in advance and there is no minimum fee or setup cost\. For more information, see [Amazon EFS Pricing](https://aws.amazon.com/efs/pricing)\.

The service is designed to be highly scalable, highly available, and highly durable\. Amazon EFS file systems store data and metadata across multiple Availability Zones in a region and can grow to petabyte scale, drive high levels of throughput, and allow massively parallel access from Amazon EC2 instances to your data\.

Amazon EFS provides file system access semantics, such as strong data consistency and file locking\. For more information, see [Data Consistency in Amazon EFS](how-it-works.md#consistency)\.

 Amazon EFS also allows you to control access to your file systems through Portable Operating System Interface \(POSIX\) permissions\. For more information, see [Security](security-considerations.md)\.

Amazon EFS supports two forms of encryption for file systems, encryption in transit and encryption at rest\. You can enable encryption at rest when creating an Amazon EFS file system\. If you do, all your data and metadata is encrypted\. You can enable encryption in transit when you mount the file system\. For more information, see [Encrypting Data and Metadata in EFS](encryption.md)\.

Amazon EFS is designed to provide the throughput, IOPS, and low latency needed for a broad range of workloads\. With Amazon EFS, throughput and IOPS scale as a file system grows, and file operations are delivered with consistent, low latencies\. For more information, see [Amazon EFS Performance](performance.md)\.

**Note**  
Using Amazon EFS with Microsoft Windows Amazon EC2 instances is not supported\.

## Are you a first\-time user of Amazon EFS?<a name="welcome-first-time-user"></a>

 If you are a first\-time user of Amazon EFS, we recommend you read the following sections in order:

1. For an Amazon EFS product and pricing overview, see [Amazon EFS](https://aws.amazon.com/efs/)\.

1. For an Amazon EFS technical overview, see [Amazon EFS: How It Works](how-it-works.md)\. 

1. Try the introductory exercises:
   + [Getting Started](getting-started.md)
   + [Walkthroughs](walkthroughs.md)

If you would like to learn more about Amazon EFS, the following topics discuss the service in greater detail:
+ [Creating Resources for Amazon EFS](creating-using.md)
+ [Managing Amazon EFS File Systems](managing.md)
+ [Amazon EFS API](api-reference.md)