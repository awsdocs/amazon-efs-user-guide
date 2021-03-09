# Using AWS Transfer Family to access files in your Amazon EFS file system<a name="using-aws-transfer-integration"></a>

AWS Transfer Family is a fully managed AWS service that you can use to transfer files into and out of Amazon EFS file systems over the following protocols:
+  Secure Shell \(SSH\) File Transfer Protocol \(SFTP\) \(AWS Transfer for SFTP\)
+ File Transfer Protocol Secure \(FTPS\) \(AWS Transfer for FTPS\)
+ File Transfer Protocol \(FTP\) \(AWS Transfer for FTP\)

Using Transfer Family, you can securely enable third parties such as your vendors, partners, or customers access to your files over the supported protocols at scale globally, without needing to manage any infrastructure\. Additionally, you can now easily access your EFS file systems from Windows, macOS, and Linux environments using SFTP, FTPS, and FTP clients\. This helps expand the accessibility of your data beyond NFS clients and access points, to users across multiple environments\.

Using Transfer Family to transfer data in Amazon EFS file systems is accounted for in the same manner as other client usage\. For more information, see [Throughput modes](performance.md#throughput-modes) and [Amazon EFS quotas and limits](limits.md)\.

To learn more about AWS Transfer Family, see the [AWS Transfer Family User Guide](https://docs.aws.amazon.com/transfer/latest/userguide/what-is-aws-transfer-family.html)\.

**Note**  
Using Transfer Family with Amazon EFS is disabled by default for AWS accounts that have Amazon EFS file systems with policies that allow public access that were created before January 6, 2021\. To enable using Transfer Family to access your file system, contact AWS Support\.

**Topics**
+ [Prerequisites for using AWS Transfer Family with Amazon EFS](#prerequisites-aws-transfer)
+ [Configuring your Amazon EFS file system to work with AWS Transfer Family](#config-efs-aws-transfer-int)

## Prerequisites for using AWS Transfer Family with Amazon EFS<a name="prerequisites-aws-transfer"></a>

To use Transfer Family to access files your Amazon EFS file system, your configuration must meet the following conditions:
+ The Transfer Family server and your Amazon EFS file system are located in the same AWS Region\.
+ IAM policies are configured to enable access to the IAM role used by AWS Transfer\. For more information, see [Create an IAM role and policy](https://docs.aws.amazon.com/transfer/latest/userguide/requirements-roles.html) in the *AWS Transfer Family User Guide*\.
+ \(Optional\) If the Transfer Family server is owned by a different account, enable cross\-account access\.
  + Ensure that your file system policy does not allow public access\. For more information, see [Blocking public access](access-control-block-public-access.md)\.
  + Modify the file system policy to enable cross\-account access\. For more information, see [Configuring cross\-account access for Transfer Family](#efs-cross-acct-access-transfer)\.

## Configuring your Amazon EFS file system to work with AWS Transfer Family<a name="config-efs-aws-transfer-int"></a>

Configuring an Amazon EFS file system to work with Transfer Family requires the following steps:
+ **Step 1\.** Get the list of POSIX IDs that are allocated to the Transfer Family users\.
+ **Step 2\.** Ensure that your file system's directories are accessible to the Transfer Family users by using the POSIX IDs allocated to the Transfer Family users\.
+ **Step 3\.** Configure IAM to enable access to the IAM role used by Transfer Family\.

### Setting file and directory permissions for Transfer Family users<a name="efs-access-aws-transfer"></a>

Make sure that the Transfer Family users have access to the necessary file and directories on your EFS file system\. Assign access permissions to the directory using the list of POSIX IDs allocated to the Transfer Family users\. In this example, a user creates a directory named `transferFam` under the EFS mount point\. Creating a directory is optional, depending on your use case\. If needed, you can choose it's name and location on the EFS file system\.

**To assign file and directory permissions to POSIX users for Transfer Family**

1. Connect to your Amazon EC2 instance\. Amazon EFS only supports mounting by Linux\-based EC2 instances\.

1. Mount your EFS file system if it is not already mounted on the EC2 instance\. For more information, see [Mounting EFS file systems](mounting-fs.md)\.

1. The following example creates the directory on the EFS file system, and changes its group to the POSIX group ID for the Transfer Family users, which is 1101 in this example\.

   1. Create the directory `efs/transferFam` using the following commands\. In practice, you can use a name and location on the file system of your choosing\.

      ```
      [ec2-user@ip-192-0-2-0 ~]$ ls 
      efs  efs-mount-point  efs-mount-point2
      [ec2-user@ip-192-0-2-0 ~]$ ls efs
      [ec2-user@ip-192-0-2-0 ~]$ sudo mkdir efs/transferFam
      [ec2-user@ip-192-0-2-0 ~]$ ls -l efs
      total 0
      drwxr-xr-x 2 root root 6 Jan  6 15:58 transferFam
      ```

   1. Use the following command to change the group of `efs/transferFam` to the POSIX GID assigned to the Transfer Family users\.

      ```
      [ec2-user@ip-192-0-2-0 ~]$ sudo chown :1101 efs/transferFam/
      ```

   1. Confirm the change\.

      ```
      [ec2-user@ip-192-0-2-0 ~]$ ls -l efs
      total 0
      drwxr-xr-x 2 root 1101 6 Jan  6 15:58 transferFam
      ```

### Enable access to the IAM role used by Transfer Family<a name="configure-iam-transfer-role"></a>

In Transfer Family, you create a resource\-based IAM policy and an IAM role that define user access to the EFS file system\. For more information, see [Create an IAM role and policy](https://docs.aws.amazon.com/transfer/latest/userguide/requirements-roles.html) in the *AWS Transfer Family User Guide*\. You must grant that Transfer Family IAM role access to your EFS file system using either an IAM identity policy or a file system policy\.

The following is an example file system policy that grants `ClientMount` \(read\) and `ClientWrite` access to the IAM role `EFS-role-for-transfer`\.

```
{
    "Version": "2012-10-17",
    "Id": "efs-policy-wizard-8698b356-4212-4d30-901e-ad2030b57762",
    "Statement": [
        {
            "Sid": "Grant-transfer-role-access",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111122223333:role/EFS-role-for-transfer"
            },
            "Action": [
                "elasticfilesystem:ClientWrite",
                "elasticfilesystem:ClientMount"
            ]
        }
    ]
}
```

For more information about creating a file system policy, see [Creating file system policies](create-file-system-policy.md)\. For more information about using identity\-based IAM policies to manage access to EFS resources, see [Managing Access to Resources](access-control-overview.md#access-control-manage-access-intro)\.

### Configuring cross\-account access for Transfer Family<a name="efs-cross-acct-access-transfer"></a>

If the Transfer Family server used to access your file system belongs to a different AWS account, you must grant that account access to your file system\. Also, your file system policy has to be non\-public\. For more information about blocking public access to your file system, see [Blocking public access](access-control-block-public-access.md)\. 

You can grant a different AWS account access to your file system in the file system policy\. In the Amazon EFS console, use the **Grant additional permissions** section of the **File system policy editor** to specify the AWS account and the level of file system access you are granting\. For more information about creating or editing a file system policy, see [Creating file system policies](create-file-system-policy.md)\. 

You can specify the account using the account ID or the account Amazon Resource Name \(ARN\)\. For more information about ARNs, see [IAM ARNs](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns) in the *IAM User Guide*\.

The following example is a non\-public file system policy that grants cross\-account access to the file system\. It has the following two statements:

1. The first statement, `NFS-client-read-write-via-fsmt`, grants read, write, and root privileges to NFS clients accessing the file system using a file system mount target\.

1. The second statement, `Grant-cross-account-access`, grants only read and write privileges to the AWS account 111122223333, which is the account that owns the Transfer Family server that needs access to this EFS file system in your account\.

```
{    
    "Statement": [
        {
            "Sid": "NFS-client-read-write-via-fsmt",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "elasticfilesystem:ClientRootAccess",
                "elasticfilesystem:ClientWrite",
                "elasticfilesystem:ClientMount"
            ],
            "Condition": {
                "Bool": {
                    "elasticfilesystem:AccessedViaMountTarget": "true"
                }
            }
        },
        {
            "Sid": "Grant-cross-account-access",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111122223333:root"
            },
            "Action": [
                "elasticfilesystem:ClientWrite",
                "elasticfilesystem:ClientMount"
            ]
        }
    ]
}
```

The following file system policy adds a statement granting access to the IAM role used by Transfer Family\.

```
{
    "Statement": [
        {
            "Sid": "NFS-client-read-write-via-fsmt",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "elasticfilesystem:ClientRootAccess",
                "elasticfilesystem:ClientWrite",
                "elasticfilesystem:ClientMount"
            ],
            "Condition": {
                "Bool": {
                    "elasticfilesystem:AccessedViaMountTarget": "true"
                }
            }
        },
        {
            "Sid": "Grant-cross-account-access",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111122223333:root"
            },
            "Action": [
                "elasticfilesystem:ClientWrite",
                "elasticfilesystem:ClientMount"
            ]
        },
        {
            "Sid": "Grant-transfer-role-access",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111122223333:role/EFS-role-for-transfer"
            },
            "Action": [
                "elasticfilesystem:ClientWrite",
                "elasticfilesystem:ClientMount"
            ]
        }
    ]
}
```