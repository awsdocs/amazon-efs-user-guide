# Step 3: Transfer Files to Amazon EFS Using AWS DataSync<a name="gs-step-four-sync-files"></a>

Now that you have created a functioning Amazon EFS file system, you can use AWS DataSync to transfer files from an existing file system to Amazon EFS\. AWS DataSync is a data transfer service that simplifies, automates, and accelerates moving and replicating data between on\-premises storage systems and AWS storage services over the internet or AWS Direct Connect\. AWS DataSync can transfer your file data, and also file system metadata such as ownership, timestamps, and access permissions\.

## Before You Begin<a name="step4-prereq"></a>

In this step, we assume that you have the following:
+ A source NFS file system that you can transfer files from\. This source system needs to be accessible over NFS version 3, version 4, or 4\.1\. Example file systems include those located in an on\-premises data center, self\-managed in\-cloud file systems, and Amazon EFS file systems\. 
+ A destination Amazon EFS file system to transfer files to\. If you don't have an Amazon EFS file system, create one\. For more information, see [Getting Started with Amazon Elastic File System](getting-started.md)\.
+ Your server and network meet the AWS DataSync requirements\. To learn more, see the [ AWS DataSync requirements](https://docs.aws.amazon.com/datasync/latest/userguide/requirements.html)\.

To transfer files from a source location to a destination location using AWS DataSync, you do the following:
+ Download and deploy an agent in your environment and activate it\.
+ Create and configure a source and destination location\.
+ Create and configure a task\.
+ Run the task to transfer files from the source to the destination\.

To learn how to transfer files from an existing on\-premises file system to your EFS file system, see [Getting Started with AWS DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/getting-started.html) in the *AWS DataSync User Guide*\. To learn how to transfer files from an existing in\-cloud file system to your EFS file system, see [Deploying the AWS DataSync Agent as an Amazon EC2 Instance](https://docs.aws.amazon.com/datasync/latest/userguide/ec2-agent.html) in the *AWS DataSync User Guide*, and the [ Amazon EFS AWS DataSync In\-Cloud Transfer Quick Start and Scheduler](https://github.com/aws-samples/amazon-efs-tutorial/tree/master/in-cloud-transfer)\. 