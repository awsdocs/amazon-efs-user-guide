# Creating an Amazon Elastic File System<a name="creating-using-create-fs"></a>

Following, you can find an explanation about how to create an Amazon EFS file system and optional tags for the file system\. This section explains how to create these resources using both the console and the AWS Command Line Interface \(AWS CLI\)\. 

**Note**  
If you are new to Amazon EFS, we recommend you go through the Getting Started exercise, which provides console\-based end\-to\-end instructions to create and access a file system in your VPC\. For more information, see [Getting Started](getting-started.md)\. 


+ [Requirements](#reqs-fs-create)
+ [Permissions Required](#perm-fs-api)
+ [Creating a File System](#creating-using-create-fs-part1)

## Requirements<a name="reqs-fs-create"></a>

To create a file system, the only requirement is that you create a token to ensure idempotent operation\. If you use the console, it generates the token for you\. For more information, see [CreateFileSystem](API_CreateFileSystem.md)\. After you create a file system, Amazon EFS returns the file system description as JSON\. Following is an example\. 

```
{
    "SizeInBytes": {
        "Value": 6144
    },
    "CreationToken": "console-d7f56c5f-e433-41ca-8307-9d9c0example",
    "CreationTime": 1422823614.0,
    "FileSystemId": "fs-c7a0456e",
    "PerformanceMode" : "generalPurpose",
    "NumberOfMountTargets": 0,
    "LifeCycleState": "available",
    "OwnerId": "231243201240"
}
```

If you use the console, the console displays this information in the user interface\.

After creating a file system, you can create optional tags for the file system\. Initially, the file system has no name\. You can create a **Name** tag to assign a file system name\. Amazon EFS provides the [CreateTags](API_CreateTags.md) operation for creating tags\. Each tag is simply a key\-value pair\. 

## Permissions Required<a name="perm-fs-api"></a>

For all operations, such as creating a file system and creating tags, a user must have AWS Identity and Access Management permissions for the corresponding API action and resource\. 

You can perform any Amazon EFS operations using the root credentials of your AWS account, but using root credentials is not recommended\. If you create IAM users in your account, you can grant them permissions for Amazon EFS actions with user policies\. You can also use roles to grant cross\-account permissions\. For more information about managing permissions for the API actions, see [Authentication and Access Control for Amazon EFS](auth-and-access-control.md)\.

## Creating a File System<a name="creating-using-create-fs-part1"></a>

You can create a file system using the Amazon EFS console or using the AWS Command Line Interface\. You can also create file systems programmatically using AWS SDKs\.

### Creating a File System Using the Amazon EFS Console<a name="creating-using-fs-part1-console"></a>

The Amazon EFS console provides an integrated experience\. In the console, you can specify VPC subnets to create mount targets and optional file system tags when you create a file system\.

To create the file system mount targets in your VPC, you must specify VPC subnets\. The console prepopulates the list of VPCs in your account that are in the selected AWS Region\. First, you select your VPC, and then the console lists the Availability Zones in the VPC\. For each Availability Zone, you can select a subnet from the list\. After you select a subnet, you can either specify an available IP address in the subnet or let Amazon EFS choose an address\.

When creating a file system, you also choose a performance mode\. There are two performance modes to choose from—General Purpose and Max I/O\. For the majority of use cases, we recommend that you use the general purpose performance mode for your file system\. For more information about the different performance modes, see [Performance Modes](performance.md#performancemodes)\.

You can enable encryption when creating a file system\. If you enable encryption for your file system, all data and metadata stored on it is encrypted\. For more information about EFS encryption, see [Security](security-considerations.md)\.

When you choose **Create File System**, the console sends a series of API requests to create the file system\. The console then sends API requests to create tags and mount targets for the file system\. The following example console shows the **MyFS** file system\. It has the **Name** tag and three mount targets that are being created\. The mount target lifecycle state must be **Available** before you can use it to mount the file system on an EC2 instance\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/create-fs-descriptions-10.png)

For instructions on how to create an Amazon EFS file system using the console, see [Step 1: Create Your EC2 Resources and Launch Your EC2 Instance](gs-step-one-create-ec2-resources.md)\.

### Creating a File System Using the AWS CLI<a name="creating-using-fs-part1-cli"></a>

When using the AWS CLI, you create these resources in order\. First, you create a file system\. Then, you can create mount targets and optional tags for the file system using corresponding AWS CLI commands\.

The following examples use the `adminuser` as the `profile` parameter value\. You need to use an appropriate user profile to provide your credentials\. For information about the AWS CLI, see [Getting Set Up with the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\. 

+ To create a file system, use the Amazon EFS `create-file-system` CLI command \(corresponding operation is [CreateFileSystem](API_CreateFileSystem.md)\), as shown following\.

  ```
  $ aws efs create-file-system \
  --creation-token creation-token \
  --region aws-region \
  --profile adminuser
  ```

  For example, the following `create-file-system` command creates a file system in the **us\-west\-2** region\. The command specifies **MyFirstFS** as the creation token\. For a list of AWS regions where you can create an Amazon EFS file system, see the [Amazon Web Services General Reference](http://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem_region)\.

  ```
  $  aws efs create-file-system \
  --creation-token MyFirstFS \
  --region us-west-2 \
  --profile adminuser
  ```

  After successfully creating the file system, Amazon EFS returns the file system description as JSON, as shown in the following example\.

  ```
  {
      "SizeInBytes": {
          "Value": 6144
      },
      "CreationToken": "MyFirstFS",
      "CreationTime": 1422823614.0,
      "FileSystemId": "fs-c7a0456e",
      "PerformanceMode" : "generalPurpose",
      "NumberOfMountTargets": 0,
      "LifeCycleState": "available",
      "OwnerId": "231243201240"
  }
  ```

  Amazon EFS also provides the `describe-file-systems` CLI command \(corresponding operation is [DescribeFileSystems](API_DescribeFileSystems.md)\) that you can use to retrieve a list of file systems in your account, as shown following:

  ```
  $  aws efs describe-file-systems \
  --region aws-region \
  --profile adminuser
  ```

  Amazon EFS returns a list of the file systems in your AWS account created in the specified region\.

+ To create tags, use the Amazon EFS `create-tags` CLI command \(the corresponding API operation is [CreateTags](API_CreateTags.md)\)\. The following example command adds the `Name` tag to the file system\.

  ```
  aws efs create-tags \
  --file-system-id File-System-ID \
  --tags Key=Name,Value=SomeExampleNameValue \
  --region aws-region \
  --profile adminuser
  ```

  You can retrieve a list of tags created for a file system using the `describe-tags` CLI command \(corresponding operation is [DescribeTags](API_DescribeTags.md)\), as shown following\.

  ```
  aws efs describe-tags \
  --file-system-id File-System-ID \
  --region aws-region \
  --profile adminuser
  ```

  Amazon EFS returns these descriptions as JSON\. The following is an example of tags returned by the `DescribeTags` operation\. It shows a file system as having only the `Name` tag\.

  ```
  {
      "Tags": [
          {
              "Value": "MyFS",
              "Key": "Name"
          }
      ]
  }
  ```