# AWS managed policies for Amazon EFS<a name="security-iam-awsmanpol"></a>

To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the **ViewOnlyAccess** AWS managed policy provides read\-only access to many AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.







## AWS managed policy: AmazonElasticFileSystemFullAccess<a name="security-iam-awsmanpol-AmazonElasticFileSystemFullAccess"></a>

You can attach the `AmazonElasticFileSystemFullAccess` policy to your IAM identities\.

This policy grants administrative permissions that allow full access to Amazon EFS and access to related AWS services via the AWS Management Console\.

**Permissions details**

This policy includes the following permissions\.
+ `elasticfilesystem` – Allows principals to perform all actions in the Amazon EFS console\. It also allows principals to create \(`elasticfilesystem:Backup`\) and restore \(`elasticfilesystem:Restore`\) backups using AWS Backup\.
+ `cloudwatch` – Allows principals to describe Amazon CloudWatch file system metrics and alarms for a metric in the Amazon EFS console\.
+ `ec2` – Allows principals to create, delete, and describe network interfaces, describe and modify network interface attributes, describe Availability Zones, security groups, subnets, virtual private clouds \(VPCs\) and VPC attributes associated with an Amazon EFS file system in the Amazon EFS console\.
+ `kms` – Allows principals to list aliases for AWS Key Management Service \(AWS KMS\) keys and to describe KMS keys in the Amazon EFS console\.
+ `iam` – Grants permission to create a service linked role that allows Amazon EFS to manage AWS resources on the user's behalf\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "cloudwatch:DescribeAlarmsForMetric",
                "cloudwatch:GetMetricData",
                "ec2:CreateNetworkInterface",
                "ec2:DeleteNetworkInterface",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeNetworkInterfaceAttribute",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeVpcs",
                "ec2:ModifyNetworkInterfaceAttribute",
                "elasticfilesystem:Backup",
                "elasticfilesystem:CreateFileSystem",
                "elasticfilesystem:CreateMountTarget",
                "elasticfilesystem:CreateTags",
                "elasticfilesystem:CreateAccessPoint",
                "elasticfilesystem:CreateReplicationConfiguration",
                "elasticfilesystem:DeleteFileSystem",
                "elasticfilesystem:DeleteMountTarget",
                "elasticfilesystem:DeleteTags",
                "elasticfilesystem:DeleteAccessPoint",
                "elasticfilesystem:DeleteFileSystemPolicy",
                "elasticfilesystem:DeleteReplicationConfiguration",
                "elasticfilesystem:DescribeAccountPreferences",
                "elasticfilesystem:DescribeBackupPolicy",
                "elasticfilesystem:DescribeFileSystems",
                "elasticfilesystem:DescribeFileSystemPolicy",
                "elasticfilesystem:DescribeLifecycleConfiguration",
                "elasticfilesystem:DescribeMountTargets",
                "elasticfilesystem:DescribeMountTargetSecurityGroups",
                "elasticfilesystem:DescribeReplicationConfigurations",
                "elasticfilesystem:DescribeTags",
                "elasticfilesystem:DescribeAccessPoints",
                "elasticfilesystem:ModifyMountTargetSecurityGroups",
                "elasticfilesystem:PutAccountPreferences",
                "elasticfilesystem:PutBackupPolicy",
                "elasticfilesystem:PutLifecycleConfiguration",
                "elasticfilesystem:PutFileSystemPolicy",
                "elasticfilesystem:UpdateFileSystem",
                "elasticfilesystem:TagResource",
                "elasticfilesystem:UntagResource",
                "elasticfilesystem:ListTagsForResource",                
                "elasticfilesystem:Restore",
                "kms:DescribeKey",
                "kms:ListAliases"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": "iam:CreateServiceLinkedRole",
            "Effect": "Allow",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "elasticfilesystem.amazonaws.com"
                    ]
                }
            }
        }
    ]
}
```

## AWS managed policy: AmazonElasticFileSystemReadOnlyAccess<a name="security-iam-awsmanpol-AmazonElasticFileSystemReadOnlyAccess"></a>

You can attach the `AmazonElasticFileSystemReadOnlyAccess` policy to your IAM identities\.

This policy grants read only access to Amazon EFS via the AWS Management Console\.

**Permissions details**

This policy includes the following permissions\.




+ `elasticfilesystem` – Allows principals to describe attributes of Amazon EFS file systems, including account preferences, backup and file system policies, lifecycle configuration, mount targets and their security groups, tags, and access points in the Amazon EFS console\.
+ `cloudwatch` – Allows principals to retrieve CloudWatch metrics and describe alarms for metrics in the Amazon EFS console\.
+ `ec2` – Allows principals to view Availability Zones, network interfaces and their attributes, security groups, subnets, VPCs and their attributes in the Amazon EFS console\.
+ `kms` – Allows principals to list aliases for AWS KMS keys in the Amazon EFS console\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:DescribeAlarmsForMetric",
                "cloudwatch:GetMetricData",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeNetworkInterfaceAttribute",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeVpcs",
                "elasticfilesystem:DescribeAccountPreferences",
                "elasticfilesystem:DescribeBackupPolicy",
                "elasticfilesystem:DescribeFileSystems",
                "elasticfilesystem:DescribeFileSystemPolicy",
                "elasticfilesystem:DescribeLifecycleConfiguration",
                "elasticfilesystem:DescribeMountTargets",
                "elasticfilesystem:DescribeMountTargetSecurityGroups",                
                "elasticfilesystem:DescribeTags",
                "elasticfilesystem:DescribeAccessPoints",
                "elasticfilesystem:DescribeReplicationConfigurations",
                "elasticfilesystem:ListTagsForResource",
                "kms:ListAliases"
            ],
            
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AmazonElasticFileSystemClientReadWriteAccess<a name="security-iam-awsmanpol-AmazonElasticFileSystemClientReadWriteAccess"></a>

You can attach the `AmazonElasticFileSystemClientReadWriteAccess` policy to an IAM entity\.

This policy grants read and write client access to an Amazon EFS file system\. This policy allows NFS clients to mount, read and write to Amazon EFS file systems\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "elasticfilesystem:ClientMount",
                "elasticfilesystem:ClientWrite",
                "elasticfilesystem:DescribeMountTargets"
            ],
            "Resource": "*"
        }
    ]
}
```

