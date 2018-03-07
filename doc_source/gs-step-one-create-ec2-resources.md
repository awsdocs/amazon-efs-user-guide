# Step 1: Create Your EC2 Resources and Launch Your EC2 Instance<a name="gs-step-one-create-ec2-resources"></a>

Before you can launch and connect to an Amazon EC2 instance, you need to create a key pair, unless you already have one\. You can create a key pair using the Amazon EC2 console and then you can launch your EC2 instance\.

**Note**  
Using Amazon EFS with Microsoft Windows Amazon EC2 instances is not supported\.

**To create a key pair**

+ Follow the steps in [Setting Up with Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances* to create a key pair\. If you already have a key pair, you do not need to create a new one and you can use your existing key pair for this exercise\.

**To launch the EC2 instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. In **Step 1: Choose an Amazon Machine Image \(AMI\)**, find the Amazon Linux AMI at the top of the list and choose **Select**\.
**Note**  
If you choose either the **Amazon Linux AMI 2016\.03\.0** or **Amazon Linux AMI 2016\.09\.0** AMI when launching your Amazon EC2 instance, you don't need to install `nfs-utils` because it's already included in the AMI by default\.

1. In **Step 2: Choose an Instance Type**, choose **Next: Configure Instance Details**\.

1. In **Step 3: Configure Instance Details**, choose **Network**, and then choose the entry for your default VPC\. It should look something like `vpc-xxxxxxx (172.31.0.0/16) (default)`\. 

   1. Choose **Subnet**, and then choose a subnet in any Availability Zone\.

   1. Choose **Next: Add Storage**\.

1. Choose **Next: Tag Instance**\.

1. Name your instance and choose **Next: Configure Security Group**\.

1. In **Step 6: Configure Security Group**, review the contents of this page, ensure that **Assign a security group** is set to **Create a new security group**, and verify that the inbound rule being created has the following default values\.

   + **Type:** SSH

   + **Protocol:** TCP

   + **Port Range:** 22

   + **Source:** Anywhere 0\.0\.0\.0/0  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-review-security-group-600w.png)
**Note**  
You can configure the EFS file system to mount on your EC2 instance automatically\. For more information, see [Configuring an EFS File System to Mount Automatically at EC2 Instance Launch](mount-fs-auto-mount-onreboot.md#mount-fs-auto-mount-on-creation)\.

1. Choose **Review and Launch**\.

1. Choose **Launch**\.

1. Select the check box for the key pair that you created, and then choose **Launch Instances**\.

1. Choose **View Instances**\.

1. Choose the name of the instance you just created from the list, and then choose **Actions**\.

   1. From the menu that opens, choose **Networking** and then choose **Change Security Groups**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-change-security-group-600w.png)

   1. Select the check box next to the security group with the description **default VPC security group**\.

   1. Choose **Assign Security Groups**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/gs-assign-security-group-600w.png)
**Note**  
In this step, you assign your VPC's default security group to the Amazon EC2 instance\. Doing this ensures that the instance is a member of the security group that the Amazon EFS file system mount target authorizes for connection in [Step 2: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md)\.  
By using your VPC's default security group, with its default inbound and outbound rules, you are potentially opening up this instance and this file system to potential threats from within your VPC\. Make sure that you follow [Step 5: Clean Up Resources and Protect Your AWS Account](gs-step-four-cleanup.md) at the end of this Getting Started exercise to remove resources exposed to your VPC's default security group for this example\. For more information, see [Security Groups for Amazon EC2 Instances and Mount Targets](security-considerations.md#network-access)\.

1. Choose your instance from the list\.

1. On the **Description** tab, make sure that you have two entries listed next to **security groups**—one for the default VPC security group and one for the security group that you created when you launched the instance\.

1. Make a note of the values listed next to **VPC ID** and **Public DNS**\. You need those values later in this exercise\.