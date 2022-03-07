# Mounting file systems using the EFS mount helper<a name="efs-mount-helper"></a>

The EFS mount helper helps you mount your EFS file systems on your EC2 Linux and Mac instances running the supported distributions listed in [Overview](overview-amazon-efs-utils.md)\. 

The Amazon EFS mount helper simplifies mounting your file systems\. It includes the Amazon EFS recommended mount options by default\. Additionally, the mount helper has built\-in logging for troubleshooting purposes\. If you encounter an issue with your Amazon EFS file system, you can share these logs with AWS Support\. For more information about mounting your file system, see [Mounting EFS file systems](mounting-fs.md)\. 

**Note**  
Amazon EFS does not support mounting from Amazon EC2 Windows instances\.

**Topics**
+ [How it works](#efs-mount-helper-how)
+ [Getting support logs](#mount-helper-logs)
+ [Prerequisites for using the EFS mount helper](#mount-helper-prerequisites)
+ [Mounting on Amazon EC2 Linux instances using the EFS mount helper](#mounting-fs-mount-helper-ec2-linux)
+ [Mounting on Amazon EC2 Mac instances using the EFS mount helper](#mounting-fs-mount-helper-ec2-mac)
+ [Mounting Amazon EFS file systems from a different AWS Region](#mount-different-region)
+ [Mounting file systems with One Zone storage classes](#mounting-one-zone)
+ [Mounting with IAM authorization](#mounting-IAM-option)
+ [Mounting with EFS access points](#mounting-access-points)
+ [Mounting on your on\-premises Linux client with the EFS mount helper over AWS Direct Connect and VPN](#mounting-fs-mount-helper-direct)
+ [Mounting your Amazon EFS file system automatically](#mount-fs-auto-mount-onreboot)
+ [Mounting EFS to multiple EC2 instances using AWS Systems Manager](#mount-multiple-ec2-instances)
+ [Mounting EFS file systems from another AWS account or VPC](#manage-fs-access-vpc-peering)

## How it works<a name="efs-mount-helper-how"></a>

The mount helper defines a new network file system type, called `efs`, which is fully compatible with the standard `mount` command in Linux\. The mount helper also supports mounting an Amazon EFS file system at instance boot time automatically by using entries in the `/etc/fstab` configuration file\.

**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting-efs-mounting.md#automount-fails)\.

You can mount a file system by specifying one of the following properties:
+ **File system DNS name** – If you use the file system DNS name, and the mount helper cannot resolve it, for example when you are mounting a file system in a different VPC, it will fall back to using the mount target ip address\. For more information, see [Mounting EFS file systems from another AWS account or VPC](#manage-fs-access-vpc-peering)\.
+ **File system ID** – If you use the file system ID, the mount helper resolves the it to the local IP address of the mount target elastic network interface \(ENI\) without calling external resources\.
+ **Mount target IP address** – You can use the IP address of one of the file systems mount targets\.

You can find the value for all of these properties in the Amazon EFS console\. The file system DNS name is found in the **Attach** screen\.

When encryption of data in transit is declared as a mount option for your Amazon EFS file system, the mount helper initializes a client `stunnel` process, and a supervisor process called `amazon-efs-mount-watchdog`\. The `amazon-efs-mount-watchdog` process monitors the health of TLS mounts, and is started automatically the first time an EFS file system is mounted over TLS\. This process is managed by either `upstart` or `systemd` depending on your Linux distribution, and by `launchd` on the macOS Big Sur distribution

`Stunnel` is an open\-source multipurpose network relay\. The client `stunnel` process listens on a local port for inbound traffic, and the mount helper redirects NFS client traffic to this local port\. 

The mount helper uses TLS version 1\.2 to communicate with your file system\. Using TLS requires certificates, and these certificates are signed by a trusted Amazon Certificate Authority\. For more information on how encryption works, see [Data encryption in Amazon EFS](encryption.md)\.

### Mount options used by Amazon EFS client<a name="mount-helper-setting"></a>

The Amazon EFS client uses the following mount options that are optimized for Amazon EFS:
+ `nfsvers=4.1` – used when mounting on EC2 Linux instances

  `nfsvers=4.0` – used when mounting on an EC2 Mac instance running MacOS Big Sur
+ `rsize=1048576`
+ `wsize=1048576`
+ `hard`
+ `timeo=600`
+ `retrans=2`
+ `noresvport`
+ `mountport=2049` – only used when mounting on EC2 Mac instances running macOS Big Sur

## Getting support logs<a name="mount-helper-logs"></a>

The mount helper has built\-in logging for your Amazon EFS file system\. You can share these logs with AWS Support for troubleshooting purposes\. 

You can find the logs stored in `/var/log/amazon/efs` for systems with the mount helper installed\. These logs are for the mount helper, the stunnel process itself, and for the `amazon-efs-mount-watchdog` process that monitors the stunnel process\.

**Note**  
The watchdog process ensures that each mount's stunnel process is running, and stops the stunnel when the Amazon EFS file system is unmounted\. If for some reason a stunnel process is terminated unexpectedly, the watchdog process restarts it\.

You can change the configuration of your logs in `/etc/amazon/efs/efs-utils.conf`\. However, doing so requires unmounting and then remounting the file system with the mount helper for the changes to take effect\. Log capacity for the mount helper and watchdog logs is limited to 20 MiB\. Logs for the stunnel process are disabled by default\.

**Important**  
You can enable logging for the stunnel process logs\. However, enabling the stunnel logs can use up a nontrivial amount of space on your file system\.

## Prerequisites for using the EFS mount helper<a name="mount-helper-prerequisites"></a>

You can mount an Amazon EFS file system on an Amazon EC2 instance using the Amazon EFS mount helper\. To use the mount helper, you need the following:
+ **File system ID of the file system to mount** \- The EFS mount helper resolves the file system ID to the local IP address of the mount target elastic network interface \(ENI\) without calling external resources\.
+ **An Amazon EFS mount target** – You create mount targets in your virtual private cloud \(VPC\)\. If you create your file system in the console using the service recommended settings, a mount target is created in each availability zone in the AWS Region that the file system is in\. For instructions to create mount targets, see [Creating and managing mount targets](accessing-fs.md)\.
**Note**  
We recommend that you wait 90 seconds after creating a mount target before you mount your file system\. This wait lets the DNS records propagate fully in the AWS Region where the file system is\.

  If you use a mount target in an Availability Zone different from that of your Amazon EC2 instance, you incur standard EC2 charges for data sent across Availability Zones\. You also might see increased latencies for file system operations\.
**Note**  
Amazon EFS file systems using One Zone storage classes have a single mount target located in the same availability zone as the file system\. To use the EFS mount helper, the AWS compute instance mounting the file system must be located in the same availability zone as the file system\.
+ For mounting file systems with **One Zone storage classes** from a different Availability Zone:
  + **The name of the file system's Availability Zone ** – If you are mounting an EFS file system using One Zone storage classes that is located in a different Availability Zone than the EC2 instance\.
  + **Mount target DNS name** – Alternatively, you can specify the mount target's DNS name instead of the Availability Zone\.
+ **An Amazon EC2 instance running one of the supported Linux or macOS distributions** – The supported distributions for mounting your file system with the mount helper are the following:
  + Amazon Linux 2
  + Amazon Linux 2017\.09 and newer
  + macOS Big Sur
  + Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer
  + Ubuntu 16\.04 LTS and newer
**Note**  
EC2 Mac instances running macOS Big Sur support NFS 4\.0 only\.
+ **The Amazon EFS mount helper installed on the EC2 instance** – The mount helper is a tool in the `amazon-efs-utils`\. For information about installing `amazon-efs-utils`, see [Manually installing the Amazon EFS client](installing-amazon-efs-utils.md)\.
+ **The EC2 instance is in a VPC** – The connecting EC2 instance must be in a virtual private cloud \(VPC\) based on the Amazon VPC service\. It also must be configured to use the DNS server provided by AWS\. For information about the Amazon DNS server, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.
+ **VPC has DNS hostnames enabled** – The VPC of the connecting EC2 instance must have DNS hostnames enabled\. For more information, see [Viewing DNS Hostnames for Your EC2 Instance](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-viewing) in the *Amazon VPC User Guide*\. 

## Mounting on Amazon EC2 Linux instances using the EFS mount helper<a name="mounting-fs-mount-helper-ec2-linux"></a>

**To mount your Amazon EFS file system using the mount helper on EC2 Linux instances**

1. Open a terminal window on your EC2 instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information, see [Connect to your Linux instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) for Linux instances\.

1. Run one of the following commands to mount your file system\.
**Note**  
If the EC2 instance and the file system you are mounting are located in different AWS Regions, see [Mounting Amazon EFS file systems from a different AWS Region](#mount-different-region) to edit the `region` property in the `efs-utils.conf` file\.
   + To mount using the file system id:

     ```
     sudo mount -t efs file-system-id efs-mount-point/
     ```

     ```
     sudo mount -t efs fs-12345678 efs/
     ```

     Alternatively, if you want to use encryption of data in transit, you can mount your file system with the following command\.

     ```
     sudo mount -t efs -o tls fs-12345678 efs/
     ```
   + To mount using the file system DNS name:

     ```
     sudo mount -t efs -o tls file-system-dns-name efs-mount-point/
     ```

     ```
     sudo mount -t efs -o tls fs-12345678.efs.us-east-2.amazonaws.com efs/
     ```
   + To mount using the mount target IP address:

     ```
     sudo mount -t efs -o tls,mounttargetip=mount-target-ip file-system-id efs-mount-point/
     ```

     ```
     sudo mount -t efs -o tls,mounttargetip=192.0.2.0 fs-12345678 efs/
     ```

   You can view and copy the exact commands to mount your file system in the **Attach** dialog box\.

   1. In the Amazon EFS console, choose the file system that you want to mount to display its details page\.

   1. To display the mount commands to use for this file system, choose **Attach** in the upper right\.  
![\[Amazon EFS Attach file system screen showing the exact commands to use when mounting the file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fs-attach.png )

      The **Attach** screen displays the exact commands to use for mounting the file system in the following ways:
      + \(**Mount via DNS**\) Using the file system's DNS name with the EFS mount helper or an NFS client\.
      + \(**Mount via IP**\) Using the mount target IP address in the selected Availability Zone with an NFS client\.

## Mounting on Amazon EC2 Mac instances using the EFS mount helper<a name="mounting-fs-mount-helper-ec2-mac"></a>

**To mount your Amazon EFS file system using the mount helper on EC2 Mac instances running macOS Big Sur**

1. Open a terminal window on your EC2 Mac instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information, see [Connect to your instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-mac-instances.html#mac-instance-ssh) for Mac instances, in the *Amazon EC2 User Guide for Linux Instances*\.

1. Run the following command to mount your file system\. 
**Note**  
By default, the EFS mount helper uses encryption in transit when mounting on EC2 Mac instances, whether or not you use the `tls` option in the mount command\.

   ```
   sudo mount -t efs file-system-id efs-mount-point/
   ```

   ```
   sudo mount -t efs fs-12345678 efs/
   ```

   You can also use the `tls` option when mounting\.

   ```
   sudo mount -t efs -o tls fs-12345678:/ efs
   ```

   To mount a file system on an EC2 Mac instance without using encryption in transit, use the `notls` option, as shown in the following command\.

   ```
   sudo mount -t efs -o notls file-system-id efs-mount-point/
   ```

   You can view and copy the exact commands to mount your file system in the management console's **Attach** dialog box, described as follows\.

   1. In the Amazon EFS console, choose the file system that you want to mount to display its details page\.

   1. To display the mount commands to use for this file system, choose **Attach** in the upper right\.  
![\[Amazon EFS Attach file system screen showing the exact commands to use when mounting the file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fs-attach.png )

      The **Attach** screen displays the exact commands to use for mounting the file system in the following ways:
      + \(**Mount via DNS**\) Using the file system's DNS name with the EFS mount helper or an NFS client\.
      + \(**Mount via IP**\) Using the mount target IP address in the selected Availability Zone with an NFS client\.
**Note**  
If the EC2 instance and the file system you are mounting are located in different AWS Regions, see [Mounting Amazon EFS file systems from a different AWS Region](#mount-different-region) to edit the `region` property in the `efs-utils.conf` file\.

## Mounting Amazon EFS file systems from a different AWS Region<a name="mount-different-region"></a>

If you are mounting your EFS file system from an Amazon EC2 instance that is in a different AWS Region than the file system, you will need to edit the `region` property value in the `efs-utils.conf` file\.

**To edit the region property in `efs-utils.conf`**

1. Access the terminal for your EC2 instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to your Linux instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Locate the `efs-utils.conf` file, and open it using your preferred editor\.

1. Locate the following line:

   ```
   #region = us-east-1
   ```

   1. Uncomment the line\.

   1. If the file system is not located in the `us-east-1` region, replace `us-east-1` with the ID of the region in which the file system is located\.

   1. Save the changes\.

1. Mount the file system using the EFS mount helper for [Linux](#mounting-fs-mount-helper-ec2-linux) or [Mac](#mounting-fs-mount-helper-ec2-mac) instances\.

## Mounting file systems with One Zone storage classes<a name="mounting-one-zone"></a>

Amazon EFS file systems that use One Zone storage classes support only a single mount target which is located in the same Availability Zone as the file system\. You cannot add additional mount targets\. This section describes things to consider when mounting Amazon EFS file systems that use One Zone storage classes\.

You can avoid data transfer charges between Availability Zones and achieve better performance by accessing an EFS file system using an Amazon EC2 compute instance that is located in the same Availability Zone as that of the file system's mount target\. This applies to file systems using EFS Standard or One Zone storage classes\.

### Mounting file systems that use One Zone storage classes on EC2 in a different Availability Zone<a name="mounting-one-zone-efs-util"></a>

If you are mounting an EFS file system using One Zone storage classes on an EC2 instance that is located in a different Availability Zone, you have to specify the file system's Availability Zone name or the DNS name of the file system's mount target in the mount helper mount command\.

The following command specifies the file system's availability zone name\.

```
sudo mount -t efs -o az=availability-zone-name,tls file-system-id /mnt
```

This is the command with sample values:

```
sudo mount -t efs -o az=us-east-1a,tls fs-12345678 /mnt
```

The following command specifies the DNS name of the file system's mount target\.

```
sudo mount -t efs tls mount-target-dns-name /mnt
```

This is the command with an example mount target DNS name\. 

```
sudo mount -t efs tls us-east-1a.fs-12345678.efs.amazonaws.com /mnt
```

### Mounting file systems with One Zone storage with other AWS compute instances<a name="mounting-one-zone-other-compute-instances"></a>

When you use an Amazon EFS file system with One Zone storage classes with Amazon Elastic Container Service, Amazon Elastic Kubernetes Service, or AWS Lambda, you need to configure the service to use the same Availability Zone that the EFS file system is located in, illustrated as follows, and described in the following sections\.

![\[Diagram showing AWS compute instances connecting to an EFS One Zone file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/efs-mount-onezone.png)

#### Connecting from Amazon Elastic Container Service<a name="mount-one-zone-ecs"></a>

You can use Amazon EFS file systems with Amazon ECS to share file system data across your fleet of container instances so your tasks have access to the same persistent storage, no matter the instance on which they land\. To use Amazon EFS One Zone storage classes with Amazon ECS, you should choose only subnets that are in the same Availability Zone as your file system when launching your task\. For more information, see [Amazon EFS volumes](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/efs-volumes.html) in the *Amazon Elastic Container Service Developer Guide*\.

#### Connecting from Amazon Elastic Kubernetes Service<a name="mount-one-zone-eks"></a>

When mounting an Amazon EFS file system that uses One Zone storage classes from Amazon EKS, you can use the Amazon EFS [Container Storage Interface](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) \(CSI\) driver, which supports Amazon EFS access points, to share a file system between multiple pods in an Amazon EKS or self\-managed Kubernetes cluster\. The Amazon EFS CSI driver is installed in the Fargate stack\. When using the Amazon EFS CSI driver with Amazon EFS One Zone storage classes, you can use the `nodeSelector` option when launching your pod to ensure it gets scheduled within the same availability zone as your file system\.

#### Connecting from AWS Lambda<a name="mount-one-zone-lambda"></a>

You can use Amazon EFS with AWS Lambda to share data across function invocations, read large reference data files, and write function output to a persistent and shared store\. AWS Lambda securely connects the function instances to the Amazon EFS mount targets that are in the same Availability Zone and subnet\. When you use AWS Lambda with Amazon EFS file systems using EFS One Zone storage classes, configure your function to only launch invocations into subnets that are in the same Availability Zone as your file system\.

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

To see how to use amazon\-efs\-utils with AWS Direct Connect and VPN to mount Amazon EFS file systems onto on\-premises Linux clients, see [Walkthrough: Create and mount a file system on\-premises with AWS Direct Connect and VPN](efs-onpremises.md)\.

## Mounting your Amazon EFS file system automatically<a name="mount-fs-auto-mount-onreboot"></a>

You can configure an Amazon EC2 instance to automatically mount an EFS file system when it reboots in two ways:
+ When you create a new EC2 instance using the Launch Instance Wizard\.
+ Update the EC2 `/etc/fstab` file with an entry for the EFS file system\.

Both of these methods use the EFS mount helper to mount the file system\. The mount helper is part of the `amazon-efs-utils` set of tools\. 

The `amazon-efs-utils` tools are available for installation on Amazon Linux and Amazon Linux 2 Amazon Machine Images \(AMIs\)\. For more information about `amazon-efs-utils`, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\. If you are using another Linux distribution, such as Red Hat Enterprise Linux \(RHEL\), manually build and install `amazon-efs-utils`\. For more information, see [Installing the Amazon EFS client on other Linux distributions](installing-amazon-efs-utils.md#installing-other-distro)\.

**Note**  
Amazon EFS file systems do not support automatic mounting on Amazon EC2 Mac instances running macOS Big Sur\.

### Configuring EC2 instances to mount an EFS file system at instance launch<a name="mount-fs-auto-mount-on-creation"></a>

When you create a new Amazon EC2 Linux instance using the EC2 Launch Instance Wizard, you can configure it to mount your Amazon EFS file system automatically\. The EC2 instance mounts the file system automatically the instance first launched and also whenever it restarts\.

**Note**  
Amazon EFS file systems do not support mounting on Amazon EC2 Mac instances running macOS Big Sur at instance launch\.

Before you perform this procedure, make sure that you have created your Amazon EFS file system\. For more information, see [Step 1: Create your Amazon EFS file system](gs-step-two-create-efs-resources.md) in the Amazon EFS Getting Started exercise\.

**Note**  
You can't use Amazon EFS with Microsoft Windows–based Amazon EC2 instances\.

Before you can launch and connect to an Amazon EC2 instance, you need to create a key pair, unless you already have one\. Follow the steps in [Setting Up with Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances* to create a key pair\. If you already have a key pair, you can use it for this exercise\.

**To configure your EC2 instance to mount an EFS file system automatically at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. In **Step 1: Choose an Amazon Machine Image \(AMI\)**, find an Amazon Linux AMI at the top of the list and choose **Select**\.

1. In **Step 2: Choose an Instance Type**, choose **Next: Configure Instance Details**\.

1. In **Step 3: Configure Instance Details**, provide the following information: 
   + For **Network**, choose the entry for the same VPC that the EFS file system you're mounting is in\.
   + For **Subnet**, choose a default subnet in any Availability Zone\.
   + For **File systems**, choose the EFS file system that you want to mount\. The path shown next the file system ID is the mount point that the EC2 instance will use, which you can change\.
   + Under **Advanced Details**, the **User data** is automatically generated, and includes the commands needed to mount the EFS file systems you specified under **File systems**\.

1. Choose **Next: Add Storage**\.

1. Choose **Next: Add Tags**\.

1. Name your instance and choose **Next: Configure Security Group**\.

1. In **Step 6: Configure Security Group**, set **Assign a security group** to **Select an existing security group**\. Choose the default security group to make sure that it can access your EFS file system\.

   You can't access your EC2 instance by Secure Shell \(SSH\) using this security group\. For access by SSH, later you can edit the default security and add a rule to allow SSH or a new security group that allows SSH\. You can use the following settings:
   + **Type:** SSH
   + **Protocol:** TCP
   + **Port Range:** 22
   + **Source:** Anywhere 0\.0\.0\.0/0

1. Choose **Review and Launch**\.

1. Choose **Launch**\.

1. Select the check box for the key pair that you created, and then choose **Launch Instances**\.

Your EC2 instance is now configured to mount the EFS file system at launch and whenever it's rebooted\.

### Using /etc/fstab to mount automatically<a name="mount-fs-auto-mount-update-fstab"></a>

To automatically remount your Amazon EFS file system directory when the Amazon EC2 instance reboots, use the file `/etc/fstab`\. The `/etc/fstab` file contains information about file systems\. The command `mount -a`, which runs during instance start\-up, mounts the file systems listed in `/etc/fstab`\. This procedure uses the EFS mount helper to mount the file system which needs to be installed on the EC2 instance\. 

**Note**  
Amazon EFS file systems do not support automatic mounting using `/etc/fstab` on Amazon EC2 Mac instances running macOS Big Sur\.

The mount helper is part of the `amazon-efs-utils` set of tools, which is available for installation on Amazon Linux and Amazon Linux 2 Amazon Machine Images \(AMIs\)\. For more information about installing `amazon-efs-utils` on an Amazon Linux or Amazon Linux 2 AMI, see [Manually installing the Amazon EFS client](installing-amazon-efs-utils.md)\. If you are using another Linux distribution, such as Red Hat Enterprise Linux \(RHEL\), manually build and install `amazon-efs-utils`\. For more information, see [Installing the Amazon EFS client on other Linux distributions](installing-amazon-efs-utils.md#installing-other-distro)\.

**Note**  
Before you can update the `/etc/fstab` file of your EC2 instance, make sure that you already created your Amazon EFS file system\. For more information, see [Step 1: Create your Amazon EFS file system](gs-step-two-create-efs-resources.md)\.

**To update the /etc/fstab file on your EC2 instance**

1. Connect to your EC2 instance:
   + To connect to your instance from a computer running macOS or Linux, specify the \.pem file for your SSH command\. To do this, use the `-i` option and the path to your private key\.
   + To connect to your instance from a computer running Windows, you can use either MindTerm or PuTTY\. To use PuTTY, install it and convert the \.pem file to a \.ppk file\.

   For more information, see the following topics in the *Amazon EC2 User Guide for Linux Instances*:
   +  [Connecting to your Linux instance from Windows using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) 
   +  [Connecting to your Linux instance using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) 

1. Open the `/etc/fstab` file in an editor\.

1. Automatically mount your EFS file system using either IAM authorization or an EFS access point:
   + To automatically mount with IAM authorization to an Amazon EC2 instance that has an instance profile, add the following line to the `/etc/fstab` file\.

     ```
     file-system-id:/ efs-mount-point efs _netdev,noresvport,tls,iam 0 0
     ```
   + To automatically mount with IAM authorization to a Linux instance using a credentials file, add the following line to the `/etc/fstab` file\.

     ```
     file-system-id:/ efs-mount-point efs _netdev,noresvport,tls,iam,awsprofile=namedprofile 0 0
     ```
   + To automatically mount a file system using an EFS access point, add the following line to the `/etc/fstab` file\.

     ```
     file-system-id efs-mount-point efs _netdev,noresvport,tls,accesspoint=access-point-id 0 0
     ```
**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting-efs-mounting.md#automount-fails)\.

   For more information, see [Mounting with IAM authorization](#mounting-IAM-option) and [Mounting with EFS access points](#mounting-access-points)\.

1. Save the changes to the file\.

1. Test the `fstab` entry by using the `mount` command with the `'fake'` option along with the `'all'` and `'verbose'` options\.

   ```
   $ sudo mount -fav
   home/ec2-user/efs      : successfully mounted
   ```

Your EC2 instance is now configured to mount the EFS file system whenever it restarts\.

**Note**  
In some cases, your Amazon EC2 instance might need to start regardless of the status of your mounted Amazon EFS file system\. In such cases, add the `nofail` option to your file system's entry in your `/etc/fstab` file\.

The line of code you added to the `/etc/fstab` file does the following\.


| Field | Description | 
| --- | --- | 
|  `file-system-id:/`  |  The ID for your Amazon EFS file system\. You can get this ID from the console or programmatically from the CLI or an AWS SDK\.  | 
|  `efs-mount-point`  |  The mount point for the EFS file system on your EC2 instance\.  | 
|  `efs`  |  The type of file system\. When you're using the mount helper, this type is always `efs`\.  | 
|  `mount options`  |  Mount options for the file system\. This is a comma\-separated list of the following options: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/efs-mount-helper.html)  | 
|  `0`  |  A nonzero value indicates that the file system should be backed up by `dump`\. For EFS, this value should be `0`\.  | 
|  `0`  |  The order in which `fsck` checks file systems at boot\. For EFS file systems, this value should be `0` to indicate that `fsck` should not run at start\-up\.  | 

## Mounting EFS to multiple EC2 instances using AWS Systems Manager<a name="mount-multiple-ec2-instances"></a>

You can mount EFS file systems to multiple Amazon EC2 instances remotely and securely without having to log in to the instances by using AWS Systems Manager Run Command\. For more information about AWS Systems Manager Run Command, see [AWS Systems Manager run command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html) in the AWS Systems Manager User Guide\. The following prerequisites are required before mounting EFS file systems using this method:

1. The EC2 instances are launched with an instance profile that includes the `AmazonElasticFileSystemsUtils` permissions policy\. For more information, see [Step 1: Configure an IAM instance profile with the required permissions](manage-efs-utils-with-aws-sys-manager.md#configure-sys-mgr-iam-instance-profile)\.

1. Version 1\.28\.1 or later of the Amazon EFS client \(amazon\-efs\-utils package\) is installed on the EC2 instances\. You can use AWS Systems Manager to automatically install the package on your instances\. For more information, see [Step 2: Configure an Association used by State Manager for installing or updating the Amazon EFS client](manage-efs-utils-with-aws-sys-manager.md#config-sys-mgr-association)\.

**To mount multiple EFS file systems to multiple EC2 instances using the console**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Run Command**\.

1. Choose **Run a command**\.

1. Enter **AWS\-RunShellScript** in the **Commands** search field\.

1. Select **AWS\-RunShellScript**\.

1. In **Command parameters** enter the mount command to use for each EFS file system that you want to mount\. For example:

   ```
   sudo mount -t efs -o tls fs-12345678:/ /mnt/efs
   sudo mount -t efs -o tls,accesspoint=fsap-12345678 fs-01233210 /mnt/efs
   ```

   For more information about EFS mount commands using the Amazon EFS client, see [Mounting on Amazon EC2 Linux instances using the EFS mount helper](#mounting-fs-mount-helper-ec2-linux) or [Mounting on Amazon EC2 Mac instances using the EFS mount helper](#mounting-fs-mount-helper-ec2-mac)\.

1. Select the target AWS Systems Manager managed EC2 instances that you want the command to run on\.

1. Make any other additional settings you would like\. Then choose **Run** to run the command and mount the EFS file systems specified in the command\.

   Once you run the command, you can see its status in the command history\.

## Mounting EFS file systems from another AWS account or VPC<a name="manage-fs-access-vpc-peering"></a>

You can mount your Amazon EFS file system using IAM authorization for NFS clients and EFS Access Points using the EFS mount helper\. By default, the EFS mount helper uses domain name service \(DNS\) to resolve the IP address of your EFS mount target\. If you are mounting the file system from a different account or virtual private cloud \(VPC\), you need to resolve the EFS mount target manually\.

Following, you can find instructions for determining the correct EFS mount target IP address to use for your NFS client\. You can also find instructions for configuring the client to mount the EFS file system using that IP address\.

### Mounting using IAM or access points from another VPC<a name="mount-fs-different-vpc"></a>

When you use a VPC peering connection or transit gateway to connect VPCs, Amazon EC2 instances that are in one VPC can access EFS file systems in another VPC, even if the VPCs belong to different accounts\. 

#### Prerequisites<a name="mount-fs-different-vpc-prerequisites"></a>

Before using the following the procedure, take these steps:
+ Install the Amazon EFS client, part of the `amazon-efs-utils` set of utilities on the compute instance you're mounting the EFS file system on\. You use the EFS mount helper, which is included in `amazon-efs-utils`, to mount the file system\. For instructions on installing `amazon-efs-utils`, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\.
+ Allow the `ec2:DescribeAvailabilityZones` action in the IAM policy for the IAM role you attached to the instance\. We recommend that you attach the AWS managed policy `AmazonElasticFileSystemsUtils` to an IAM entity to provide the necessary permissions for the entity\.
+ When mounting from another AWS account, update the file system resource policy to allow the `elasticfilesystem:DescribeMountTarget` action for the principal ARN of other AWS account\. For example:

  ```
  {
      "Id": "access-point-example03",
      "Statement": [
          {
              "Sid": "access-point-statement-example03",
              "Effect": "Allow",
              "Principal": {"AWS": "arn:aws:iam::555555555555"},
              "Action": "elasticfilesystem:DescribeMountTargets",
              "Resource": "arn:aws:elasticfilesystem:us-east-2:111122223333:file-system/fs-12345678",
          }
      ]
  }
  ```

  For more information about EFS file system resource policies, see [Resource\-based policies](access-control-overview.md#access-control-manage-access-intro-resource-policies)\.
+ Install botocore\. The EFS client uses botocore to retrieve the mount target ip address when the file system DNS name cannot be resolved when mounting a file system in another VPC\. For more information, see [Install botocore](https://github.com/aws/efs-utils#Install-botocore) in the efs\-utils README file\.
+ Set up either a VPC peering connection or a VPC transit gateway\. 

  You connect the client's VPC and your EFS file system's VPC using either a VPC peering connection or a VPC transit gateway\. When you use a VPC peering connection or transit gateway to connect VPCs, Amazon EC2 instances that are in one VPC can access EFS file systems in another VPC, even if the VPCs belong to different accounts\.

  A *transit gateway *is a network transit hub that you can use to interconnect your VPCs and on\-premises networks\. For more information about using VPC transit gateways, see [Getting Started with transit gateways](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html) in the *Amazon VPC Transit Gateways Guide*\.

  A *VPC peering connection* is a networking connection between two VPCs\. This type of connection enables you to route traffic between them using private Internet Protocol version 4 \(IPv4\) or Internet Protocol version 6 \(IPv6\) addresses\. You can use VPC peering to connect VPCs within the same AWS Region or between AWS Regions\. For more information on VPC peering, see [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon VPC Peering Guide*\.

To ensure high availability of your file system, we recommend that you always use an EFS mount target IP address that is in the same availability zone as your NFS client\. If you're mounting an EFS file system that is in another account, ensure that the NFS client and EFS mount target are in the same availability zone ID\. This requirement applies because AZ names can differ from one account to another\.

**To mount an EFS file system in another VPC using IAM or an access point**

1. Connect to your EC2 instance:
   + To connect to your instance from a computer running macOS or Linux, specify the \.pem file for your SSH command\. To do this, use the `-i` option and the path to your private key\.
   + To connect to your instance from a computer running Windows, you can use either MindTerm or PuTTY\. To use PuTTY, install it and convert the \.pem file to a \.ppk file\.

   For more information, see the following topics in the *Amazon EC2 User Guide for Linux Instances*:
   +  [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) 
   +  [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)

1. Create a directory for mounting the file system using the following command\.

   ```
   $ sudo mkdir /mnt/efs
   ```

1. To mount the file system using IAM authorization, use the following command:

   ```
   $ sudo mount -t efs -o tls,iam file-system-dns-name /mnt/efs/
   ```

   For more information about using IAM authorization with EFS, see [Using IAM to control file system data access](iam-access-control-nfs-efs.md)\.

   To mount the file system using an EFS access point, use the following command:

   ```
   $ sudo mount -t efs -o tls,accesspoint=access-point-id file-system-dns-name /mnt/efs/
   ```

   For more information about EFS access points, see [Working with Amazon EFS Access Points](efs-access-points.md)\.

#### Mounting Amazon EFS file systems from a different AWS Region<a name="mount-different-region-vpc"></a>

If you are mounting your EFS file system from another VPC that is in a different AWS Region than the file system, you will need to edit the `efs-utils.conf` file\. In `efs-utils.conf`, locate the following lines:

```
#region = us-east-1
```

Uncomment the line, and replace the value for the ID of the region in which the file system is located, if it is not in `us-east-1`\.

### Mounting from another AWS account in the same VPC<a name="mount-fs-diff-account-same-vpc"></a>

Using shared VPCs, you can mount an Amazon EFS file system that is owned by one AWS account from Amazon EC2 instances that are owned by a different AWS account\. For more information about setting up a shared VPC, see [Working with shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html) in the *Amazon VPC Peering Guide*\. 

After you set up VPC sharing, the EC2 instances can mount the EFS file system using Domain Name System \(DNS\) name resolution or the EFS mount helper\. We recommend using the EFS mount helper to mount your EFS file systems\.