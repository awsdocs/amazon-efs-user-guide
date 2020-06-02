# Step 1: Create Your Amazon EFS File System<a name="gs-step-two-create-efs-resources"></a>

In this step, you create your Amazon EFS file system\.

**To create your Amazon EFS file system**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create File System**\. The **Create file system** page is displayed\.

1. In the **Configure network access** section, for **VPC**, choose your default VPC\. It looks something like `vpc-xxxxxxx (172.31.0.0/16) (default)`\. Note the VPC ID; you need it in a later step\.

1. Select the check boxes for all of the Availability Zones\. Make sure that they all have the default subnets, automatic IP addresses, and the default security groups chosen\. These are your mount targets\. For more information, see [Creating Mount Targets](accessing-fs.md)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-configure-file-system-800w1.png)

1. Choose **Next Step**\.

1. In **Add tags**, name your file system, and add any other tags to help describe and manage your ﬁle system\.

1. \(Optional\) In **Enable Lifecycle Management**, select a **Lifecycle policy** so that your file system uses the lower\-cost Infrequent Access storage class\. For more information about storage classes, see [EFS Storage Classes](storage-classes.md)\. 

1. Keep **Bursting** and **General Purpose** selected as your default performance and throughput modes\.

1. \(Optional\) For **Enable encryption**, leave **Enable encryption of data at rest** unchecked for this exercise\. For more information on encrypting the data on your file system, see [Data Encryption in EFS](encryption.md)\. Choose **Next Step**\. The **Configure client access** page is displayed\.

1.  For this exercise, leave the default settings for **File system policy** and **Access Points**\. For more information about EFS file system policies and how to use them, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\. For more information about EFS access points, see [Working with Amazon EFS Access Points](efs-access-points.md)\. Choose **Next Step**\. The **Review and create** page is displayed\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-review-create.png)

1. Review the file system configuration, and then choose **Create File System**\.

1. The **File systems** page is displayed, with your new file system selected\. Note the **File system ID** value\. You need this value for the next step\.