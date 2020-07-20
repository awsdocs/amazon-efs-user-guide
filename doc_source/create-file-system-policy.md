# Creating file system policies<a name="create-file-system-policy"></a>

You can create a file system policy using the Amazon EFS console or using the AWS CLI\. You can also create a file system policy programmatically using AWS SDKs or the Amazon EFS API directly\. To learn more about file system policies and see examples, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\.

## Creating a file system policy \(console\)<a name="create-file-system-policy-console"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File Systems**\.

1. On the **File systems** page, choose the file system that you want to edit or create a file system policy for\. The details page for that file system is displayed\.

1. Choose **File system policy**, then choose **Edit**\. The **File system policy** page appears\.  
![\[File system policy editor is where you create and edit file system policies in the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fsp-editor.png)

1. In **Policy options**, you can choose any combination of the following preconfigured policies:
   + Prevent root access by default
   + Enforce read\-only access by default
   + Enforce in\-transit encryption for all clients

   If you choose a preconfigured policy, the policy JSON object is displayed in the **Policy editor** panel\.

1. Use the **Policy editor** to customize a preconfigured policy or to create your own policy\. When you use the editor, the preconfigured policy options become unavailable\. To undo your policy changes, choose **Clear**\.

   When you clear the editor, the preconfigured policies become available once again\.

1. After you complete editing or creating the policy, choose **Save**\. The details page for the file system is displayed, showing the policy in **File system policy**\.

## Creating a file system policy \(CLI\)<a name="create-file-system-policy-cli"></a>

In the following example, the `put-file-system-policy` CLI command creates a file system policy that allows all IAM principals read\-only access to the EFS file system\. The equivalent API command is [PutFileSystemPolicy](API_PutFileSystemPolicy.md)\.

```
aws efs put-file-system-policy --file-system-id fs-01234567 --policy '{
    "Version": "2012-10-17",
    "Id": "1",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "elasticfilesystem:ClientMount"
            ],
            "Principal": {
                "AWS": "*"
            }
        }                                                                                                 
    ]
}'
```

```
{
    "FileSystemId": "fs-01234567",
    "Policy": "{
    "Version" : "2012-10-17",
    "Id" : "1",
    "Statement" : [
        {
           "Sid" : "efs-statement-7c8d8687-1c94-4fdc-98b7-111122223333",
           "Effect" : "Allow",
           "Principal" : {
              "AWS" : "*"
           },
           "Action" : [ 
              "elasticfilesystem:ClientMount" 
           ],
           "Resource" : "arn:aws:elasticfilesystem:us-east-2:111122223333:file-system/fs-01234567"
           } 
        ]
    }
}
```