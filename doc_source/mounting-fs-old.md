# Mounting file systems without the EFS mount helper<a name="mounting-fs-old"></a>

**Note**  
In this section, you can learn how to mount your Amazon EFS file system without the amazon\-efs\-utils package\. To use encryption of data in transit with your file system, you must mount your file system with Transport Layer Security \(TLS\)\. To do so, we recommend using the amazon\-efs\-utils package\. For more information, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\.

Following, you can learn how to install the Network File System \(NFS\) client and how to mount your Amazon EFS file system on an Amazon EC2 instance\. You also can find an explanation of the `mount` command and the available options for specifying your file system's Domain Name System \(DNS\) name in the `mount` command\. In addition, you can find how to use the file `fstab` to automatically remount your file system after any system restarts\.

**Note**  
Before you can mount a file system, you must create, configure, and launch your related AWS resources\. For detailed instructions, see [Getting started with Amazon Elastic File System](getting-started.md)\.

**Note**  
Prior to mounting your file system, you need to create VPC security groups for your Amazon EC2 instances and mount targets with the required inbound and outbound access\. For more information, see [Using VPC security groups for Amazon EC2 instances and mount targets](network-access.md)\.

**Topics**
+ [NFS support](#mounting-fs-nfs-info)
+ [Installing the NFS client](#mounting-fs-install-nfsclient)
+ [Recommended NFS mount options](mounting-fs-nfs-mount-settings.md)
+ [Mounting on Amazon EC2 with a DNS name](mounting-fs-mount-cmd-dns-name.md)
+ [Mounting with an IP address](mounting-fs-mount-cmd-ip-addr.md)

## NFS support<a name="mounting-fs-nfs-info"></a>

Amazon EFS supports the Network File System versions 4\.0 and 4\.1 \(NFSv4\) protocols when mounting your file systems on Amazon EC2 instances\. Although NFSv4\.0 is supported, we recommend that you use NFSv4\.1\. Mounting your Amazon EFS file system on your Amazon EC2 instance also requires an NFS client that supports your chosen NFSv4 protocol\. Amazon EC2 Mac instances running macOS Big Sur only support NFS v4\.0\.

Amazon EFS does not support the `nconnect` mount option\.

**Note**  
For Linux kernel versions 5\.4\.\*, the Linux NFS client uses a default `read_ahead_kb` value of 128 KB\. We recommend increasing this value to 15 MB\. For more information, see [Optimizing the NFS read\_ahead\_kb size](performance-tips.md#efs-perf-optimize-nfs-read-ahead)\.

For optimal performance and to avoid a variety of known NFS client bugs, we recommend working with a recent Linux kernel\. If you are using an enterprise Linux distribution, we recommend the following:
+ Amazon Linux 2
+ Amazon Linux 2017\.09 or newer
+ Red Hat Enterprise Linux \(and derivatives such as CentOS\) version 7 and newer
+ Ubuntu 16\.04 LTS and newer
+ SLES 12 Sp2 or later

If you are using another distribution or a custom kernel, we recommend kernel version 4\.3 or newer\.

**Note**  
RHEL 6\.9 might be suboptimal for certain workloads due to [Poor Performance When Opening Many Files in Parallel](troubleshooting-efs-general.md#open-close-operations-serialized)\.

**Note**  
Using Amazon EFS with Amazon EC2 instances running Microsoft Windows is not supported\.

### Troubleshooting AMI and kernel versions<a name="ami-kernel-versions-troubleshooting"></a>

To troubleshoot issues related to certain AMI or kernel versions when using Amazon EFS from an EC2 instance, see [Troubleshooting AMI and Kernel Issues](troubleshooting-efs-ami-kernel.md)\.

## Installing the NFS client<a name="mounting-fs-install-nfsclient"></a>

To mount your Amazon EFS file system on your Amazon EC2 instance, first you need to install an NFS client\. To connect to your EC2 instance and install an NFS client, you need the public DNS name of the EC2 instance and a user name to log in\. That user name for your instance is typically `ec2-user`\.

**To connect your EC2 instance and install the NFS client**

1. Connect to your EC2 instance\. Note the following about connecting to the instance:
   + To connect to your instance from a computer running macOS or Linux, specify the \.pem file to your Secure Shell \(SSH\) client with the `-i` option and the path to your private key\.
   + To connect to your instance from a computer running Windows, you can use either MindTerm or PuTTY\. If you plan to use PuTTY, you need to install it and use the following procedure to convert the \.pem file to a \.ppk file\. 

   For more information, see the following topics in the *Amazon EC2 User Guide for Linux Instances*:
   +  [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) 
   +  [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)

     The key file cannot be publicly viewable for SSH\. You can use the `chmod 400` *filename*`.pem` command to set these permissions\. For more information, see [Create a Key Pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-key-pair)\.

1. \(Optional\) Get updates and reboot\.

   ```
   $ sudo yum -y update  
   $  sudo reboot
   ```

1. After the reboot, reconnect to your EC2 instance\.

1. Install the NFS client\.

   If you're using an Amazon Linux AMI or Red Hat Linux AMI, install the NFS client with the following command\.

   ```
   $ sudo yum -y install nfs-utils
   ```

   If you're using an Ubuntu Amazon EC2 AMI, install the NFS client with the following command\.

   ```
   $ sudo apt-get -y install nfs-common
   ```

1. Start the NFS service using the following commands\. For RHEL 7:

   ```
   $ sudo service nfs start
   ```

   For RHEL 8:

   ```
   $ sudo service nfs-server start
   ```

1. Verify that the NFS service started, as follows\.

   ```
   $ sudo service nfs status
   Redirecting to /bin/systemctl status nfs.service
   ● nfs-server.service - NFS server and services
      Loaded: loaded (/usr/lib/systemd/system/nfs-server.service; disabled; vendor preset: disabled)
      Active: active (exited) since Wed 2019-10-30 16:13:44 UTC; 5s ago
     Process: 29446 ExecStart=/usr/sbin/rpc.nfsd $RPCNFSDARGS (code=exited, status=0/SUCCESS)
     Process: 29441 ExecStartPre=/bin/sh -c /bin/kill -HUP `cat /run/gssproxy.pid` (code=exited, status=0/SUCCESS)
     Process: 29439 ExecStartPre=/usr/sbin/exportfs -r (code=exited, status=0/SUCCESS)
    Main PID: 29446 (code=exited, status=0/SUCCESS)
      CGroup: /system.slice/nfs-server.service
   ```

If you use a custom kernel \(that is, if you build a custom AMI\), you need to include at a minimum the NFSv4\.1 client kernel module and the right NFS4 userspace mount helper\.

**Note**  
If you choose **Amazon Linux AMI 2016\.03\.0** or **Amazon Linux AMI 2016\.09\.0** when launching your Amazon EC2 instance, you don't need to install `nfs-utils` because it's already included in the AMI by default\.

**Next: Mount your file system**  
Use one of the following procedures to mount your file system\.
+ [Mounting on Amazon EC2 with a DNS name](mounting-fs-mount-cmd-dns-name.md)
+ [Mounting with an IP address](mounting-fs-mount-cmd-ip-addr.md)
+ [Mounting your Amazon EFS file system automatically](efs-mount-helper.md#mount-fs-auto-mount-onreboot)