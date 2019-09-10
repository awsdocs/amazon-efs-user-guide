# Creating or Deleting Mount Targets in a VPC<a name="manage-fs-access-create-delete-mount-targets"></a>

To access an Amazon EFS file system in a VPC, you need mount targets\. For an Amazon EFS file system, the following is true:
+ You can create one mount target in each Availability Zone\. 
+ If the VPC has multiple subnets in an Availability Zone, you can create a mount target in only one of those subnets\. All EC2 instances in the Availability Zone can share the single mount target\. 

**Note**  
We recommend that you create a mount target in each of the Availability Zones\. There are cost considerations for mounting a file system on an EC2 instance in an Availability Zone through a mount target created in another Availability Zone\. For more information, see [Amazon EFS](https://aws.amazon.com/efs/)\. In addition, by always using a mount target local to the instance's Availability Zone, you eliminate a partial failure scenario\. If the mount target's zone goes down, you can't access your file system through that mount target\. 

For more information about the operation, see [CreateMountTarget](API_CreateMountTarget.md)\.

You can delete mount targets\. A mount target deletion forcibly breaks any mounts of the file system via that mount target, which might disrupt instances or applications using those mounts\. For more information, see [DeleteMountTarget](API_DeleteMountTarget.md)\.

## Using the Console<a name="manage-fs-create-delete-mt-console"></a>

Use the following procedure to create new mount targets or delete or update existing mount targets using the AWS Management Console\.

**To create new mount targets or update or delete existing ones \(console\)**

1. In the Amazon EFS console, choose the file system, and for **Actions** choose **Manage File System Access**\. 

   The console displays the **Manage File System Access** page with a list of file system mount targets you have created in the selected VPC\. The console shows a list of Availability Zones and mount target information, if there is a mount target in that Availability Zone\.

   The console shows that the file system has one mount target in the **eu\-west\-2c** Availability Zone, as shown following:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/manage-fs-05.png)

1. To create new mount targets

   1. Click on the left side in the specific **Availability Zone** row\.

   1. If the Availability Zone has multiple subnets, select a subnet from the **Subnet** list\. 

   1. Amazon EFS automatically selects an available IP address, or you can provide another IP address explicitly\.

   1. Choose a **Security Group** from the list\. 

      For more information about security groups, see [Amazon EC2 Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. To delete a mount target, choose the **X** next to the Availability Zone from which you want to remove a mount target\.

## Using the AWS CLI<a name="manage-fs-create-delete-mt-cli"></a>

To create a mount target, use the `create-mount-target` AWS CLI command \(corresponding operation is [CreateMountTarget](API_CreateMountTarget.md)\), as shown following:

```
$ aws efs create-mount-target \
--file-system-id file-system-ID (for which to create the mount target) \
--subnet-id  vpc-subnet-ID (in which to create mount target) \
--security-group security-group IDs (to associate with the mount target) \
--region aws-region (for example, us-west-2) \
--profile adminuser
```

The AWS Region \(the `region` parameter\) must be the VPC region\. 

You can get a list of mount targets created for a file system using the `describe-mount-target` AWS CLI command \(corresponding operation is [DescribeMountTargets](API_DescribeMountTargets.md)\), as shown following: 

```
$ aws efs describe-mount-targets \
--file-system-id file-system-ID \
--region aws-region-where-file-system-exists \
--profile adminuser
```

Here's a sample response:

```
{
    "MountTargets": [
        {
            "MountTargetId": "fsmt-52a643fb",
            "NetworkInterfaceId": "eni-f11e8395",
            "FileSystemId": "fs-6fa144c6",
            "LifeCycleState": "available",
            "SubnetId": "subnet-15d45170",
            "OwnerId": "23124example",
            "IpAddress": "10.0.2.99"
        },
        {
            "MountTargetId": "fsmt-55a643fc",
            "NetworkInterfaceId": "eni-14a6ae4d",
            "FileSystemId": "fs-6fa144c6",
            "LifeCycleState": "available",
            "SubnetId": "subnet-0b05fc52",
            "OwnerId": "23124example",
            "IpAddress": "10.0.19.174"
        }
    ]
}
```

To delete an existing mount target, use the `delete-mount-target` AWS CLI command \(corresponding operation is [DeleteMountTarget](API_DeleteMountTarget.md)\), as shown following:

```
$ aws efs delete-mount-target \
--mount-target-id mount-target-ID-to-delete \
--region aws-region-where-mount-target-exists \
--profile adminuser
```