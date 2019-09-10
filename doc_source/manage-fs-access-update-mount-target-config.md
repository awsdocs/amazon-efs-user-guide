# Updating the Mount Target Configuration<a name="manage-fs-access-update-mount-target-config"></a>

After you create a mount target for your file system, you might want to update security groups that are in effect\. You can't change the IP address of an existing mount target\. To change an IP address, delete the mount target and create a new one with the new address\. Deleting a mount target breaks any existing file system mounts\. 

## Modifying a Security Group<a name="manage-fs-access-update-mount-target-config-sg"></a>

Security groups define inbound and outbound access\. When you change security groups associated with a mount target, make sure that you authorize necessary inbound and outbound access\. Doing so enables your EC2 instance to communicate with the file system\. 

For more information about security groups, see [Amazon EC2 Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\.

### Using the Console<a name="manage-fs-access-update-mount-target-config-sg-console"></a>

**To modify an EFS security group**

1. In the Amazon EFS console, choose the file system and for **Actions**, choose **Manage File System Access**\. 

   The console displays the **Manage File System Access** page with a list of Availability Zones and mount target information, if there is a mount target in the Availability Zone\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/manage-fs-05.png)

1. In the **Security Group** column, you can add or remove security groups\. Choose **X** to remove an existing security group\. Choose the **Security Group** box to choose from other available security groups\.

   If you remove all security groups, Amazon EFS assigns the VPC's default security group\. 

### Using the CLI<a name="manage-fs-access-update-mount-target-config-sg-cli"></a>

To modify security groups that are in effect for a mount target, use the `modify-mount-target-security-group` AWS CLI command \(corresponding operation is [ModifyMountTargetSecurityGroups](API_ModifyMountTargetSecurityGroups.md)\) to replace any existing security groups, as shown following\.

```
$ aws efs modify-mount-target-security-groups \
--mount-target-id mount-target-ID-whose-configuration-to-update \
--security-groups  security-group-ids-separated-by-space \
--region aws-region-where-mount-target-exists \
--profile adminuser
```