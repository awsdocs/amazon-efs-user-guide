# Blocking public access<a name="access-control-block-public-access"></a>

The Amazon EFS block public access feature provides settings to help you manage public access to Amazon EFS file systems\. By default, new Amazon EFS file systems don't allow public access, however, users can modify file system policies to allow public access\.

**Topics**
+ [Blocking public access with AWS Transfer](#block-efs-public-access-with-transferfamily)
+ [The meaning of "public"](#what-is-a-public-policy)

## Blocking public access with AWS Transfer<a name="block-efs-public-access-with-transferfamily"></a>

When using Amazon EFS with AWS Transfer Family, file system access requests received from an Transfer Family server that is owned by a different account than the file system are blocked if the file system allows public access\. Amazon EFS evaluates the file system's IAM policies when and if the policy is public, then it will block the request\. To permit AWS Transfer family access to your file system, simply update your file system policy so that it is not considered public\. 

**Note**  
Using Transfer Family with Amazon EFS is disabled by default for AWS accounts that have EFS file systems with policies that allow public access that were created before January 6, 2021\. Please contact customer support to enable using Transfer Family to access your file system\.

## The meaning of "public"<a name="what-is-a-public-policy"></a>

When evaluating whether a file system allows public access, Amazon EFS assumes that the file system policy is public\. It then evaluates the file system policy to determine if it qualifies as non\-public\. To be considered non\-public, a file system policy must grant access only to fixed values \(values that don't contain a wild card\) of one or more of the following:
+ A set of Classless Inter\-Domain Routings \(CIDRs\), using `aws:SourceIp`\. For more information about CIDR, see [RFC 4632](https://www.rfc-editor.org/rfc/rfc4632.txt)on the RFC Editor website\.
+ An AWS principal, user, role, or service principal \(e\.g\. `aws:PrincipalOrgID`\)
+ `aws:SourceArn`
+ `aws:SourceVpc`
+ `aws:SourceVpce`
+ `aws:SourceOwner`
+ `aws:SourceAccount`
+ `elasticfilesystem:AccessedViaMountTarget`
+ `aws:userid, outside the pattern "AROLEID:*"`

Under these rules, the following example policy is considered public\.

```
{  
    "Version": "2012-10-17",
    "Id": "efs-policy-wizard-15ad9567-2546-4bbb-8168-5541b6fc0e55",
    "Statement": [
        {
            "Sid": "efs-statement-14a7191c-9401-40e7-a388-6af6cfb7dd9c",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "elasticfilesystem:ClientMount",
                "elasticfilesystem:ClientWrite",
                "elasticfilesystem:ClientRootAccess"
            ]
        }
    ]
}
```

You can make this file system policy non\-public by using the EFS condition key `elasticfilesystem:AccessedViaMountTarget` set to true\. `elasticfilesystem:AccessedViaMountTarget` is used to allow the specified EFS actions to clients accessing the EFS file system using a file system mount target\. The following non\-public policy uses the `elasticfilesystem:AccessedViaMountTarget` condition key set to true\.

```
{  
    "Version": "2012-10-17",
    "Id": "efs-policy-wizard-15ad9567-2546-4bbb-8168-5541b6fc0e55",
    "Statement": [
        {
            "Sid": "efs-statement-14a7191c-9401-40e7-a388-6af6cfb7dd9c",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "elasticfilesystem:ClientMount",
                "elasticfilesystem:ClientWrite",
                "elasticfilesystem:ClientRootAccess"
            ],
            "Condition": {
                "Bool": {
                    "elasticfilesystem:AccessedViaMountTarget": "true"
                }
            }
        }
    ]
}
```

For more information about EFS condition keys, see [EFS Condition Keys for Clients](iam-access-control-nfs-efs.md#efs-condition-keys-for-nfs)\. For more information about creating file system policies, see [Creating file system policies](create-file-system-policy.md)\.