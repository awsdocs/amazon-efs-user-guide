# Working with Amazon EFS Access Points<a name="efs-access-points"></a>

Amazon EFS *access points *are application\-specific entry points into an EFS file system that make it easier to manage application access to shared datasets\. Access points can enforce a user identity, including the user's POSIX groups, for all file system requests that are made through the access point\. Access points can also enforce a different root directory for the file system so that clients can only access data in the specified directory or its subdirectories\.

You can use AWS Identity and Access Management \(IAM\) policies to enforce that specific applications use a specific access point\. By combining IAM policies with access points, you can easily provide secure access to specific datasets for your applications\. 

For more information on creating an access point, see [Creating and deleting access points](create-access-point.md)\.

**Topics**
+ [Creating an Access Point](#efs-access-poiont-create)
+ [Mounting a File System Using an Access Point](#mount-with-access-point)
+ [Enforcing a User Identity Using an Access Point](#enforce-identity-access-points)
+ [Enforcing a Root Directory with an Access Point](#enforce-root-directory-access-point)
+ [Using Access Points in IAM Policies](#access-points-iam-policy)

## Creating an Access Point<a name="efs-access-poiont-create"></a>

You can create access points for an existing Amazon EFS file system using the AWS Management Console, the AWS Command Line Interface \(AWS CLI\), and the EFS API\. 

For directions about how to create an access point, see [Creating and deleting access points](create-access-point.md)\.

## Mounting a File System Using an Access Point<a name="mount-with-access-point"></a>

You use the EFS mount helper when mounting a file system using an access point\. In the mount command, include file system ID, the access point ID, and the `tls` mount option, as shown in the following example\.

```
$ mount -t efs -o tls,accesspoint=fsap-12345678 fs-12345678: /localmountpoint
```

For more information on mounting file systems using an access point, see [Mounting with EFS access points](mounting-fs.md#mounting-access-points)\.

## Enforcing a User Identity Using an Access Point<a name="enforce-identity-access-points"></a>

You can use an access point to enforce user and group information for all file system requests made through the access point\. To enable this feature, you need to specify the operating system identity to enforce when you create the access point\.

As part of this, you provide the following:
+ User ID – The numeric POSIX user ID for the user\.
+ Group ID – The numeric POSIX group ID for the user\.
+ Secondary group IDs – An optional list of secondary group IDs\.

When user enforcement is enabled, Amazon EFS replaces the NFS client's user and group IDs with the identity configured on the access point for all file system operations\. User enforcement also does the following:
+ The owner and group for new files and directories are set to the user ID and group ID of the access point\.
+ EFS considers the user ID, group ID, and secondary group IDs of the access point when evaluating file system permissions\. EFS ignores the NFS client's IDs\.

**Important**  
Enforcing a user identity is subject to the `ClientRootAccess` IAM permission\.   
For example, in some cases you might configure the access point user ID, group ID, or both to be root \(that is, setting the UID, GID, or both to 0\)\. In such cases, you must grant the `ClientRootAccess` IAM permission to the NFS client\.

## Enforcing a Root Directory with an Access Point<a name="enforce-root-directory-access-point"></a>

You can use an access point to override the root directory for a file system\. When you enforce a root directory, the NFS client using the access point uses the root directory configured on the access point instead of the file system's root directory\. 

You enable this feature by setting the access point `Path` attribute when creating an access point\. The `Path` attribute is the full path of the root directory of the file system for all file system requests made through this access point\. The full path can't exceed 100 characters in length\. It can include up to four subdirectories\.

When you specify a root directory on an access point, it becomes the root directory of the file system for the NFS client mounting the access point\. For example, suppose that the root directory of your access point is `/data`\. In this case, mounting `fs-12345678:/` using the access point has the same effect as mounting `fs-12345678:/data` without using the access point\. 

When specifying a root directory in your access point, ensure that the directory permissions are configured to allow the user of the access point to successfully mount the file system\. Specifically, make sure that the execute bit is set for the access point user or group, or for everyone\. For example, a directory permission value of 755 allows the directory user owner to list files, create files, and mount, and all other users to list files and mount\.

### Creating the Root Directory for an Access Point<a name="create-root-directory-access-point"></a>

If a root directory path for an access point doesn't exist on the file system, Amazon EFS automatically creates that root directory with configurable ownership and permissions\. This approach makes it possible to provision file system access for a specific user or application without mounting your file system from a Linux host\. You can configure the root directory ownership and permission by using the following attributes when creating an access point:
+ `OwnerUid` – The numeric POSIX user ID to use as the root directory owner\.
+ `OwnerGiD` – The numeric POSIX group ID to use as the root directory owner group\.
+ Permissions – The Unix mode of the directory\. A common configuration is 755\. Ensure that the execute bit is set for the access point user so they are able to mount\. This configuration gives the directory owner permission to enter, list, and write new files in the directory\. It gives all other users permission to enter and list files\. For more information on working with Unix file and directory modes, see [Working with Users, Groups, and Permissions at the Network File System \(NFS\) Level](accessing-fs-nfs-permissions.md)\.

When you mount a file system with an access point, the root directory for the access point is created if the directory doesn't already exist\. If the root directory configured on the access point already exists before mount time, the existing permissions aren't overwritten by the access point\. If you delete the root directory, EFS recreates it the next time that the file system is mounted using the access point\.

### Security Model for Access Point Root Directories<a name="root-directory-security-access-point"></a>

When a root directory override is in effect, Amazon EFS behaves like a Linux NFS server with the `no_subtree_check` option enabled\. 

In the NFS protocol, servers generate file handles that are used by clients as unique references when accessing files\. EFS securely generates file handles that are unpredictable and specific to an EFS file system\. When a root directory override is in place, EFS doesn't disclose file handles for files outside the specified root directory\. However, in some cases a user might get a file handle for a file outside of their access point by using an out\-of\-band mechanism\. For example, they might do so if they have access to a second access point\. If they do this, they can perform read and write operations on the file\. 

File ownership and access permissions are always enforced, for access to files within and outside of a user's access point root directory\. 

## Using Access Points in IAM Policies<a name="access-points-iam-policy"></a>

You can use an IAM policy to enforce that a specific NFS client, identified by its IAM role, can only access a specific access point\. To do this, you use the `elasticfilesystem:AccessPointArn` IAM condition key\. The `AccessPointArn` is the Amazon Resource Name \(ARN\) of the access point that the file system is mounted with\.

Following is an example of a file system policy that allows the IAM role `app1` to access the file system using access point `fsap-01234567`\. The policy also allows `app2` to use the file system using access point `fsap-89abcdef`\.

```
{
    "Version": "2012-10-17",
    "Id": "MyFileSystemPolicy",
    "Statement": [
        {
            "Sid": "App1Access",
            "Effect": "Allow",
            "Principal": { "AWS": "arn:aws:iam::111122223333:role/app1" },
            "Action": [
                "elasticfilesystem:ClientMount",
                "elasticfilesystem:ClientWrite"
            ],
            "Condition": {
                "StringEquals": {
                    "elasticfilesystem:AccessPointArn" : "arn:aws:elasticfilesystem:us-east-1:222233334444:access-point/fsap-01234567"
                }
            }
        },
        {
            "Sid": "App2Access",
            "Effect": "Allow",
            "Principal": { "AWS": "arn:aws:iam::111122223333:role/app2" },
            "Action": [
                "elasticfilesystem:ClientMount",
                "elasticfilesystem:ClientWrite"
            ],
            "Condition": {
                "StringEquals": {
                    "elasticfilesystem:AccessPointArn" : "arn:aws:elasticfilesystem:us-east-1:222233334444:access-point/fsap-89abcdef"
                }
            }
        }
    ]
}
```