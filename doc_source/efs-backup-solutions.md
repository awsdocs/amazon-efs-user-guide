# Data Protection for Amazon EFS<a name="efs-backup-solutions"></a>

There are two options available for protecting your data by backing up your EFS file systems\.
+ AWS Backup service
+ The EFS\-to\-EFS backup solution

AWS Backup is a simple and cost\-effective way to back up your Amazon EFS file systems that are in AWS Regions where the AWS Backup service is available\. AWS Backup is a unified backup service designed to simplify the creation, migration, restoration, and deletion of backups, while providing improved reporting and auditing\. For more information, see [Using AWS Backup with Amazon EFS](awsbackup.md)\.

The EFS\-to\-EFS backup solution is suitable for all Amazon EFS file systems in all AWS Regions\. It includes an AWS CloudFormation template that launches, configures, and runs the AWS services required to deploy this solution\. This solution follows AWS best practices for security and availability\. For more information, see [EFS\-to\-EFS Backup Solution](https://aws.amazon.com/answers/infrastructure-management/efs-backup/) in AWS Answers\.