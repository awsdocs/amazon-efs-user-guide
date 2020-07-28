# Using IAM to Control NFS Access to Amazon EFS<a name="iam-access-control-nfs-efs"></a>

 You can use both IAM identity policies and resource policies to control NFS client access to Amazon EFS resources in a way that is scalable and optimized for cloud environments\. Using IAM, you can permit clients to perform specific actions on a file system, including read\-only, write, and root access\. 

 NFS clients can identify themselves using an IAM role when connecting to an EFS file system\. When a client connects to a file system, Amazon EFS evaluates the file system’s IAM resource policy, which is called a file system policy, along with any identity\-based IAM policies to determine the appropriate file system access permissions to grant\. 

When you use IAM authorization for NFS clients, client connections and IAM authorization decisions are logged to AWS CloudTrail\. For more information about how to log Amazon EFS API calls with CloudTrail, see [Logging Amazon EFS API Calls with AWS CloudTrail](logging-using-cloudtrail.md)\. 

**Important**  
You must use the EFS mount helper to mount your Amazon EFS file systems in order to use IAM authorization to control access by NFS clients\. For more information, see [Mounting with IAM authorization](mounting-fs.md#mounting-IAM-option)\.

## Default EFS File System Policy<a name="default-filesystempolicy"></a>

The default EFS file system policy grants full access to any NFS client that can connect to the file system\. The default policy is in effect whenever a user\-configured file system policy doesn't exist, including at file system creation\. Whenever the default file system policy is in effect, a `` API operation returns a `PolicyNotFound` response\.

## EFS Actions for NFS Clients<a name="efs-filesystempolicy-actions"></a>

You can specify the following actions for NFS clients on a file system using a file system policy\.


| Action | Description | 
| --- | --- | 
|  `elasticfilesystem:ClientMount`  |  Provides an NFS client read\-only access to a file system\.  | 
|  `elasticfilesystem:ClientWrite`  |  Provides an NFS client with write permissions on a file system\.  | 
|  `elasticfilesystem:ClientRootAccess`  |  Provides an NFS client the ability to use the root user when accessing a file system\.  | 

## EFS Condition Keys for NFS Clients<a name="efs-condition-keys-for-nfs"></a>

To express conditions, you use predefined condition keys\. Amazon EFS has the following predefined condition keys for NFS clients\.


| EFS Condition Key | Description | Operator | 
| --- | --- | --- | 
|  aws:SecureTransport  |  Use this key to require NFS clients to use TLS when connecting to an EFS file system\.  |  Boolean  | 
| elasticfilesystem:AccessPointArn | ARN of the EFS access point that the client is connecting to\. | 

## File System Policy Examples<a name="file-sys-policy-examples"></a>

In this section, you can find example file system policies that grant or deny permissions for various Amazon EFS actions\. For information about the elements of a resource\-based policy, see [Specifying Policy Elements: Actions, Effects, and Principals](access-control-overview.md#access-control-specify-efs-actions)\.

**Important**  
 If you grant permission to an individual IAM user or role in a file system policy, don't delete or recreate that user or role while the policy is still in effect on the file system\. If this happens, that user or role is effectively locked out from file system and will not be able to access it\. For more information, see [Specifying a Principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html#Principal_specifying) in the *IAM User Guide*\. 

### Example 1: Grant Read and Write Access to all IAM Principals<a name="file-sys-policy-readonly"></a>

This example EFS file system policy has the following characteristics:
+ The effect is `Allow`\.
+ The principal is set to "\*", all IAM entities\.
+ The action is set to `ClientMount`, `ClientWrite`, and `ClientRootAccess`\.
+ The condition for granting permissions is set to `SecureTransport`—only NFS clients using TLS to connect to the file system are granted access\.

```
{
    "Version": "2012-10-17",
    "Id": "ExamplePolicy01",
    "Statement": [
        {
            "Sid": "ExampleSatement01",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "elasticfilesystem:ClientMount",
                "elasticfilesystem:ClientWrite"
            ],
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": "true"
                }
            }
        }
    ]
}
```

### Example 2: Grant Read\-Only Access<a name="file-sys-policy-readonly"></a>

The following file system policy only grants `ClientMount`, or read\-only, permissions to all IAM principals\.

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

To learn how to set additional file system policies, including denying root access to all IAM principals, except for a specific management workstation, see [Walkthrough: Enable Root Squashing Using IAM Authorization for NFS Clients](enable-root-squashing.md)\.

### Example 3: Grant Access to a EFS Access Point<a name="file-sys-policy-accessprofile-efs"></a>

You use an EFS access policy to provide an NFS client with an application specific view into shared file\-based datasets on an EFS file system\. You grant the access point permissions on the file system using a file system policy\. This file policy example uses a condition element to grant a specific access point that is identified by its ARN full access to the file system\. For more information about EFS access points, see [Working with Amazon EFS Access Points](efs-access-points.md)\.

```
{
    "Version": "2012-10-17",
    "Id": "access-point-example03",
    "Statement": [
        {
            "Sid": "access-point-statement-example03",
            "Effect”: "Allow",
            "Principal": {"arn:aws:iam::account_id:role/myapp"},
            "Action": "elasticfilesystem:Client*",
            "Condition": { 
                "StringEquals": {
                    "elasticfilesystem:AccessPointArn":"arn:aws:elasticfilesystem:region:account_id:access-point/access_point_id" } 
            }
            
        }
    ]
}
```
