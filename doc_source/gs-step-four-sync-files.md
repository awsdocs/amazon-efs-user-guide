# Step 4: Transfer Files from On\-Premises File Systems to Amazon EFS Using AWS DataSync<a name="gs-step-four-sync-files"></a>

Now that you have created a functioning Amazon EFS file system, you can use AWS DataSync to transfer files from an existing on\-premises file system to Amazon EFS\. AWS DataSync is a data transfer service that simplifies, automates, and accelerates moving and replicating data between on\-premises storage systems and AWS storage services over the internet or AWS Direct Connect\. AWS DataSync can transfer your file data, and also file system metadata such as ownership, time stamps, and access permissions\.

In this step, we assume that you have the following:
+ An on\-premises source NFS file system that you can transfer files from\. This source system needs to be accessible over NFS version 3 or version 4\.
+ A destination Amazon EFS file system to transfer files to\. If you don't have an Amazon EFS file system, create one\. For more information, see [Getting Started with Amazon Elastic File System](getting-started.md)\.
+ Your on\-premises server and network meet the AWS DataSync requirements\. To learn more, see the [AWS DataSync requirements](https://docs.aws.amazon.com/datasync/latest/userguide/requirements.html)\.

To transfer files from a source location to a destination location using AWS DataSync, you do the following:
+ Choose your use case\.
+ Download and deploy an agent in your on\-premises environment and activate it\.
+ Create and configure a source and destination location\.
+ Create and configure a task\.
+ Run the task to transfer files from the source to the destination\.

To learn how to transfer files from an existing on\-premises file system to your EFS file system, see the [AWS DataSync User Guide](https://docs.aws.amazon.com/datasync/latest/userguide/what-is-datasync.html)\.