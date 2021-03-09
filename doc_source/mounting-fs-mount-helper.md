# Mounting with the EFS mount helper<a name="mounting-fs-mount-helper"></a>

You can mount an Amazon EFS file system on a number of clients using the Amazon EFS mount helper\. In the following sections, you can find the mount helper process for the different types of clients\.

**Topics**
+ [Mounting on Amazon EC2 with the EFS mount helper](#mounting-fs-mount-helper-ec2)
+ [Mounting with IAM authorization](#mounting-IAM-option)
+ [Mounting with EFS access points](#mounting-access-points)
+ [Mounting on your on\-premises Linux client with the EFS mount helper over AWS Direct Connect and VPN](#mounting-fs-mount-helper-direct)

## Mounting on Amazon EC2 with the EFS mount helper<a name="mounting-fs-mount-helper-ec2"></a>

You can mount an Amazon EFS file system on an Amazon EC2 instance using the Amazon EFS mount helper\. To use the mount helper, you need the following:
+ **The file system ID of the EFS file system that you want to mount ** – After you create an Amazon EFS file system, you can get that file system's ID from the console or programmatically through the Amazon EFS API\. The ID is in this format: `fs-12345678`\. The EFS mount helper resolves the file system ID to the local IP address of the mount target elastic network interface \(ENI\) without calling external resources\.
+ **An Amazon EFS mount target** – You create mount targets in your virtual private cloud \(VPC\)\. If you create your file system in the console, you create your mount targets at the same time\. For instructions to create mount targets using the EFS console, see [Step 2: Configure network access](creating-using-create-fs.md#configure-efs-network-access)\.

  If you use a mount target in an Availability Zone different from that of your Amazon EC2 instance, you incur standard EC2 charges for data sent across Availability Zones\. You also might see increased latencies for file system operations\.
**Note**  
We recommend that you wait 90 seconds after creating a mount target before you mount your file system\. This wait lets the DNS records propagate fully in the AWS Region where the file system is\.
+ **An Amazon EC2 instance running one of the supported Linux or macOS distributions** – The supported distributions for mounting your file system with the mount helper are the following:
  + Amazon Linux 2 
  + Amazon Linux 2017\.09 and newer 
  + macOS Big Sur
  + Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer 
  + Ubuntu 16\.04 LTS and newer
**Note**  
EC2 Mac instances running macOS Big Sur support NFS 4\.0 only\.
+ **The Amazon EFS mount helper installed on the EC2 instance** – The mount helper is a tool in the `amazon-efs-utils`\. For information about installing `amazon-efs-utils`, see [Manually installing the Amazon EFS client](installing-amazon-efs-utils.md)\.

### Mount options used by Amazon EFS mount helper<a name="mount-helper-setting"></a>

The EFS mount helper uses the following mount options that are optimized for Amazon EFS:
+ `nfsvers=4.1` – used when mounting on EC2 Linux instances

  `nfsvers=4.0` – used when mounting on an EC2 Mac instance running MacOS Big Sur
+ `rsize=1048576`
+ `wsize=1048576`
+ `hard`
+ `timeo=600`
+ `retrans=2`
+ `noresvport`
+ `mountport=2049` – only used when mounting on EC2 Mac instances running macOS Big Sur

To use the `mount` command, the following must be true:
+ The connecting EC2 instance must be in a virtual private cloud \(VPC\) based on the Amazon VPC service\. It also must be configured to use the DNS server provided by Amazon\. For information about the Amazon DNS server, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\. 
+ The VPC of the connecting EC2 instance must have DNS hostnames enabled\. For more information, see [Viewing DNS Hostnames for Your EC2 Instance](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-viewing) in the *Amazon VPC User Guide*\. 

**To mount your Amazon EFS file system with the mount helper**

1. Open a terminal window on your EC2 instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information, see [Connect to your Linux instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) for Linux instances, or [Connect to your instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-mac-instances.html#mac-instance-ssh) for Mac instances, in the *Amazon EC2 User Guide for Linux Instances*\.

1. Run the following command to mount your file system\.

   ```
   sudo mount -t efs fs-12345678:/ efs
   ```

   Alternatively, if you want to use encryption of data in transit, you can mount your file system with the following command\.

   ```
   sudo mount -t efs -o tls fs-12345678:/ efs
   ```

   You can view and copy the exact commands to mount your file system in the **Attach** dialog box\.

   1. In the Amazon EFS console, choose the file system that you want to mount to display its details page\.

   1. To display the mount commands to use for this file system, choose **Attach** in the upper right\.  
![\[Amazon EFS Attach file system screen showing the exact commands to use when mounting the file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fs-attach.png )

      The **Attach** screen displays the exact commands to use for mounting the file system in the following ways:
      + \(**Mount via DNS**\) Using the file system's DNS name with the EFS mount helper or an NFS client\.
      + \(**Mount via IP**\) Using the mount target IP address in the selected Availability Zone with an NFS client\.

### Mounting Amazon EFS file systems from a different AWS Region<a name="mount-different-region"></a>

If you are mounting your EFS file system from an Amazon EC2 instance that is in a different AWS Region than the file system, you will need to edit the `efs-utils.conf` file\. In `efs-utils.conf`, locate the following lines:

```
#region = us-east-1
```

Uncomment the line, and replace the Region value with the Region in which the file system is located, if it is not in `us-east-1`\.

## Mounting with IAM authorization<a name="mounting-IAM-option"></a>

To mount your Amazon EFS file system on Linux instances using AWS Identity and Access Management \(IAM\) authorization, you use the EFS mount helper\. For more information about IAM authorization for NFS clients, see [Using IAM to control file system data access](iam-access-control-nfs-efs.md)\.

### Mounting with IAM using an EC2 instance profile<a name="mount-iam-ec2-profile"></a>

If you are mounting with IAM authorization to an Amazon EC2 instance with an instance profile, use the `tls` and `iam` mount options, shown following\.

```
$ sudo mount -t efs -o tls,iam file-system-id efs-mount-point
```

To automatically mount with IAM authorization to an Amazon EC2 instance that has an instance profile, add the following line to the `/etc/fstab` file on the EC2 instance\.

```
file-system-id:/ efs-mount-point efs _netdev,tls,iam 0 0
```

### Mounting with IAM using a named profile<a name="mount-iam-creds-file"></a>

You can mount with IAM authorization using the IAM credentials located in the AWS CLI credentials file `~/.aws/credentials`, or the AWS CLI config file `~/.aws/config`\. If `"awsprofile"` is not specified, the "default" profile is used\.

To mount with IAM authorization to a Linux instance using a credentials file, use the `tls`, `awsprofile`, and `iam` mount options, shown following\.

```
$ sudo mount -t efs -o tls,iam,awsprofile=namedprofile file-system-id efs-mount-point/
```

To automatically mount with IAM authorization to a Linux instance using a credentials file, add the following line to the `/etc/fstab` file on the EC2 instance\.

```
file-system-id:/ efs-mount-point efs _netdev,tls,iam,awsprofile=namedprofile 0 0
```

## Mounting with EFS access points<a name="mounting-access-points"></a>

You can mount an EFS file system using an EFS access point\. To do this, use the EFS mount helper\. 

When you mount a file system using an access point, the mount command includes the `access-point-id` and the `tls` mount option in addition to the regular mount options\. An example is shown following\. 

```
$ sudo mount -t efs -o tls,accesspoint=access-point-id file-system-id efs-mount-point
```

To automatically mount a file system using an access point, add the following line to the `/etc/fstab` file on the EC2 instance\.

```
file-system-id efs-mount-point efs _netdev,tls,accesspoint=access-point-id 0 0
```

For more information about EFS access points, see [Working with Amazon EFS Access Points](efs-access-points.md)\.

## Mounting on your on\-premises Linux client with the EFS mount helper over AWS Direct Connect and VPN<a name="mounting-fs-mount-helper-direct"></a>

You can mount your Amazon EFS file systems on your on\-premises data center servers when connected to your Amazon VPC with AWS Direct Connect or VPN\. Mounting your Amazon EFS file systems with amazon\-efs\-utils also makes mounting simpler with the mount helper and allows you to enable encryption of data in transit\. 

To see how to use amazon\-efs\-utils with AWS Direct Connect and VPN to mount Amazon EFS file systems onto on\-premises Linux clients, see [Walkthrough: Create and Mount a File System On\-Premises with AWS Direct Connect and VPN](efs-onpremises.md)\.