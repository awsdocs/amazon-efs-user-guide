# Creating Mount Targets<a name="accessing-fs"></a>

After you create a file system, you can create mount targets and then you can mount the file system on EC2 instances in your VPC, as shown in the following illustration\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/overview-flow.png)

For more information about creating a file system, see [Creating an Amazon Elastic File System](creating-using-create-fs.md)\.

The mount target security group acts as a virtual firewall that controls the traffic\. For example, it determines which Amazon EC2 instances can access the file system\. This section explains the following:
+ Mount target security groups and how to enable traffic\.
+ How to mount the file system on your Amazon EC2 instance\.
+ NFS\-level permissions considerations\. 

  Initially, only the root user on the Amazon EC2 instance has read\-write\-execute permissions on the file system\. This topic discusses NFS\-level permissions and provides examples that show you how to grant permissions in common scenarios\. For more information, see [Working with Users, Groups, and Permissions at the Network File System \(NFS\) Level ](accessing-fs-nfs-permissions.md)\.

You can create mount targets for a file system using the console, using AWS Command Line Interface, or programmatically using the AWS SDKs\. When using the console, you can create mount targets when you first create a file system or after the file system is created\.

## Creating a Mount Target Using the Amazon EFS console<a name="create-mount-target-console"></a>

Perform the steps in the following procedure to create a mount target using the console\. As you follow the console steps, you can also create one or more mount targets\. You can create one mount target for each Availability Zone in your VPC\.

**To create an Amazon EFS file system \(console\)**

1. Sign in to the AWS Management Console and open the Amazon EFS console at [ https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create File System**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-efs-resources-100.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/)
**Note**  
The console shows the preceding page only if you don't already have any Amazon EFS file systems\. If you have created file systems, the console shows a list of your file systems\. On the list page, choose **Create File System**\.

1. On the **Step 1: Configure File System Access** page, select the VPC and the Availability Zone in the VPC where you want the console to create one or more mount targets for the file system that you are creating\. This VPC should be the same Amazon VPC in which you created your Amazon EC2 instance in the preceding section\.

   1. Select a Amazon VPC from the **VPC** list\. 
**Warning**  
If the Amazon VPC you want is not listed, verify the region in the global navigation in the Amazon EFS console\.

   1. In the **Create Mount Targets** section, select all of the Availability Zones listed\.

      We recommend that you create mount targets in all Availability Zones\. You can then mount your file system on Amazon EC2 instances created in any of the Amazon VPC subnets\. 
**Note**  
You can access a file system on an Amazon EC2 instance in one Availability Zone by using a mount target created in another Availability Zone, but there are costs associated with cross–Availability Zone access\.

      For each Availability Zone, do the following: 
      + Choose a **Subnet** from the list where you want to create the mount target\.

        You can create one mount target in each Availability Zone\. If you have multiple subnets in an Availability Zone where you launched your Amazon EC2 instance, you don't have to create mount target in the same subnet, it can be any subnet in the Availability Zone\. 
      + Leave **IP Address** select to **Automatic**\. Amazon EFS will select one of the available IP addresses for the mount target\.
      + Specify the **Security Group** you created specifically for the mount target, or the default security group for the default VPC\. Both security groups will have the necessary inbound rule that allows inbound access from the EC2 instance security group\.

        Click in the **Security Group** box and the console will show you the available security groups\. Here you can select a specific security group and remove the **Default** security group, or leave the default in place, depending on how you configured your Amazon EC2 instance\.

           
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-efs-resources-110.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/)

1. On the **Step 2: Configure optional settings** page, specify a value for the **Name** tag \(**MyExampleFileSystem**\) and choose your performance mode\.

   The console prepopulates the **Name** tag because Amazon EFS uses its value as the file system display name\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-efs-resources-120.png)

1. On the **Step 3: Review and Create** page, choose **Create File System**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-efs-resources-130.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/)

1. The console shows the newly created file system on the **File Systems** page\. Verify that all mount targets show the **Life Cycle State** as **Available**\. It might take a few moments before the mount targets become available \(you can expand/collapse the file system in the EFS console to force it to refresh\)\.

1. Under **File system access**, you'll see the file system's **DNS name**\. Make a note of this DNS name\. In the next section, you use the DNS name to mount the file system on the Amazon EC2 instance through the mount target\. The Amazon EC2 instance on which you mount the file system can resolve the file system's DNS name to the mount target's IP address\.

Now you are ready to mount the Amazon EFS file system on an Amazon EC2 instance\.

## Creating a Mount Target using the AWS CLI<a name="create-mount-target-cli"></a>

To create a mount target using AWS CLI, use the `create-mount-target` CLI command \(corresponding operation is [CreateMountTarget](API_CreateMountTarget.md)\), as shown following\.

```
$ aws efs create-mount-target \
--file-system-id file-system-id \
--subnet-id  subnet-id \
--security-group ID-of-the-security-group-created-for-mount-target \
--region aws-region \
--profile adminuser
```

After successfully creating the mount target, Amazon EFS returns the mount target description as JSON as shown in the following example\.

```
{
    "MountTargetId": "fsmt-f9a14450",
    "NetworkInterfaceId": "eni-3851ec4e",
    "FileSystemId": "fs-b6a0451f",
    "LifeCycleState": "available",
    "SubnetId": "subnet-b3983dc4",
    "OwnerId": "23124example",
    "IpAddress": "10.0.1.24"
}
```

You can also retrieve a list of mount targets created for a file system using the `describe-mount-targets` CLI command \(corresponding operation is [DescribeMountTargets](API_DescribeMountTargets.md)\), as shown following\.

```
$ aws efs describe-mount-targets \
--file-system-id file-system-id \
--region aws-region \
--profile adminuser
```

For an example, see [Walkthrough: Create an Amazon EFS File System and Mount It on an Amazon EC2 Instance Using the AWS CLI](wt1-getting-started.md)\.