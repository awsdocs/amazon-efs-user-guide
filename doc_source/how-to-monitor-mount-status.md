# How do I monitor my EFS file system's mount status?<a name="how-to-monitor-mount-status"></a>

You can use Amazon CloudWatch Logs to monitor the status of EFS file system mounts remotely without having to log into the EC2 instances\. The following pre\-requisites are required in order to use CloudWatch Logs to monitor for file system mount status:

1. The EC2 instances are launched with an instance profile that includes the `AmazonElasticFileSystemsUtils` permission policy\. For more information, see [Step 1: Configure an IAM instance profile with the required permissions](manage-efs-utils-with-aws-sys-manager.md#configure-sys-mgr-iam-instance-profile)\.

1. Version 1\.28\.1 or later of the Amazon EFS client \(amazon\-efs\-utils package\) is installed on the EC2 instances\. You can use AWS Systems Manager to automatically install the package on your instances\. For more information, see [Step 2: Configure an Association used by State Manager for installing or updating the Amazon EFS client](manage-efs-utils-with-aws-sys-manager.md#config-sys-mgr-association)\.

1. The efs\-utils\.conf configuration file is updated to enable CloudWatch Logs\. When you use AWS Systems Manager to install and configure the amazon\-efs\-utils package, this update to enable CloudWatch logging is automatically done for you\. When you install the amazon\-efs\-utils package from the yum repository, you have to manually update the /etc/amazon/efs/efs\-utils\.conf configuration file by uncommenting the `# enabled = true` line in the cloudwatch\-log section\.

Here are examples of mount status log entries:

```
Successfully mounted fs-12345678.efs.us-east-1.amazonaws.com at /home/ec2-user/efs
Mount failed, Failed to resolve "fs-01234567.efs.us-east-1.amazonaws.com"
```

**To view mount status in CloudWatch Logs**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Log groups** in the left\-hand navigation bar\.

1. Choose the **/aws/efs/utils** log group\. You will see a log stream for each EC2 instance and EFS file system combination\.

1. Choose a log stream to view specific log events including the file system mount status\.