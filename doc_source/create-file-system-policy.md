# Creating file system policies<a name="create-file-system-policy"></a>

You can create a file system policy using the Amazon EFS console or using the AWS CLI\. You can also create a file system policy programmatically using AWS SDKs or the Amazon EFS API directly\. EFS file system policies have a 20,000 character limit\. For more information about using an EFS file system policy and examples, see [Using IAM to control file system data access](iam-access-control-nfs-efs.md)\.

**Note**  
Amazon EFS file system policy changes can take several minutes to take effect\.

## Creating a file system policy \(console\)<a name="create-file-system-policy-console"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File Systems**\.

1. On the **File systems** page, choose the file system that you want to edit or create a file system policy for\. The details page for that file system is displayed\.

1. Choose **File system policy**, then choose **Edit**\. The **File system policy** page appears\.  
![\[File system policy editor is where you create and edit file system policies in the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fsp-editor.png)

1. In **Policy options**, you can choose any combination of the preconfigured file system policies:
   + Prevent root access by default \- This option removes ClientRootAccess from the set of allowed EFS actions\.
   + Enforce read\-only access by default \- This option removes ClientWriteAccess from the set of allowed EFS actions\.
   + Prevent anonymous access \- This option removes ClientMount from the set of allowed EFS actions\.
   + Enforce in\-transit encryption for all clients \- This option denies access to unencrypted clients\.

   When you choose a preconfigured policy, the policy JSON object is displayed in the **Policy editor** panel\.

1. Use **Grant additional permissions** to grant file system permissions to additional IAM principals, including another AWS account\. Choose **Add**, then enter the Principal ARN of the entity to which you are granting permissions to, then choose the **Permissions** to grant\. The additional permissions are shown in the **Policy editor**\.

1. You can use the **Policy editor** to customize a preconfigured policy or to create your own file system policy\. When you use the editor, the preconfigured policy options become unavailable\. To clear the current file system policy and start creating a new policy, choose **Clear**\.

   When you clear the editor, the preconfigured policies become available once again\.

1. After you complete editing the policy, choose **Save**\.

## Creating a file system policy \(CLI\)<a name="create-file-system-policy-cli"></a>

In the following example, the [https://docs.aws.amazon.com/cli/latest/reference/efs/put-file-system-policy.html](https://docs.aws.amazon.com/cli/latest/reference/efs/put-file-system-policy.html) CLI command creates a file system policy that allows the specified AWS account read\-only access to the EFS file system\. The equivalent API command is [PutFileSystemPolicy](API_PutFileSystemPolicy.md)\.

```
aws efs put-file-system-policy --file-system-id fs-01234567 --policy '{
    "Id": "1",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "elasticfilesystem:ClientMount"
            ],
            "Principal": {
                "AWS": "arn:aws:iam::111122223333:root"
            }
        }                                                                                                 
    ]
}
```

```
{
    "FileSystemId": "fs-01234567",
    "Policy": "{
    "Version" : "2012-10-17",
    "Id" : "1",
    "Statement" : [
        {
           "Sid" : "efs-statement-7c8d8687-1c94-4fdc-98b7-555555555555",
           "Effect" : "Allow",
           "Principal" : {
              "AWS" : "arn:aws:iam::111122223333:root"
           },
           "Action" : [ 
              "elasticfilesystem:ClientMount" 
           ],
           "Resource" : "arn:aws:elasticfilesystem:us-east-2:555555555555:file-system/fs-01234567"
           } 
        ]
    }
}
```