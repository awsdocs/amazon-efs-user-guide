# Overview of Managing Access Permissions to Your Amazon EFS Resources<a name="access-control-overview"></a>

Every AWS resource is owned by an AWS account, and permissions to create or access a resource are governed by permissions policies\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\)\. Some services, including Amazon EFS, also support attaching permissions policies to resources\.

**Note**  
An *account administrator* \(or administrator user\) is a user with administrator privileges\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When granting permissions, you decide who is getting the permissions, the resources they get permissions for, and the specific actions that you want to allow on those resources\.

**Topics**
+ [Amazon EFS Resources and Operations](#access-control-resources)
+ [Understanding Resource Ownership](#access-control-owner)
+ [Managing Access to Resources](#access-control-manage-access-intro)
+ [Specifying Policy Elements: Actions, Effects, and Principals](#access-control-specify-efs-actions)
+ [Specifying Conditions in a Policy](#specifying-conditions)

## Amazon EFS Resources and Operations<a name="access-control-resources"></a>

In Amazon EFS, the primary resource is a *file system*\. Amazon EFS also supports additional resource types, the *mount target* and *access point*\. However, for Amazon EFS, you can create mount targets and access points only in the context of an existing file system\. Mount targets and access points are referred to as *subresources*\.

These resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them as shown in the following table\. 


****  

| Resource Type | ARN Format | 
| --- | --- | 
| File system | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
| Access point | arn:aws:elasticfilesystem:region:account\-id:access\-point/access\-point\-id | 

Amazon EFS provides a set of operations to work with Amazon EFS resources\. For a list of available operations, see Amazon EFS [Actions](API_Operations.md) and [EFS Actions for NFS Clients](iam-access-control-nfs-efs.md#efs-filesystempolicy-actions)\.

## Understanding Resource Ownership<a name="access-control-owner"></a>

The AWS account owns the resources that are created in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the [principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) \(that is, the root account, an IAM user, or an IAM role\) that authenticates the resource creation request\. The following examples illustrate how this works:
+ If you use the root account credentials of your AWS account to create a file system, your AWS account is the owner of the resource \(in Amazon EFS, the resource is the file system\)\.
+ If you create an IAM user in your AWS account and grant permissions to create a file system to that user, the user can create a file system\. However, your AWS account, to which the user belongs, owns the file system resource\.
+ If you create an IAM role in your AWS account with permissions to create a file system, anyone who can assume the role can create a file system\. Your AWS account, to which the role belongs, owns the file system resource\. 

## Managing Access to Resources<a name="access-control-manage-access-intro"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of Amazon EFS\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as *identity\-based* policies \(IAM polices\) and policies attached to a resource are referred to as *resource\-based* policies\. Amazon EFS supports both identity\-based policies and resource\-based policies\. 

**Topics**
+ [Identity\-Based Policies \(IAM Policies\)](#access-control-manage-access-intro-iam-policies)
+ [Resource\-Based Policies](#access-control-manage-access-intro-resource-policies)

### Identity\-Based Policies \(IAM Policies\)<a name="access-control-manage-access-intro-iam-policies"></a>

You can attach policies to IAM identities to control access to the EFS API or to control NFS client access\. For example, to grant a user permissions to create an Amazon EFS resource, such as a file system, you can attach a permissions policy to a user or group that the user belongs to\.

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

For more information about using identity\-based policies with Amazon EFS, see [Controlling Access to the EFS API](access-control-managing-permissions.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\. 

### Resource\-Based Policies<a name="access-control-manage-access-intro-resource-policies"></a>

You can use file system policies to control API access and NFS client access to the file system\. Amazon EFS supports a resource\-based policy for file systems, called a `FileSystemPolicy`\. Using an EFS `FileSystemPolicy` you can specify who has access to the file system and what actions they can perform on it\. Using file system policies provides you an easy way to control access to your file systems, and lets you grant usage permission to other accounts on a per\-file system basis\. The following file system policy grants `ClientMount`, or read\-only, permissions to all AWS IAM principals\. 

```
{
    "Version": "2012-10-17",
    "Id": "read-only-example-policy02",
    "Statement": [
        {
            "Sid": "efs-statement-example02",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "elasticfilesystem:ClientMount"
            ]
            
        }
    ]
}
```

## Specifying Policy Elements: Actions, Effects, and Principals<a name="access-control-specify-efs-actions"></a>

For each Amazon EFS resource \(see [Amazon EFS Resources and Operations](#access-control-resources)\), the service defines a set of actions API operations \(see [Actions](API_Operations.md) and [EFS Actions for NFS Clients](iam-access-control-nfs-efs.md#efs-filesystempolicy-actions)\)that you can grant permissions for\. For the Amazon EFS file system resource, example actions are: `CreateFileSystem`, `DeleteFileSystem`, and `DescribeFileSystems`\. Performing an API operation can require permissions for more than one action\.

The following are the most basic policy elements:
+ **Resource** – In resource\-based policies \(file system policies\), the resource that the policy is attached to is the implicit resource\. For identity\-based policies, you use an Amazon Resource Name \(ARN\) to identify the resource to which the policy applies\. For more information, see [Amazon EFS Resources and Operations](#access-control-resources)\.
+ **Action** – You use action keywords to identify resource operations that you want to allow or deny\. For example, depending on the specified Effect, `elasticfilesystem:CreateFileSystem` either allows or denies the user permissions to perform the Amazon EFS `CreateFileSystem` operation\.
+ **Effect** – You specify the effect when the user requests the specific action—this can be either allow or deny\. If you don't explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user cannot access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), the user that the policy is attached to is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\. 

To learn more about IAM policy syntax and descriptions, see [IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

For a table showing all of the Amazon EFS API actions, see [Amazon EFS API Permissions: Actions, Resources, and Conditions Reference](access-control-managing-permissions.md#efs-api-permissions-ref)\.

For a table showing all of the Amazon EFS NFS client actions, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\.

## Specifying Conditions in a Policy<a name="specifying-conditions"></a>

When you grant permissions, you can use the IAM policy language to specify the conditions when a policy should take effect\. For more information about specifying conditions in a policy, see [IAM JSON Policy Elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

There are both EFS\-specific and AWS\-wide condition keys that you can use as appropriate\. For a complete list of AWS\-wide keys, see [Available Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. For a complete list of EFS\-specific condition keys, see [EFS Condition Keys for NFS Clients](iam-access-control-nfs-efs.md#efs-condition-keys-for-nfs)\.

**Note**  
The `aws:SourceIp` AWS\-wide condition can be used to control what hosts are able to use EFS actions like `CreateFileSystem`, `CreateMountTarget`, `DeleteMountTarget`, `DescribeMountTargetSecurityGroups`, or `ModifyMountTargetSecurityGroup` actions\. The `aws:SourceIp` condition cannot be used to control NFS access to EFS mount targets\. To control access to EFS mount targets, see [Controlling Network Access to Amazon EFS File Systems for NFS Clients](NFS-access-control-efs.md)\.