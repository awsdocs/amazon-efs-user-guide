# Creating File System Policies<a name="create-file-system-policy"></a>

You can create a file system policy using the Amazon EFS console or using the AWS CLI\. You can also create a file system policy programmatically using AWS SDKs or the EFS API directly\. To learn more about file system policies and see examples, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\.

## Creating a File System Policy \(Console\)<a name="create-file-system-policy-console"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File Systems**\.

1. On the **File systems** page, choose the file system that you want to crate a file system policy for\.

1. For **Actions**, choose **Manage client access**\. The **File system policy** page appears\.

1. On the **Policy settings** tab, choose one or more of the preset policy statements available\.

   Or choose the **\{\} JSON** tab to configure your own policy using the JSON editor\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/create-fsp-console.png)

1. After you complete creating your policy, choose **Set policy**\. If you chose one of the policy presets, the **\{\} JSON** tab displays the JSON formatted policy\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/root-squash-json.png)

    The policy shown is the JSON that is generated if you chose the **Disable root access by default** policy\.

1. Choose **Save policy** to save the file system policy\.

## Creating a File System Policy \(CLI\)<a name="create-file-system-policy-cli"></a>

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