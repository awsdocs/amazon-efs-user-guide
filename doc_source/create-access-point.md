# Creating Access Points<a name="create-access-point"></a>

You can create an EFS access point using the Amazon EFS console or the AWS CLI\. You can also create access points programmatically using the AWS SDKs or the EFS API directly\. For more information about EFS access points, see [Working with Amazon EFS Access Points](efs-access-points.md)\.

The following procedures describe how to create an access point for an existing file system using the console and CLI\. To create an access point while configuring a new file system, see [Step 1: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md) in the *Getting Started* section\.

## Creating an Access Point \(Console\)<a name="create-access-point-console"></a>

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File Systems**\. 

1. On the **File systems** page, choose the file system that you want to create an access point for\.

1. For **Actions**, choose **Manage client access**\. The **Manage file system permissions** page appears\.

1. For **Access points**, choose **\+Add access point**\. The **New access points** panel expands, displaying boxes where you can enter the access point configuration information\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/efs-access-point-create-console.png)

1. Enter the following information:
   + \(Optional\) For **Name**, enter a name for the access point\.
   + \(Optional\) Under **Posix User**, enter the numeric POSIX values for **User ID** and **Group ID** if you want to assign a user to the access point and override the identity information provided by the NFS client\. A group ID is required if you specify a user ID\. Optionally, also enter any secondary group IDs\.
   + \(Optional\) For **Path**, enter a path for the access point root directory\. If you don**'**t enter one, the file system root directory is used\. The path can have a maximum of four levels\.
   + If you provide a value for **Path** that doesn't already exist on the file system, make sure to provide directory owner information under **Owner**, enter the numeric POSIX values for **Owner User ID**, **Owner Group ID**, and **Permissions**\. EFS creates the directory using this information\. For more information about root directories for access points, see [Enforcing a Root Directory with an Access Point](efs-access-points.md#enforce-root-directory-access-point)\.

1. Choose **Save access points** to save and create the access point\. The new access point is added to the list\. Choose the expand icon next to the access point ID to see the detail information\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/access-point-details-console.png)

## Creating an Access Point \(CLI\)<a name="create-access-point-cli"></a>

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