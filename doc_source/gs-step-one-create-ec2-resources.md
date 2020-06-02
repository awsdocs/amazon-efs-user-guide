# Step 2: Create Your EC2 Resources and Launch Your EC2 Instance<a name="gs-step-one-create-ec2-resources"></a>

**Note**  
You can't use Amazon EFS with Microsoft Windows–based Amazon EC2 instances\.

Before you can launch and connect to an Amazon EC2 instance, you need to create a key pair, unless you already have one\. You can create a key pair using the Amazon EC2 console, and then you can launch your EC2 instance\.

**To create a key pair**
+ Follow the steps in [Setting Up with Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances* to create a key pair\. If you already have a key pair, you don't need to create a new one\. You can use your existing key pair for this exercise\.

**To launch the EC2 instance and mount an EFS file system**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. In **Step 1: Choose an Amazon Machine Image \(AMI\)**, find an Amazon Linux AMI at the top of the list and choose **Select**\.

1. In **Step 2: Choose an Instance Type**, choose **Next: Configure Instance Details**\.

1. In **Step 3: Configure Instance Details**, provide the following information: 
   + For **Network**, choose the entry for the same VPC that you noted when you created your EFS file system in [Step 1: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md)\.
   + For **Subnet**, choose a default subnet in any Availability Zone\.
   + For **File systems**, make sure that the EFS file system that you created in [Step 1: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md) is selected\. The path shown next to the file system ID is the mount point that the EC2 instance will use, which you can change\. Choose **Add to user data** to mount the file system when the EC2 is launched\.
   + Under **Advanced Details**, confirm that the user data is present in **User data**\.

1. Choose **Next: Add Storage**\.

1. Choose **Next: Add Tags**\.

1. Name your instance and choose **Next: Configure Security Group**\.

1. In **Step 6: Configure Security Group**, set **Assign a security group** to **Select an existing security group**\. Choose the default security group to make sure that it can access your EFS file system\.

   You can't access your EC2 instance by Secure Shell \(SSH\) using this security group\. SSH access isn't required for this exercise\. To add access by SSH later, you can edit the default security and add a rule to allow SSH\. Or you can create a new security group that allows SSH\. You can use the following settings to add SSH access:
   + **Type:** SSH
   + **Protocol:** TCP
   + **Port Range:** 22
   + **Source:** Anywhere 0\.0\.0\.0/0

1. Choose **Review and Launch**\.

1. Choose **Launch**\.

1. Select the check box for the key pair that you created, and then choose **Launch Instances**\.