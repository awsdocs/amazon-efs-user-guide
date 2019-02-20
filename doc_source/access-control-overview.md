# Overview of Managing Access Permissions to Your Amazon EFS Resources<a name="access-control-overview"></a>

Every AWS resource is owned by an AWS account, and permissions to create or access a resource are governed by permissions policies\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\), and some services \(such as AWS Lambda\) also support attaching permissions policies to resources\. 

**Note**  
An *account administrator* \(or administrator user\) is a user with administrator privileges\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When granting permissions, you decide who is getting the permissions, the resources they get permissions for, and the specific actions that you want to allow on those resources\.

**Topics**
+ [Amazon Elastic File System Resources and Operations](#access-control-resources)
+ [Understanding Resource Ownership](#access-control-owner)
+ [Managing Access to Resources](#access-control-manage-access-intro)
+ [Specifying Policy Elements: Actions, Effects, and Principals](#access-control-specify-efs-actions)
+ [Specifying Conditions in a Policy](#specifying-conditions)

## Amazon Elastic File System Resources and Operations<a name="access-control-resources"></a>

In Amazon Elastic File System, the primary resource is a *file system*\. Amazon Elastic File System also supports additional resource types, the *mount target* and *tags*\. However, for Amazon EFS, you can create mount targets and tags only in the context of an existing file system\. Mount targets and tags are referred to as *subresource*\.

These resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them as shown in the following table\. 


****  

| Resource Type | ARN Format | 
| --- | --- | 
| File system | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 

Amazon EFS provides a set of operations to work with Amazon EFS resources\. For a list of available operations, see Amazon Elastic File System [Actions](API_Operations.md)\.

## Understanding Resource Ownership<a name="access-control-owner"></a>

The AWS account owns the resources that are created in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the [principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) \(that is, the root account, an IAM user, or an IAM role\) that authenticates the resource creation request\. The following examples illustrate how this works:
+ If you use the root account credentials of your AWS account to create a file system, your AWS account is the owner of the resource \(in Amazon EFS, the resource is the file system\)\.
+ If you create an IAM user in your AWS account and grant permissions to create a file system to that user, the user can create a file system\. However, your AWS account, to which the user belongs, owns the file system resource\.
+ If you create an IAM role in your AWS account with permissions to create a file system, anyone who can assume the role can create a file system\. Your AWS account, to which the role belongs, owns the file system resource\. 

## Managing Access to Resources<a name="access-control-manage-access-intro"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of Amazon Elastic File System\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as *identity\-based* policies \(IAM polices\) and policies attached to a resource are referred to as *resource\-based* policies\. Amazon Elastic File System supports only identity\-based policies \(IAM policies\)\. 

**Topics**
+ [Identity\-Based Policies \(IAM Policies\)](#access-control-manage-access-intro-iam-policies)
+ [Resource\-Based Policies](#access-control-manage-access-intro-resource-policies)

### Identity\-Based Policies \(IAM Policies\)<a name="access-control-manage-access-intro-iam-policies"></a>

You can attach policies to IAM identities\. For example, you can do the following:
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to create an Amazon EFS resource, such as a file system, you can attach a permissions policy to a user or group that the user belongs to\.
+ **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, the administrator in Account A can create a role to grant cross\-account permissions to another AWS account \(for example, Account B\) or an AWS service as follows:

  1. Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permissions on resources in Account A\.

  1. Account A administrator attaches a trust policy to the role identifying Account B as the principal who can assume the role\. 

  1. Account B administrator can then delegate permissions to assume the role to any users in Account B\. Doing this allows users in Account B to create or access resources in Account A\. The principal in the trust policy can also be an AWS service principal if you want to grant an AWS service permissions to assume the role\.

  For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

The following is an example policy that allows a user to perform the `CreateFileSystem` action for your AWS account\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid" : "Stmt1EFSpermissions",  
      "Effect": "Allow",
      "Action": [
        "elasticfilesystem:CreateFileSystem",
        "elasticfilesystem:CreateMountTarget"
      ],
      "Resource": "arn:aws:elasticfilesystem:us-west-2:account-id:file-system/*"
    },
    {
     "Sid" : "Stmt2EC2permissions",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeSubnets",
        "ec2:CreateNetworkInterface",
        "ec2:DescribeNetworkInterfaces"
      ],
      "Resource": "*"
    }
  ]
```

For more information about using identity\-based policies with Amazon EFS, see [Using Identity\-Based Policies \(IAM Policies\) for Amazon Elastic File System](access-control-managing-permissions.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\. 

### Resource\-Based Policies<a name="access-control-manage-access-intro-resource-policies"></a>

Other services, such as Amazon S3, also support resource\-based permissions policies\. For example, you can attach a policy to an S3 bucket to manage access permissions to that bucket\. Amazon Elastic File System doesn't support resource\-based policies\. 

## Specifying Policy Elements: Actions, Effects, and Principals<a name="access-control-specify-efs-actions"></a>

For each Amazon Elastic File System resource \(see [Amazon Elastic File System Resources and Operations](#access-control-resources)\), the service defines a set of API operations \(see [Actions](API_Operations.md)\)\. To grant permissions for these API operations, Amazon EFS defines a set of actions that you can specify in a policy\. For example, for the Amazon EFS file system resource, the following actions are defined: `CreateFileSystem`, `DeleteFileSystem`, and `DescribeFileSystems`\. Note that, performing an API operation can require permissions for more than one action\.

The following are the most basic policy elements:
+ **Resource** – In a policy, you use an Amazon Resource Name \(ARN\) to identify the resource to which the policy applies\. For more information, see [Amazon Elastic File System Resources and Operations](#access-control-resources)\.
+ **Action** – You use action keywords to identify resource operations that you want to allow or deny\. For example, depending on the specified `Effect`, `elasticfilesystem:CreateFileSystem` either allows or denies the user permissions to perform the Amazon Elastic File System `CreateFileSystem` operation\.
+ **Effect** – You specify the effect when the user requests the specific action—this can be either allow or deny\. If you don't explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user cannot access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), the user that the policy is attached to is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\. Amazon EFS doesn't support resource\-based policies\.

To learn more about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

For a table showing all of the Amazon Elastic File System API actions, see [Amazon EFS API Permissions: Actions, Resources, and Conditions Reference](efs-api-permissions-ref.md)\.

## Specifying Conditions in a Policy<a name="specifying-conditions"></a>

When you grant permissions, you can use the IAM policy language to specify the conditions when a policy should take effect\. For example, you might want a policy to be applied only after a specific date\. For more information about specifying conditions in a policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Condition) in the *IAM User Guide*\. 

To express conditions, you use predefined condition keys\. There are no condition keys specific to Amazon Elastic File System\. However, there are AWS\-wide condition keys that you can use as appropriate\. For a complete list of AWS\-wide keys, see [Available Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. 

**Note**  
Do not use the `aws:SourceIp` AWS\-wide condition for the `CreateMountTarget`, `DeleteMountTarget`, `DescribeMountTargetSecurityGroups`, or `ModifyMountTargetSecurityGroup` actions\. Amazon EFS provisions mount targets by using its own IP address, not the IP address of the originating request\.