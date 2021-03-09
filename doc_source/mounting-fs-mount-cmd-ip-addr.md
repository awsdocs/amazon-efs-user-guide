# Mounting with an IP address<a name="mounting-fs-mount-cmd-ip-addr"></a>

As an alternative to mounting your Amazon EFS file system with the DNS name, Amazon EC2 instances can mount a file system using a mount target’s IP address\. Mounting by IP address works in environments where DNS is disabled, such as VPCs with DNS hostnames disabled, and EC2\-Classic instances mounting using ClassicLink\. For more information on ClassicLink, see [ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html) in the *Amazon EC2 User Guide for Linux Instances*\.

You can also configure mounting a file system using the mount target IP address as a fallback option for applications configured to mount the file system using its DNS name by default\. When connecting to a mount target IP address, EC2 instances should mount using the mount target IP address in the same Availability Zone as the connecting instance\.

You can view and copy the exact commands to mount your file system in the **Attach** dialog box\.

**To view and copy the exact commands to mount your EFS file system using the mount target IP address**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. In the Amazon EFS console, choose the file system that you want to mount to display its details page\.

1. To display the mount commands to use for this file system, choose **Attach** in the upper right\.  
![\[Amazon EFS Attach file system screen showing the exact commands to use when mounting the file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fs-attach-nfs-ip.png )

1. The **Attach** screen displays the exact commands to use for mounting the file system\.

   Choose **Mount via IP** to display the command to mount the file system using the mount target IP address in the selected Availability Zone with an NFS client\.
+ Using the IP address of a mount target in the `mount` command, you can mount a file system on your Amazon EC2 Linux instance with the following command\.

  ```
  sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport mount-target-IP:/   efs 
  ```
+ Using the IP address of a mount target in the `mount` command, you can mount a file system on your Amazon EC2 Mac instance running macOS Big Sur with the following command\.

  ```
  sudo mount -t nfs -o nfsvers=4.0,rsize=65536,wsize=65536,hard,timeo=600,retrans=2,noresvport,mountport=2049 mount-target-IP:/ efs
  ```
**Important**  
You must use `mountport=2049` in order to succesfully connect to the EFS file system when mounting on EC2 Mac instances running macOS Big Sur\.

## Mounting with an IP address in AWS CloudFormation<a name="mount-fs-ip-addr-cloudformation"></a>

You can also mount your file system using an IP address in an AWS CloudFormation template\. For more information, see [storage\-efs\-mountfilesystem\-ip\-addr\.config](https://github.com/awsdocs/elastic-beanstalk-samples/blob/master/configuration-files/community-provided/instance-configuration/storage-efs-mountfilesystem-ip-addr.config) in the **awsdocs/elastic\-beanstalk\-samples** repository for community\-provided configuration files on GitHub\.