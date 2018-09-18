# Mounting with an IP Address<a name="mounting-fs-mount-cmd-ip-addr"></a>

As an alternative to mounting your Amazon EFS file system with the DNS name, Amazon EC2 instances can mount a file system using a mount target’s IP address\. Mounting by IP address works in environments where DNS is disabled, such as VPCs with DNS hostnames disabled, and EC2\-Classic instances mounting using ClassicLink\. For more information on ClassicLink, see [ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html) in the *Amazon EC2 User Guide for Linux Instances*\.

You can also configure mounting a file system using the mount target IP address as a fallback option for applications configured to mount the file system using its DNS name by default\. When connecting to a mount target IP address, EC2 instances should mount using the mount target IP address in the same Availability Zone as the connecting instance\.

You can get the mount target IP address for your EFS file system through the console using the following procedure\.

**To obtain the mount target IP address for your EFS file system**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose the **Name** value of your EFS file system for **File systems**\.

1. In the **Mount targets** table, identify the **Availability Zone** that you want to use to mount your EFS file system to your EC2 instance\.

1. Make a note of the **IP address** associated with your chosen **Availability Zone**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/mount-ip-600w.png)

You can specify the IP address of a mount target in the `mount` command, as shown following\.

```
$ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport mount-target-IP:/   ~/efs-mount-point  
```

## Mounting with an IP Address in AWS CloudFormation<a name="mount-fs-ip-addr-cloudformation"></a>

You can also mount your file system using an IP address in a AWS CloudFormation template\. For more information, see [storage\-efs\-mountfilesystem\-ip\-addr\.config](https://github.com/awsdocs/elastic-beanstalk-samples/blob/master/configuration-files/community-provided/instance-configuration/storage-efs-mountfilesystem-ip-addr.config) in the **awsdocs/elastic\-beanstalk\-samples** repository for community\-provided configuration files on GitHub\.