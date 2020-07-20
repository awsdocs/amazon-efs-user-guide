# Walkthrough: Mount a File System from a Different VPC<a name="efs-different-vpc"></a>

In this walkthrough, you set up an Amazon EC2 instance to mount an Amazon EFS file system that is in a different virtual private cloud \(VPC\)\. You do this using the EFS mount helper\. The mount helper is part of the `amazon-efs-utils` set of tools\. For more information about `amazon-efs-utils`, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\.

The client's VPC and your EFS file system's VPC must be connected using either a VPC peering connection or a VPC transit gateway\. When you use a VPC peering connection or transit gateway to connect VPCs, Amazon EC2 instances that are in one VPC can access EFS file systems in another VPC, even if the VPCs belong to different accounts\.

**Note**  
Using Amazon EFS with Microsoft Windows–based clients isn't supported\.

**Topics**
+ [Before You Begin](#wt6-prepare)
+ [Step 1: Determine the Availability Zone ID of the EFS Mount Target](#wt6-efs-utils-step1)
+ [Step 2: Determine the Mount Target IP Address](#wt6-efs-utils-step2)
+ [Step 3: Add a Host Entry for the Mount Target](#wt6-efs-utils-step3)
+ [Step 4: Mount Your File System Using the EFS Mount Helper](#wt6-efs-utils-step4)
+ [Step 5: Clean Up Resources and Protect Your AWS Account](#wt6-step5-cleanup)

## Before You Begin<a name="wt6-prepare"></a>

In this walkthrough, we assume that you already have the following:
+ The `amazon-efs-utils` set of tools is installed on the EC2 instance before using this procedure\. For instructions on installing `amazon-efs-utils`, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\.
+ One of the following:
  + A VPC peering connection between the VPC where the EFS file system resides and the VPC where the EC2 instance resides\. A *VPC peering connection* is a networking connection between two VPCs\. This type of connection enables you to route traffic between them using private Internet Protocol version 4 \(IPv4\) or Internet Protocol version 6 \(IPv6\) addresses\. You can use VPC peering to connect VPCs within the same AWS Region or between AWS Regions\. For more information, see [Creating and Accepting a VPC Peering Connection](https://docs.aws.amazon.com/vpc/latest/peering/create-vpc-peering-connection.html) in the *Amazon VPC Peering Guide*\.
  + A transit gateway connecting the VPC where the EFS file system resides and the VPC where the EC2 instance resides\. A *transit gateway *is a network transit hub that you can use to interconnect your VPCs and on\-premises networks\. For more information, see [Getting Started with Transit Gateways](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html) in the *Amazon VPC Transit Gateways Guide*\.

## Step 1: Determine the Availability Zone ID of the EFS Mount Target<a name="wt6-efs-utils-step1"></a>

To ensure high availability of your file system, we recommend that you always use an EFS mount target IP address that is in the same Availability Zone as your NFS client\. If you are mounting an EFS file system that is in another account, ensure that the NFS client and EFS mount target are in the same Availability Zone ID\. This requirement applies because Availability Zone names can differ between accounts\.

**To determine the Availability Zone ID of the EC2 instance**

1. Connect to your EC2 instance:
   + To connect to your instance from a computer running macOS or Linux, specify the \.pem file for your SSH command\. To do this, use the `-i` option and the path to your private key\.
   + To connect to your instance from a computer running Windows, you can use either MindTerm or PuTTY\. To use PuTTY, install it and convert the \.pem file to a \.ppk file\.

   For more information, see the following topics in the *Amazon EC2 User Guide for Linux Instances*:
   +  [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)
   +  [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) 

1. Determine the Availability Zone ID that the EC2 instance is in using the `describe-availability-zones` CLI command as follows\.

   ```
   [ec2-user@ip-10.0.0.1] $ aws ec2 describe-availability-zones --zone-name `curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone`
   {
       "AvailabilityZones": [
           {
               "State": "available", 
               "ZoneName": "us-east-2b", 
               "Messages": [], 
               "ZoneId": "use2-az2", 
               "RegionName": "us-east-2"
           }
       ]
   }
   ```

   The Availability Zone ID is returned in the `ZoneId` property, `use2-az2`\.

## Step 2: Determine the Mount Target IP Address<a name="wt6-efs-utils-step2"></a>

Now that you know the Availability Zone ID of the EC2 instance, you can now retrieve the IP address of the mount target that is in the same Availability Zone ID\.

**To determine the mount target IP address in the same Availability Zone ID**
+ Retrieve the mount target IP address for your file system in the `use2-az2` AZ ID using the `describe-mount-targets` CLI command, as follows\.

  ```
  $ aws efs describe-mount-targets --file-system-id file_system_id
  {
      "MountTargets": [
          {
              "OwnerId": "111122223333",
              "MountTargetId": "fsmt-11223344", 
    =====>    "AvailabilityZoneId": "use2-az2",
              "NetworkInterfaceId": "eni-048c09a306023eeec", 
              "AvailabilityZoneName": "us-east-2b", 
              "FileSystemId": "fs-01234567", 
              "LifeCycleState": "available", 
              "SubnetId": "subnet-06eb0da37ee82a64f", 
              "OwnerId": "958322738406", 
    =====>    "IpAddress": "10.0.2.153"
          }, 
  ...
          {
              "OwnerId": "111122223333",
              "MountTargetId": "fsmt-667788aa", 
              "AvailabilityZoneId": "use2-az3", 
              "NetworkInterfaceId": "eni-0edb579d21ed39261", 
              "AvailabilityZoneName": "us-east-2c", 
              "FileSystemId": "fs-01234567", 
              "LifeCycleState": "available", 
              "SubnetId": "subnet-0ee85556822c441af", 
              "OwnerId": "958322738406", 
              "IpAddress": "10.0.3.107"
          }
      ]
  }
  ```

  The mount target in the `use2-az2` Availability Zone ID has an IP address of 10\.0\.2\.153\.

## Step 3: Add a Host Entry for the Mount Target<a name="wt6-efs-utils-step3"></a>

You can now make an entry in the `/etc/hosts` file on the EC2 instance that maps the mount target IP address to your EFS file system's hostname\. 

**To add a host entry for the mount target**
+ Add a line for the mount target IP address to the EC2 instance's `/etc/hosts` file\. The entry uses the format `mount-target-IP-Address file-system-ID.efs.region.amazonaws.com`\. Use the following command to add the line to the file\.

  ```
  echo "10.0.2.153 fs-01234567.efs.us-east-2.amazonaws.com" | sudo tee -a /etc/hosts
  ```

## Step 4: Mount Your File System Using the EFS Mount Helper<a name="wt6-efs-utils-step4"></a>

To mount your EFS file system, you first create a mount directory on the EC2 instance\. Then, using the EFS mount helper, you can mount the file system with either IAM authorization or an EFS access point\. For more information, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md) and [Working with Amazon EFS Access Points](efs-access-points.md)\.

**To create a mount directory**
+ Create a directory for mounting the file system using the following command\.

  ```
  $ sudo mkdir /mnt/efs/
  ```

**To mount the file system using IAM authorization**
+ Use the following command to mount the file system using IAM authorization\.

  ```
  $ sudo mount -t efs -o tls,iam file-system-id /mnt/efs/
  ```

**To mount the file system using an EFS access point**
+ Use the following command to mount the file system using an EFS access point\.

  ```
  $ sudo mount -t efs -o tls,accesspoint=access-point-id file-system-id /mnt/efs/
  ```

Now that you've mounted your Amazon EFS file system, you can test it with the following procedure\.

**To test the Amazon EFS file system connection**

1. Change directories to the new directory that you created with the following command\.

   ```
   $ cd ~/mnt/efs
   ```

1. Make a subdirectory and change the ownership of that subdirectory to your EC2 instance user\. Then navigate to that new directory with the following commands\.

   ```
   $ sudo mkdir getting-started
   $ sudo chown ec2-user getting-started
   $ cd getting-started
   ```

1. Create a text file with the following command\.

   ```
   $ touch test-file.txt
   ```

1. List the directory contents with the following command\.

   ```
   $ ls -al
   ```

As a result, the following file is created\.

```
-rw-rw-r-- 1 username username 0 Nov 15 15:32 test-file.txt
```

You can also mount your file system automatically by adding an entry to the `/etc/fstab` file\. For more information, see [Using /etc/fstab to mount automatically](mount-fs-auto-mount-onreboot.md#mount-fs-auto-mount-update-fstab)\.

**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting-efs-mounting.md#automount-fails)\.

## Step 5: Clean Up Resources and Protect Your AWS Account<a name="wt6-step5-cleanup"></a>

After you have finished this walkthrough, or if you don't want to explore the walkthroughs, make sure to take the following steps\. These clean up your resources and protect your AWS account\.

**To clean up resources and protect your AWS account**

1. Unmount the Amazon EFS file system with the following command\.

   ```
   $ sudo umount ~/efs
   ```

1. Open the Amazon EFS console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose the Amazon EFS file system that you want to delete from the list of file systems\.

1. For **Actions**, choose **Delete file system**\.

1. In the **Permanently delete file system** dialog box, type the file system ID for the Amazon EFS file system that you want to delete, and then choose **Delete File System**\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the name of the security group that you added the rule to for this walkthrough\.
**Warning**  
Don't delete the default security group for your VPC\.

1. For **Actions**, choose **Edit inbound rules**\.

1. Choose the X at the end of the inbound rule you added, and choose **Save**\.