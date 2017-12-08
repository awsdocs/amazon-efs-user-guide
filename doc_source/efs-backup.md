# Walkthrough 4: Backup Solutions for Amazon EFS File Systems<a name="efs-backup"></a>

If you need to be able to recover from unintended changes or deletions in your Amazon EFS file systems, we recommend that you implement a backup solution\. Following are two possible backup solutions that you can use, depending on regional support of the related AWS services:

+ **[EFS\-to\-EFS Backup Solution](https://aws.amazon.com/answers/infrastructure-management/efs-backup/)** – This backup solution in AWS Answers is suitable for all Amazon EFS file systems in all AWS Regions\. It includes an AWS CloudFormation template that launches, configures, and runs the AWS services required to deploy this solution\. This solution follows AWS best practices for security and availability\.

+ **[Backing Up Amazon EFS File Systems by Using AWS Data Pipeline](alternative-efs-backup.md)** – This backup solution is suitable only for Amazon EFS systems in AWS Regions that have AWS Data Pipeline support\. In this solution, you create an AWS Data Pipeline to copy data from one Amazon EFS file system to another\.