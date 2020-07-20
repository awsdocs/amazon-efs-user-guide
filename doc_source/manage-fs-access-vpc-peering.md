# Mounting EFS file systems from another account or VPC<a name="manage-fs-access-vpc-peering"></a>

You can mount your Amazon EFS file system using IAM authorization for NFS clients and EFS Access Points using the EFS mount helper\. By default, the EFS mount helper uses domain name service \(DNS\) to resolve the IP address of your EFS mount target\. If you are mounting the file system from a different account or virtual private cloud \(VPC\), you need to resolve the EFS mount target manually\.

Following, you can find instructions for determining the correct EFS mount target IP address to use for your NFS client\. You can also find instructions for configuring the client to mount the EFS file system using that IP address\.

## Mounting using IAM or access points from another VPC<a name="mount-fs-different-vpc"></a>

When you use a VPC peering connection or transit gateway to connect VPCs, Amazon EC2 instances that are in one VPC can access EFS file systems in another VPC, even if the VPCs belong to different accounts\. 

### Prerequisites<a name="mount-fs-different-vpc-prerequisites"></a>

Before using the following the procedure, take these steps:
+ Install the `amazon-efs-utils` set of tools on the client\. You use the EFS mount helper to mount the file system, and the mount helper is part of `amazon-efs-utils`\. For instructions on installing `amazon-efs-utils`, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\.
+ Set up either a VPC peering connection or a VPC transit gateway\. 

  You connect the client's VPC and your EFS file system's VPC using either a VPC peering connection or a VPC transit gateway\. When you use a VPC peering connection or transit gateway to connect VPCs, Amazon EC2 instances that are in one VPC can access EFS file systems in another VPC, even if the VPCs belong to different accounts\.

  A *transit gateway *is a network transit hub that you can use to interconnect your VPCs and on\-premises networks\. For more information about using VPC transit gateways, see [Getting Started with Transit Gateways](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html) in the *Amazon VPC Transit Gateways Guide*\.

  A *VPC peering connection* is a networking connection between two VPCs\. This type of connection enables you to route traffic between them using private Internet Protocol version 4 \(IPv4\) or Internet Protocol version 6 \(IPv6\) addresses\. You can use VPC peering to connect VPCs within the same AWS Region or between AWS Regions\. For more information on VPC peering, see [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon VPC Peering Guide*\.

To ensure high availability of your file system, we recommend that you always use an EFS mount target IP address that is in the same Availability Zone \(AZ\) as your NFS client\. If you're mounting an EFS file system that is in another account, ensure that the NFS client and EFS mount target are in the same AZ ID\. This requirement applies because AZ names can differ between accounts\.

**To mount an EFS file system in another VPC using IAM or an access point**

1. Connect to your EC2 instance:
   + To connect to your instance from a computer running Mac OS or Linux, specify the \.pem file for your SSH command\. To do this, use the `-i` option and the path to your private key\.
   + To connect to your instance from a computer running Windows, you can use either MindTerm or PuTTY\. To use PuTTY, install it and convert the \.pem file to a \.ppk file\.

   For more information, see the following topics in the *Amazon EC2 User Guide for Linux Instances*:
   +  [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) 
   +  [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)

1. Determine the AZ ID that the EC2 instance is in using the `describe-availability-zones` CLI command as follows\.

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

   The AZ ID is returned in the `ZoneId` property, `use2-az2`\.

1. Retrieve the mount target IP address for your file system in the `use2-az2` AZ ID using the `describe-mount-targets` CLI command, as follows\.

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

   The mount target in the `use2-az2` AZ ID has an IP address of 10\.0\.2\.153\.

1. Add a line for the mount target IP address to the client's `/etc/hosts` file, in the format of `mount-target-IP-Address file-system-ID.efs.region.amazonaws.com`, as follows\.

   ```
   echo "10.0.2.153 fs-01234567.efs.us-east-2.amazonaws.com" | sudo tee -a /etc/hosts
   ```

1. Create a directory for mounting the file system using the following command\.

   ```
   $ sudo mkdir /mnt/efs
   ```

1. To mount the file system using IAM authorization, use the following command:

   ```
   $ sudo mount -t efs -o tls,iam file-system-id /mnt/efs/
   ```

   For more information about using IAM authorization with EFS, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\.

   To mount the file system using an EFS access point, use the following command:

   ```
   $ sudo mount -t efs -o tls,accesspoint=access-point-id file-system-id /mnt/efs/
   ```

   For more information about EFS access points, see [Working with Amazon EFS Access Points](efs-access-points.md)\.

You can't use DNS name resolution for EFS mount points in another VPC\. To mount your EFS file system, use the IP address of the mount points in the corresponding Availability Zone\. Alternatively, you can use Amazon Route 53 as your DNS service\. In Route 53, you can resolve the EFS mount target IP addresses from another VPC by creating a private hosted zone and resource record set\. For more information on how to do so, see [Working with Private Hosted Zones](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html) and [Working with Records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/rrsets-working-with.html) in the *Amazon Route 53 Developer Guide*\.

## Mounting from another account in the same VPC<a name="mount-fs-diff-account-same-vpc"></a>

Using shared VPCs, you can mount an Amazon EFS file system that is owned by one account from Amazon EC2 instances that are owned by a different account\. For more information about setting up a shared VPC, see [Working with Shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html) in the *Amazon VPC Peering Guide*\. 

After you set up VPC sharing, the EC2 instances can mount the EFS file system using Domain Name System \(DNS\) name resolution or the EFS mount helper\.