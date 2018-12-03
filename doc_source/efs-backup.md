# Walkthrough: Backup Solutions for Amazon EFS File Systems<a name="efs-backup"></a>

If you need to be able to recover from unintended changes or deletions in your Amazon EFS file systems, we recommend that you use the [EFS\-to\-EFS Backup Solution](https://aws.amazon.com/answers/infrastructure-management/efs-backup/)\.

The EFS\-to\-EFS backup solution is suitable for all Amazon EFS file systems in all AWS Regions\. It includes an AWS CloudFormation template that launches, configures, and runs the AWS services required to deploy this solution\. This solution follows AWS best practices for security and availability\.

For more information on the EFS\-to\-EFS backup solution, see [EFS\-to\-EFS Backup Solution](https://aws.amazon.com/answers/infrastructure-management/efs-backup/) in AWS Answers\.

**Note**  
Before the EFS\-to\-EFS backup solution was available, we recommended an alternative backup solution that is only suitable in AWS Regions that have AWS Data Pipeline support\. For more information on that previous solution, see [Backing Up Amazon EFS File Systems Using AWS Data Pipeline](alternative-efs-backup.md)\.