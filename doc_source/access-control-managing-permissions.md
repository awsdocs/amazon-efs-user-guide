# Using Identity\-Based Policies \(IAM Policies\) for Amazon Elastic File System<a name="access-control-managing-permissions"></a>

This topic provides examples of identity\-based policies that demonstrate how an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\) and thereby grant permissions to perform operations on Amazon EFS resources\. 

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available for you to manage access to your Amazon Elastic File System resources\. For more information, see [Overview of Managing Access Permissions to Your Amazon EFS Resources](access-control-overview.md)\. 

The sections in this topic cover the following:
+  [Permissions Required to Use the Amazon EFS Console](#additional-console-required-permissions) 
+ [AWS Managed \(Predefined\) Policies for Amazon EFS](#access-policy-examples-aws-managed)
+ [Customer Managed Policy Examples](#access-policy-examples-for-sdk-cli)

The following shows an example of a permissions policy\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid" : "AllowFileSystemPermissions",  
      "Effect": "Allow",
      "Action": [
        "elasticfilesystem:CreateFileSystem",
        "elasticfilesystem:CreateMountTarget"
      ],
      "Resource": "arn:aws:elasticfilesystem:us-west-2:account-id:file-system/*"
    },
    {
     "Sid" : "AllowEC2Permissions",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeSubnets",
        "ec2:CreateNetworkInterface",
        "ec2:DescribeNetworkInterfaces"
      ],
      "Resource": "*"
    }
  ]
}
```

The policy has two statements:
+ The first statement grants permissions for two Amazon EFS actions \(`elasticfilesystem:CreateFileSystem` and `elasticfilesystem:CreateMountTarget`\) on a resource using the *Amazon Resource Name \(ARN\)* for the file system\. The ARN specifies a wildcard character \(\*\) because you don't know the file system ID until after you create a file system\. 
+ The second statement grants permissions for some of the Amazon EC2 actions because the `elasticfilesystem:CreateMountTarget` action in the first statement requires permissions for specific Amazon EC2 actions\. Because these Amazon EC2 actions don't support resource\-level permissions, the policy specifies the wildcard character \(\*\) as the `Resource` value instead of specifying a file system ARN\.

The policy doesn't specify the `Principal` element because in an identity\-based policy you don't specify the principal who gets the permission\. When you attach policy to a user, the user is the implicit principal\. When you attach a permissions policy to an IAM role, the principal identified in the role's trust policy gets the permissions\. 

For a table showing all of the Amazon Elastic File System API actions and the resources that they apply to, see [Amazon EFS API Permissions: Actions, Resources, and Conditions Reference](efs-api-permissions-ref.md)\. 

## Permissions Required to Use the Amazon EFS Console<a name="additional-console-required-permissions"></a>

The permissions reference table lists the Amazon EFS API operations and shows the required permissions for each operation\. For more information about Amazon EFS API operations, see [Amazon EFS API Permissions: Actions, Resources, and Conditions Reference](efs-api-permissions-ref.md)\. 

To use the Amazon EFS console, you need to grant permissions for additional actions as shown in the following permissions policy: 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid" : "Stmt1AddtionalEC2PermissionsForConsole",  
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeAvailabilityZones",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeVpcs",
        "ec2:DescribeVpcAttribute"

      ],
      "Resource": "*"
    }
    {
     "Sid" : "Stmt2AdditionalKMSPermissionsForConsole",
      "Effect": "Allow",
      "Action": [
        "kms:ListAliases",
        "kms:DescribeKey"
      ],
      "Resource": "*"
    }
  ]
}
```

The Amazon EFS console needs these additional permissions for the following reasons:
+ Permissions for the Amazon EFS actions enable the console to display Amazon EFS resources in the account\.
+ The console needs permissions for the `ec2` actions to query Amazon EC2 so it can display Availability Zones, VPCs, security groups, and account attributes\.
+ The console needs permissions for the `kms` actions to create an encrypted file system\. For more information on encrypted file systems, see [Encrypting Data and Metadata in EFS](encryption.md)\.

## AWS Managed \(Predefined\) Policies for Amazon EFS<a name="access-policy-examples-aws-managed"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\. 

The following AWS managed policies, which you can attach to users in your account, are specific to Amazon EFS:
+ **AmazonElasticFileSystemReadOnlyAccess** – Grants read\-only access to Amazon EFS resources\.
+ **AmazonElasticFileSystemFullAccess** – Grants full access to Amazon EFS resources\.

**Note**  
You can review these permissions policies by signing in to the IAM console and searching for specific policies there\.

You can also create your own custom IAM policies to allow permissions for Amazon EFS API actions\. You can attach these custom policies to the IAM users or groups that require those permissions\. 

## Customer Managed Policy Examples<a name="access-policy-examples-for-sdk-cli"></a>

In this section, you can find example user policies that grant permissions for various Amazon EFS actions\. These policies work when you are using AWS SDKs or the AWS CLI\. When you are using the console, you need to grant additional permissions specific to the console, which is discussed in [Permissions Required to Use the Amazon EFS Console](#additional-console-required-permissions)\.

**Note**  
All examples use the us\-west\-2 region and contain fictitious account IDs\.

**Topics**
+ [Example 1: Allow a User to Create a Mount Target and Tags on an Existing File System](#access-policy-example-allow-create-mount-target)
+ [Example 2: Allow a User to Perform All Amazon EFS Actions](#access-policy-example-allow-perform-all-actions)

### Example 1: Allow a User to Create a Mount Target and Tags on an Existing File System<a name="access-policy-example-allow-create-mount-target"></a>

The following permissions policy grants the user permissions to create mount targets and tags on a particular file system in the `us-west-2` region\. To create mount targets, permissions for specific Amazon EC2 actions are also required and are included in the permissions policy\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid" : "Stmt1CreateMountTargetAndTag",
      "Effect": "Allow",
      "Action": [
        "elasticfilesystem:CreateMountTarget",
        "elasticfilesystem:DescribeMountTargets",
        "elasticfilesystem:CreateTags",
        "elasticfilesystem:DescribeTags"
      ],
      "Resource": "arn:aws:elasticfilesystem:us-west-2:123456789012:file-system/file-system-ID"
    },
    {
      "Sid" : "Stmt2AdditionalEC2PermissionsToCreateMountTarget",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeSubnets",
        "ec2:CreateNetworkInterface",
        "ec2:DescribeNetworkInterfaces"
      ],
      "Resource": "*"
    }
  ]
}
```

### Example 2: Allow a User to Perform All Amazon EFS Actions<a name="access-policy-example-allow-perform-all-actions"></a>

The following permissions policy uses a wildcard character \(`"elasticfilesystem:*"`\) to allow all Amazon EFS actions in the `us-west-2` region\. Because some of the Amazon EFS actions also require permissions for Amazon EC2 actions, the policy also grants permissions for all those actions\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
     "Sid" : "Stmt1PermissionForAllEFSActions",
      "Effect": "Allow",
      "Action": "elasticfilesystem:*",
      "Resource": "arn:aws:elasticfilesystem:us-west-2:123456789012:file-system/*"
    },
    {
     "Sid" : "Stmt2RequiredEC2PermissionsForAllEFSActions",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeSubnets",
        "ec2:CreateNetworkInterface",
        "ec2:DescribeNetworkInterfaces",
        "ec2:DeleteNetworkInterface",
        "ec2:ModifyNetworkInterfaceAttribute",
        "ec2:DescribeNetworkInterfaceAttribute"
      ],
      "Resource": "*"
    }
     
  ]
}
```