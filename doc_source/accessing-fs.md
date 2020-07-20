# Creating mount targets<a name="accessing-fs"></a>

After you create a file system, you can create mount targets\. Then you can mount the file system on EC2 instances, containers, and Lambda functions in your virtual private cloud \(VPC\), as shown in the following diagram\. 

![\[Diagram showing 3 Availability Zones in a VPC, containing EC2 instances and mount targets, and a mounted file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/overview-flow.png)

For more information about creating a file system, see [Creating Amazon EFS file systems](creating-using-create-fs.md)\.

The mount target security group acts as a virtual firewall that controls the traffic\. For example, it determines which clients can access the file system\. This section explains the following:
+ Managing mount target security groups and enabling traffic\.
+ Mounting the file system on your clients\.
+ NFS\-level permissions considerations\. 

  Initially, only the root user on the Amazon EC2 instance has read\-write\-execute permissions on the file system\. This topic discusses NFS\-level permissions and provides examples that show you how to grant permissions in common scenarios\. For more information, see [Working with Users, Groups, and Permissions at the Network File System \(NFS\) Level](accessing-fs-nfs-permissions.md)\.

You can create mount targets for a file system using the AWS Management Console, AWS CLI, or programmatically using the AWS SDKs\. When using the console, you can create mount targets when you first create a file system or after the file system is created\.

For instructions to create mount targets using the Amazon EFS console when creating a new file system, see [Step 2: Configure network access](creating-using-create-fs.md#configure-efs-network-access)\.

## Managing mount targets using the Amazon EFS console<a name="console2-create-mount-target-existing-fs"></a>

Use the following procedure to add or modify mount targets for an existing Amazon EFS file system\.

**Note**  
The new Amazon EFS management console is not available in the following AWS Regions: Europe \(Milan\) Region, Africa \(Cape Town\) Region, Beijing and Ningxia Regions, or AWS GovCloud \(US\)\. If you are accessing the Amazon EFS console in these regions, you can access the user documentation for that console experience here: [ AWS Elastic File System User Guide](images/AmazonElasticFileSystem-UserGuide-console1.pdf)\.

**To manage mount targets on an Amazon EFS file system \(console\)**

1. Sign in to the AWS Management Console and open the Amazon EFS console at [ https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. In the left navigation pane, choose **File systems**\. The **File systems** page displays the EFS file systems in your account\.

1. Choose the file system that you want to manage mount targets for by choosing its **Name** or the **File system ID** to display the file system details page\.

1. Choose **Network** to display the list of existing mount targets\.  
![\[Amazon EFS console file system details, network tab.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-networktab1-efs.png)

1. Choose **Manage** to display the **Availability zone** page and make modifications\.  
![\[Amazon EFS console file system details, network tab.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-avalability-zone-efs.png)

   On this page, for existing mount targets, you can add and remove security groups, or delete the mount target\. You can also create new mount targets\.
   + To remove a security group from a mount target, choose **X** next to the security group ID\.
   + To add a security group to a mount target, choose **Select security groups** to display a list of available security groups\. Or, enter a security group ID in the search field at the top of the list\.
   + To queue a mount target for deletion, choose **Remove**\.
**Note**  
Before deleting a mount target, first unmount the file system\.
   + To add a mount target, choose **Add mount target**\. This is available only if mount targets do not already exist in each Availability Zone for the Region\.

1. Choose **Save** to save any changes\.<a name="change-vpc-console2"></a>

**To change the VPC for an Amazon EFS file system \(console\)**

To change the VPC for a file system's network configuration, you must delete all of the file system's existing mount targets\.

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. In the left navigation pane, choose **File systems**\. The **File systems** page shows the EFS file systems in your account\.

1. For the file system that you want to change the VPC for, choose the **Name** or the **File system ID**\. The file system details page is displayed\.

1. Choose **Network** to display the list of existing mount targets\.

1. Choose **Manage**\. The **Availability zone** page appears\.

1. Remove all mount targets displayed on the page\.

1. Choose **Save** to save changes and delete the mount targets\. The **Network** tab shows the mount targets status as **deleting**\.

1. When all the mount targets statuses show as **deleted**, choose **Manage**\. The **Availability zone** page appears\.

1. Choose the new VPC from the **Virtual Private Cloud \(VPC\)** list\.

1. Choose **Add mount target** to add a new mount target\. For each mount target you add, enter the following:
   + An **Availability zone**
   + A **Subnet ID**
   + An **IP address**, or keep it set to **Automatic**
   + One or more **Security groups**

1. Choose **Save** to implement the VPC and mount target changes\.

## Managing mount targets using the AWS CLI<a name="create-mount-target-cli"></a>

**To create a mount target \(CLI\)**
+ To create a mount target, use the `create-mount-target` CLI command \(corresponding operation is [CreateMountTarget](API_CreateMountTarget.md)\), as shown following\.

  ```
  $ aws efs create-mount-target \
  --file-system-id file-system-id \
  --subnet-id  subnet-id \
  --security-group ID-of-the-security-group-created-for-mount-target \
  --region aws-region \
  --profile adminuser
  ```

  The following example shows the command with sample data\.

  ```
  $ aws efs create-mount-target \
  --file-system-id fs-0123467 \
  --subnet-id  subnet-b3983dc4 \
  --security-group sg-01234567 \
  --region us-east-2 \
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

**To retrieve a list of mount targets for a file system \(CLI\)**
+ You can also retrieve a list of mount targets created for a file system using the [ `describe-mount-targets`](https://docs.aws.amazon.com/cli/latest/reference/efs/describe-mount-targets.html) CLI command \(the corresponding operation is [DescribeMountTargets](API_DescribeMountTargets.md)\), as shown following\.

  ```
  $ aws efs describe-mount-targets --file-system-id fs-a576a6dc
  ```

  ```
  {
      "MountTargets": [
          {
              "OwnerId": "111122223333",
              "MountTargetId": "fsmt-48518531",
              "FileSystemId": "fs-a576a6dc",
              "SubnetId": "subnet-88556633",
              "LifeCycleState": "available",
              "IpAddress": "172.31.25.203",
              "NetworkInterfaceId": "eni-0123456789abcdef1",
              "AvailabilityZoneId": "use2-az2",
              "AvailabilityZoneName": "us-east-2b"
          },
          {
              "OwnerId": "111122223333",
              "MountTargetId": "fsmt-5651852f",
              "FileSystemId": "fs-a576a6dc",
              "SubnetId": "subnet-44223377",
              "LifeCycleState": "available",
              "IpAddress": "172.31.46.181",
              "NetworkInterfaceId": "eni-0123456789abcdefa",
              "AvailabilityZoneId": "use2-az3",
              "AvailabilityZoneName": "us-east-2c"
          },
          {
              "OwnerId": "111122223333",
              "MountTargetId": "fsmt-5751852e",
              "FileSystemId": "fs-a576a6dc",
              "SubnetId": "subnet-a3520bcb",
              "LifeCycleState": "available",
              "IpAddress": "172.31.12.219",
              "NetworkInterfaceId": "eni-0123456789abcdef0",
              "AvailabilityZoneId": "use2-az1",
              "AvailabilityZoneName": "us-east-2a"
          }
      ]
  }
  ```

**To delete an existing mount target \(CLI\)**
+ To delete an existing mount target, use the `delete-mount-target` AWS CLI command \(corresponding operation is [DeleteMountTarget](API_DeleteMountTarget.md)\), as shown following\.
**Note**  
Before deleting a mount target, first unmount the file system\.

  ```
  $ aws efs delete-mount-target \
  --mount-target-id mount-target-ID-to-delete \
  --region aws-region-where-mount-target-exists
  ```

  The following is an example with sample data\.

  ```
  $ aws efs delete-mount-target \
  --mount-target-id fsmt-5751852e \
  --region us-east-2 \
  ```<a name="modify-mount-target-sg-cli"></a>

**To modify the security group of an existing mount target**
+ To modify security groups that are in effect for a mount target, use the `modify-mount-target-security-group` AWS CLI command \(the corresponding operation is [ModifyMountTargetSecurityGroups](API_ModifyMountTargetSecurityGroups.md)\) to replace any existing security groups, as shown following\.

  ```
  $ aws efs modify-mount-target-security-groups \
  --mount-target-id mount-target-ID-whose-configuration-to-update \
  --security-groups  security-group-ids-separated-by-space \
  --region aws-region-where-mount-target-exists \
  --profile adminuser
  ```

  The following is an example with sample data\.

  ```
  $ aws efs modify-mount-target-security-groups \
  --mount-target-id fsmt-5751852e \
  --security-groups  sg-1004395a sg-1114433a \
  --region us-east-2
  ```

For more information, see [Walkthrough: Create an Amazon EFS File System and Mount It on an Amazon EC2 Instance Using the AWS CLI](wt1-getting-started.md)\.