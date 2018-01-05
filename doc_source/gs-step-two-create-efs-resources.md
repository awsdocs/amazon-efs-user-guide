# Step 2: Create Your Amazon EFS File System<a name="gs-step-two-create-efs-resources"></a>

In this step, you create your Amazon EFS file system\.

**To create your Amazon EFS file system**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create File System**\.

1. Choose your default VPC from the **VPC** list\. It has the same **VPC ID** that you noted at the end of [Step 1: Create Your EC2 Resources and Launch Your EC2 Instance](gs-step-one-create-ec2-resources.md)\.

1. Select the check boxes for all of the Availability Zones\. Make sure that they all have the default subnets, automatic IP addresses, and the default security groups chosen\. These are your mount targets\. For more information, see [Creating Mount Targets](accessing-fs.md)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-configure-file-system-600w.png)

1. Choose **Next Step**\.

1. Name your file system, keep **general purpose** selected as your default performance mode, and choose **Next Step**\.

1. Choose **Create File System**\.

1. Choose your file system from the list and make a note of the **File system ID** value\. You'll need this value for the next step\.