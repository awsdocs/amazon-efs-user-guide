# Step 1: Create Your Amazon EFS File System<a name="gs-step-two-create-efs-resources"></a>

In this step, you create your Amazon EFS file system that has the service recommended settings using the Amazon EFS console\.

**To create your Amazon EFS file system**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create file system** to open the **Create file system** dialog box\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-create-file-system.png)

1. \(Optional\) Enter a **Name** for your file system\.

1. For **Virtual Private Cloud \(VPC\)**, choose your VPC, or keep it set to your default VPC\.

1. Choose **Create** to create a file system that uses the following service recommended settings:
   + Automatic backups turned on, for more information, see [Using AWS Backup with Amazon EFS](awsbackup.md)\.
   + Mount targets – Amazon EFS creates mount targets with the following settings:
     + A mount target in each Availability Zone in the Region where the file system is created\.
     + Located in the default subnets of the VPC you selected\.
     + Using the VPC's default security group – you can manage security groups after the file system is the created\.

     For more information, see [Managing file system network accessibility](manage-fs-access.md)\.
   + General Purpose performance mode – For more information, see [Performance Modes](performance.md#performancemodes)\.
   + Bursting throughput mode – For more information, see [Throughput Modes](performance.md#throughput-modes)\.
   + Encryption of data at rest enabled using your default key for Amazon EFS \(aws/elasticfilesystem\) – For more information, see [Encrypting Data at Rest](encryption-at-rest.md)\.
   + Lifecycle management enabled with a 30\-day policy – For more information, see [EFS lifecycle management](lifecycle-management-efs.md)\.

   After you create the file system, you can customize the file system's settings, with the exception of encryption and performance mode\.

   If you want to create a file system with a customized configuration, choose **Customize**\. For more information about creating a file system with customized settings, see [Creating a file system using the Amazon EFS console](creating-using-create-fs.md#creating-using-fs-part1-console), 

1. The **File systems** page appears with a banner across the top showing the status of the file system you created\. A link to access the file system details page appears in the banner when the file system becomes available\.  
![\[File system details page showing the newly created file system and its configuration details.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-filesystemdetails.png)