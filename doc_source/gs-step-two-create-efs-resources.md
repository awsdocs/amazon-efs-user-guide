# Step 1: Create your Amazon EFS file system<a name="gs-step-two-create-efs-resources"></a>

In this step, you create your Amazon EFS file system that has the service recommended settings using the Amazon EFS console\.

**To create your Amazon EFS file system**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create file system** to open the **Create file system** dialog box\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-create-file-system.png)

1. \(Optional\) Enter a **Name** for your file system\.

1. For **Virtual Private Cloud \(VPC\)**, choose your VPC, or keep it set to your default VPC\.

1. For **Availability and Durability**, choose one of the following:
   + **Regional** to create a file system that uses [Standard storage classes](storage-classes.md#regional-sc)\. Standard storage classes store file system data and metadata redundantly across all Availability Zones within an AWS Region\. **Regional** offers the highest levels of availability and durability\.
   + **One Zone** to create a file system that uses [One Zone storage classes](storage-classes.md#one-zone-sc)\. One Zone storage classes store file sytem data and metadata redundantly within a single Availability Zone which makes it less expensive than Standard storage classes\.

     Because EFS One Zone storage classes store data in a single AWS Availability Zone, data stored in these storage classes may be lost in the event of a disaster or other fault that affects all copies of the data within the Availability Zone, or in the event of Availability Zone destruction resulting from disasters, such as earthquakes and floods\.

   If you choose One Zone, choose the **Availability Zone** that you want the file system created in, or leave the default setting\.
**Note**  
One Zone storage classes are not available in all Availability Zones in AWS Regions where Amazon EFS is available\.

   For more information, see [EFS storage classes](storage-classes.md)\.

1. Choose **Create** to create a file system that uses the following service recommended settings:
   + Automatic backups turned on, for more information, see [Using AWS Backup to back up and restore Amazon EFS file systems](awsbackup.md)\.
   + Mount targets – Amazon EFS creates mount targets with the following settings:
     + For file systems that use Standard storage classes, a mount target is created in each Availability Zone in the AWS Region in which the file system is created\. For file systems that use One Zone storage classes, a single mount target is created in the Availability Zone you specified\.
     + Located in the default subnets of the VPC you selected\.
     + Using the VPC's default security group – You can manage security groups after the file system is the created\.

     For more information, see [Managing file system network accessibility](manage-fs-access.md)\.
   + General Purpose performance mode – For more information, see [Performance modes](performance.md#performancemodes)\.
   + Bursting throughput mode – For more information, see [Throughput modes](performance.md#throughput-modes)\.
   + Encryption of data at rest enabled using your default key for Amazon EFS \(`aws/elasticfilesystem`\) – For more information, see [Encrypting data at rest](encryption-at-rest.md)\.
   + Lifecycle Management – Amazon EFS creates the file system with the following lifecycle policies:
     + **Transition into IA** set to **30 days since last access**
     + **Transition out of IA** set to **On first access**

     For more information, see [Amazon EFS lifecycle management](lifecycle-management-efs.md)\.

   After you create the file system, you can customize the file system's settings with the exception of availability and durability, encryption, and performance mode\.

   If you want to create a file system with a customized configuration, choose **Customize**\. For more information about creating a file system with customized settings, see [Creating a file system with custom settings using the Amazon EFS console](creating-using-create-fs.md#creating-using-fs-part1-console)\. 

1. The **File systems** page appears with a banner across the top showing the status of the file system you created\. A link to access the file system details page appears in the banner when the file system becomes available\.  
![\[File system details page showing the newly created file system and its configuration details.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-efs-filesystem-INT-details.png)

   For more information about file system status, see [File system status](file-system-state.md)\.