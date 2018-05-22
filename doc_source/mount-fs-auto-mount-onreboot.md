# Mounting Your Amazon EFS File System Automatically<a name="mount-fs-auto-mount-onreboot"></a>

You can use `fstab` to automatically mount your Amazon EFS file system using the mount helper whenever the Amazon EC2 instance it is mounted on reboots\. For more information on the mount helper, see [EFS Mount Helper](using-amazon-efs-utils.md#efs-mount-helper)\. You can set up automatic mounting in two ways\. You can update the `/etc/fstab` file in your EC2 instance after you connect to the instance for the first time, or you can configure automatic mounting of your EFS file system when you create your EC2 instance\.

## Updating an Existing EC2 Instance to Mount Automatically<a name="mount-fs-auto-mount-update-fstab"></a>

To automatically remount your Amazon EFS file system directory when the Amazon EC2 instance reboots, you can use the file `fstab`\. The file `fstab` contains information about file systems, and the command `mount -a`, which runs during instance startup, mounts the file systems listed in the `fstab` file\.

**Note**  
Before you can update the /etc/fstab file of your EC2 instance, make sure that you've already created your Amazon EFS file system\. For more information, see [Step 2: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md) in the Amazon EFS Getting Started exercise\.

**To update the /etc/fstab file in your EC2 instance**

1. Connect to your EC2 instance, and open the `/etc/fstab` file in an editor\.

1. Add the following line to the `/etc/fstab` file\.

**For File System witm amazon-efs-utils**
   ```
   fs-12345678:/ /mnt/efs efs defaults,_netdev 0 0
   ```
   
**For File System without amazon-efs-utils**
   ```
   fs-12345678 /mnt/efs nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,_netdev 0 0
   ```
   
**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting-efs-mounting.md#automount-fails)\.

1. Save the changes to the file\.

Your EC2 instance is now configured to mount the EFS file system whenever it restarts\.

**Note**  
If your Amazon EC2 instance needs to start regardless of the status of your mounted Amazon EFS file system, you'll want to add the `nofail` option to your file system's entry in your `etc/fstab` file\.

The line of code you added to the /etc/fstab file does the following\.


| Field | Description | 
| --- | --- | 
|  `fs-12345678:/`  |  The ID for your Amazon EFS file system\. You can get this ID from the console or programmatically from the CLI or an AWS SDK\.  | 
|  `/mnt/efs`  |  The mount point for the EFS file system on your EC2 instance\.  | 
|  `efs`  |  The type of file system\. When you're using the mount helper, this type is always `efs`\.  | 
|  `mount options`  |  Mount options for the file system\. This is a comma\-separated list of the following options: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/mount-fs-auto-mount-onreboot.html)  | 
|  `0`  |  A nonzero value indicates that the file system should be backed up by `dump`\. For EFS, this value should be `0`\.  | 
|  `0`  |  The order in which `fsck` checks file systems at boot\. For EFS file systems, this value should be `0` to indicate that `fsck` should not run at startup\.  | 

## Configuring an EFS File System to Mount Automatically at EC2 Instance Launch<a name="mount-fs-auto-mount-on-creation"></a>

You can configure an Amazon EC2 instance to mount your Amazon EFS file system automatically when it is first launched with a script that works with `cloud-init`\. You add the script during the **Launch Instance** wizard of the EC2 management console\. For an example of how to launch an EC2 instance from the console, see [Getting Started](getting-started.md)\.

The script installs the NFS client and writes an entry in the `/etc/fstab` file that will identify the mount target DNS name as well as the subdirectory in your EC2 instance on which to mount the EFS file system\. The script ensures the file gets mounted when the EC2 instance is launched and after each system reboot\.

For more information about the customized version of `cloud-init` used by Amazon Linux, see [http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonLinuxAMIBasics.html#CloudInit](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonLinuxAMIBasics.html#CloudInit) in the *Amazon EC2 User Guide for Linux Instances*\.

**To configure your EC2 instance to mount an EFS file system automatically at launch**

1. Open the Amazon EC2 console in your web browser, and begin the **Launch Instance** wizard\.

1. When you reach **Step 3: Configure Instance Details**, configure your instance details, expand the **Advanced** section, and then do the following:

   1. Paste the following script into **User data**\. You must update the script by providing the appropriate values for *fs\-12345678* and */mnt/efs*:

     ```
     #cloud-config
     repo_update: true
     repo_upgrade: all
     
     packages:
     - amazon-efs-utils
     
     runcmd:
     - file_system_id_01=fs-12345678
     - efs_directory=/mnt/efs
     
     - mkdir -p ${efs_directory}
     - echo "${file_system_id_01}:/ ${efs_directory} efs tls,_netdev" >> /etc/fstab
     - mount -a -t efs defaults
     ```
**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting-efs-mounting.md#automount-fails)\.

     If you are specifying a custom path to your mount point, as in the example, you may want to use `mkdir -p`, because the `-p` option creates intermediate parent directories as needed\. The `- chown` line of the preceding example changes the ownership of the directory at the mount point from the root user to the default Linux system user account for Amazon Linux, `ec2-user`\. You can specify any user with this command, or leave it out of the script to keep ownership of that directory with the root user\.

     For more information about user data scripts, see [Adding User Data](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html#instancedata-add-user-data) in the *Amazon EC2 User Guide for Linux Instances*\. 

1. Complete the **Launch Instance** wizard\.
**Note**  
To verify that your EC2 instance is working correctly, you can integrate these steps into the Getting Started exercise\. For more information, see [Getting Started](getting-started.md)\.

Your EC2 instance is now configured to mount the EFS file system at launch\.