## Amazon EFS updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>

View details about updates to AWS managed policies for Amazon EFS since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the Amazon EFS [Document History](document-history.md) page\.


| Change | Description | Date | 
| --- | --- | --- | 
| [AmazonElasticFileSystemServiceRolePolicy](using-service-linked-roles.md#slr-permissions) – Update to an existing policy | Amazon EFS added new permissions to allow principals to create, describe, and delete Amazon EFS replications, and to create Amazon EFS file systems\. This is required so that Amazon EFS can manage files system replication configurations on the user's behalf\. | January 25, 2022 | 
| [AmazonElasticFileSystemReadOnlyAccess](#security-iam-awsmanpol-AmazonElasticFileSystemReadOnlyAccess) – Update to an existing policy | Amazon EFS added a new permission to allow principals to describe Amazon EFS replications\. This is required so that users can view files system replication configurations\. | January 25, 2022 | 
| [AmazonElasticFileSystemFullAccess](#security-iam-awsmanpol-AmazonElasticFileSystemFullAccess) – Update to an existing policy | Amazon EFS added new permissions to allow principals to create, describe, and delete Amazon EFS replications\. This is required so that users can manage files system replication configurations\. | January 25, 2022 | 
| [AmazonElasticFileSystemClientReadWriteAccess](#security-iam-awsmanpol-AmazonElasticFileSystemClientReadWriteAccess) – Started tracking policy | Grants read and write privileges on Amazon EFS file systems to NFS clients\. | January 3, 2022 | 
| [AmazonElasticFileSystemServiceRolePolicy](using-service-linked-roles.md#slr-permissions) – Started tracking policy | The service\-linked role permissions for Amazon EFS\. | October 8, 2021 | 
|  [AmazonElasticFileSystemFullAccess](#security-iam-awsmanpol-AmazonElasticFileSystemFullAccess) – Update to an existing policy  |  Amazon EFS added new permissions to allow principals to modify and describe Amazon EFS account preferences\. This is required so that users can view and set account preferences settings in the Amazon EFS console\.  | May 7, 2021 | 
|  [AmazonElasticFileSystemReadOnlyAccess](#security-iam-awsmanpol-AmazonElasticFileSystemReadOnlyAccess) – Update to an existing policy  |  Amazon EFS added new permissions to allow principals to describe Amazon EFS account preferences\. This is required so that users can view account preferences settings in the Amazon EFS console\.  | May 7, 2021 | 
|  Amazon EFS started tracking changes  |  Amazon EFS started tracking changes for its AWS managed policies\.  | May 7, 2021 | 