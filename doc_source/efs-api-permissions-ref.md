# Amazon EFS API Permissions: Actions, Resources, and Conditions Reference<a name="efs-api-permissions-ref"></a>

When you are setting up [Access Control](auth-and-access-control.md#access-control) and writing a permissions policy that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table lists each Amazon EFS API operation, the corresponding actions for which you can grant permissions to perform the action, and the AWS resource for which you can grant the permissions\. You specify the actions in the policy's `Action` field, and you specify the resource value in the policy's `Resource` field\. 

You can use AWS\-wide condition keys in your Amazon EFS policies to express conditions\. For a complete list of AWS\-wide keys, see [Available Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. 

**Note**  
To specify an action, use the `elasticfilesystem:` prefix followed by the API operation name \(for example, `elasticfilesystem:CreateFileSystem`\)\.

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.


**Amazon EFS API and Required Permissions for Actions**  

| Amazon EFS API Operations | Required Permissions \(API Actions\) | Resources | 
| --- | --- | --- | 
|   [CreateFileSystem](API_CreateFileSystem.md)   | elasticfilesystem:CreateFileSystem For information about KMS\-related permissions for encrypted file systems, see [Amazon EFS Key Policies for AWS KMS](encryption.md#EFSKMSPolicy)\.   |  `arn:aws:elasticfilesystem:region:account-id:file-system/*`  | 
|   [CreateMountTarget](API_CreateMountTarget.md)  |  `elasticfilesystem:CreateMountTarget` `ec2:DescribeSubnets` `ec2:DescribeNetworkInterfaces` `ec2:CreateNetworkInterface`  | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
|   [CreateTags](API_CreateTags.md)   | elasticfilesystem:CreateTags | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
| [DeleteFileSystem](API_DeleteFileSystem.md) | elasticfilesystem:DeleteFileSystem | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
| [DeleteMountTarget](API_DeleteMountTarget.md)  |  `elasticfilesystem:DeleteMountTarget` `ec2:DeleteNetworkInterface`  | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
|  [DeleteTags](API_DeleteTags.md) | elasticfilesystem:DeleteTags | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
| [DescribeFileSystems](API_DescribeFileSystems.md) | elasticfilesystem:DescribeFileSystems |  `arn:aws:elasticfilesystem:region:account-id:file-system/file-system-id` or `arn:aws:elasticfilesystem:region:account-id:file-system/*`  | 
| [DescribeMountTargetSecurityGroups](API_DescribeMountTargetSecurityGroups.md) |  `elasticfilesystem:DescribeMountTargetSecurityGroups` `ec2:DescribeNetworkInterfaceAttribute`  | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
| [DescribeMountTargets](API_DescribeMountTargets.md) | elasticfilesystem:DescribeMountTargets | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
| [DescribeTags](API_DescribeTags.md) | elasticfilesystem:DescribeTags | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
| [ModifyMountTargetSecurityGroups](API_ModifyMountTargetSecurityGroups.md) |  `elasticfilesystem:ModifyMountTargetSecurityGroups` ` ec2:ModifyNetworkInterfaceAttribute `  | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 
| [UpdateFileSystem](API_UpdateFileSystem.md) |  `elasticfilesystem:UpdateFileSystem`  | arn:aws:elasticfilesystem:region:account\-id:file\-system/file\-system\-id | 