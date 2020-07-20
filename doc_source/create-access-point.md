# Creating and deleting access points<a name="create-access-point"></a>

You can create Amazon EFS access points using the AWS Management Console or the AWS CLI\. You can also create access points programmatically using the AWS SDKs or the Amazon EFS API directly\. For more information about EFS access points, see [Working with Amazon EFS Access Points](efs-access-points.md)\.

The following procedures describe how to create an access point using the console and the AWS CLI\.

**Note**  
The new Amazon EFS management console is not available in the following AWS Regions: Europe \(Milan\) Region, Africa \(Cape Town\) Region, Beijing and Ningxia Regions, or AWS GovCloud \(US\)\. If you are accessing the Amazon EFS console in these regions, you can access the user documentation for that console experience here: [ AWS Elastic File System User Guide](images/AmazonElasticFileSystem-UserGuide-console1.pdf)\.

## Creating an access point \(console\)<a name="console2-create-access-point"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Access points** to open the **Access points** window\.

1. Choose **Create access point** to display the **Create access point** page\.

   You can also open the **Create access point** page by choosing **File Systems**\. Choose a file system **Name** or **File system ID** and then choose **Access points** and **Create access point** to create an access point for that file system\.  
![\[Console screen shot showing the Create access point page where you create and edit access points in the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-create-access-point.png)

   1. Enter the following information in the **Details** panel:
      + **File system** – Enter a file system name or ID and choose the matching file system, or just choose from the list that appears when you choose the input field\.
      + \(Optional\) **Name** – Enter a name for the access point\.
      + \(Optional\) **Root directory path** – You can specify a root directory for the access point; the default access point root is /\. To enter a root directory path, use the format `/foo/bar`\. For more information, see [Enforcing a Root Directory with an Access Point](efs-access-points.md#enforce-root-directory-access-point)\.

   1. \(Optional\) In the **POSIX user** panel, you can specify the full POSIX identity to use to enforce user and group information for all file operations by NFS clients using the access point\. For more information, see [Enforcing a User Identity Using an Access Point](efs-access-points.md#enforce-identity-access-points)\.
      + **User ID** – Enter a numeric POSIX user ID for the user\.
      + **Group ID** – Enter a numeric POSIX group ID for the user\.
      + **Secondary group IDs** – Enter an optional comma\-separated list of secondary group IDs\.

   1. \(Optional\) For **Root directory creation permissions** you can specify the permissions to use when Amazon EFS creates the root directory path, if specified and it doesn't already exist\. For more information, see [Enforcing a Root Directory with an Access Point](efs-access-points.md#enforce-root-directory-access-point)\.
      + **Owner user ID** – enter the numeric POSIX user ID to use as the root directory owner\.
      + **Owner group ID** – enter the numeric POSIX group ID to use as the root directory owner group\.
      + **Permissions** – enter the Unix mode of the directory\. A common configuration is 755\. Ensure that the execute bit is set for the access point user so they are able to mount\. 

1. Choose **Create access point** to create the access point using this configuration\.

## Creating an access point \(CLI\)<a name="create-access-point-cli"></a>

In the following example, the `create-access-point` CLI command creates an access point for the file system\. The equivalent API command is [CreateAccessPoint](API_CreateAccessPoint.md)\.

```
aws efs create-access-point --file-system-id fs-01234567 --client-token 010102020-3
{
    "ClientToken": "010102020-3",
    "Tags": [],
    "AccessPointId": "fsap-092e9f80b3fb5e6f3",
    "AccessPointArn": "arn:aws:elasticfilesystem:us-east-2:111122223333:access-point/fsap-092e9f80b3fb5e6f3",
    "FileSystemId": "fs-01234567",
    "RootDirectory": {
        "Path": "/"
    },
    "OwnerId": "111122223333",
    "LifeCycleState": "creating"
}
```

## Deleting an access point<a name="delete-access-point"></a>

When you delete an access point, any clients using the access point lose access to the Amazon EFS file system that it's configured for\.

### Deleting an access point \(console\)<a name="delete-ap-console"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. In the left navigation pane, choose **Access points** to open the **Access points** page\.

1. Select the access point to delete\.

1. Choose **Delete**\.

1. Choose **Confirm** to confirm the action and delete the access point\.

### Deleting an access point \(CLI\)<a name="delete-ap-cli"></a>

In the following example, the `delete-access-point` CLI command deletes the specified access point\. The equivalent API command is [DeleteAccessPoint](API_DeleteAccessPoint.md)\. If the command is successful, the service returns an HTTP 204 response with an empty HTTP body\.

```
aws efs delete-access-point --access-point-id fsap-092e9f80b3fb5e6f3 --client-token 010102020-3
```