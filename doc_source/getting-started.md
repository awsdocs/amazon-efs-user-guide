# Getting Started with Amazon Elastic File System<a name="getting-started"></a>

**Topics**
+ [Assumptions](#gs-assumptions)
+ [Related Topics](#gs-related-topics)
+ [Step 1: Create Your EC2 Resources and Launch Your EC2 Instance](gs-step-one-create-ec2-resources.md)
+ [Step 2: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md)
+ [Step 3: Connect to Your Amazon EC2 Instance and Mount the Amazon EFS File System](gs-step-three-connect-to-ec2-instance.md)
+ [Step 4: Sync Files from Existing File Systems to Amazon EFS Using EFS File Sync](gs-step-four-sync-files.md)
+ [Step 5: Clean Up Resources and Protect Your AWS Account](gs-step-four-cleanup.md)

This Getting Started exercise shows you how to quickly create an Amazon Elastic File System \(Amazon EFS\) file system, mount it on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance in your VPC, and test the end\-to\-end setup\.

There are four steps you need to perform to create and use your first Amazon EFS file system:
+ Create your Amazon EC2 resources and launch your instance\.
+ Create your Amazon EFS file system\.
+ Connect to your Amazon EC2 instance and mount the Amazon EFS file system\.
+ Clean up your resources and protect your AWS account\.

## Assumptions<a name="gs-assumptions"></a>

For this exercise, we assume the following:
+ You're already familiar with using the Amazon EC2 console to launch instances\.
+ Your Amazon VPC, Amazon EC2, and Amazon EFS resources are all in the same region\. This guide uses the US West \(Oregon\) Region \(us\-west\-2\)\.
+ You have a default VPC in the region that you're using for this Getting Started exercise\. If you don't have a default VPC, or if you want to mount your file system from a new VPC with new or existing security groups, you can still use this Getting Started exercise\. To do so, configure [Security Groups for Amazon EC2 Instances and Mount Targets](security-considerations.md#network-access)\.
+ You have not changed the default inbound access rule for the default security group\.

You can use the root credentials of your AWS account to sign in to the console and try the Getting Started exercise\. However, AWS Identity and Access Management \(IAM\) recommends that you do not use the root credentials of your AWS account\. Instead, create an administrator user in your account and use those credentials to manage resources in your account\. For more information, see [Setting Up](setting-up.md)\.

## Related Topics<a name="gs-related-topics"></a>

This guide also provides a walkthrough to perform a similar Getting Started exercise using AWS Command Line Interface \(AWS CLI\) commands to make the Amazon EFS API calls\. For more information, see [Walkthrough 1: Create Amazon EFS File System and Mount It on an EC2 Instance Using the AWS CLI](wt1-getting-started.md)\.