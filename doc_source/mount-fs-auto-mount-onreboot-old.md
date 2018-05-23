# Mounting Automatically<a name="mount-fs-auto-mount-onreboot-old"></a>

You can use the file `fstab` to automatically mount your Amazon EFS file system whenever the Amazon EC2 instance that it's mounted on reboots\. You can set up automatic mounting in two ways\. You can update the `/etc/fstab` file in your EC2 instance after you connect to the instance for the first time, or you can configure automatic mounting of your EFS file system when you create your EC2 instance\.

## Updating an Existing EC2 Instance to Mount Automatically<a name="mount-fs-auto-mount-update-fstab-old"></a>

To automatically remount your Amazon EFS file system directory when the Amazon EC2 instance reboots, you can use the file `fstab`\. The file `fstab` contains information about file systems, and the command `mount -a`, which runs during instance startup, mounts the file systems listed in the `fstab` file\.

**Note**  
Before you can update the /etc/fstab file of your EC2 instance, make sure that you've already created your Amazon EFS file system and that you're connected to your Amazon EC2 instance\. For more information, see [Step 2: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md) in the Amazon EFS Getting Started exercise\.

**To update the /etc/fstab file in your EC2 instance**

1. Connect to your EC2 instance, and open the `/etc/fstab` file in an editor\.

1. Add the following line to the `/etc/fstab` file\.

   ```
   mount-target-DNS:/ efs-mount-point nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,_netdev,noresvport 0 0
   ```

   If you want to copy the contents of your `/etc/fstab` file between EC2 instances in different Availability Zones \(AZ\), we recommend that you use the file system DNS name\. Don't copy the `/etc/fstab` file between AZs if you're using the mount target DNS name\. If you do, then each file system has a unique DNS name for each Availability Zone with a mount target\. For more information about DNS names, see [Mounting on Amazon EC2 with a DNS Name](mounting-fs-mount-cmd-dns-name.md)\.

1. Save the changes to the file\.

Your EC2 instance is now configured to mount the EFS file system whenever it restarts\.

**Note**  
If your Amazon EC2 instance needs to start regardless of the status of your mounted Amazon EFS file system, add the `nofail` option to your file system's entry in your `etc/fstab` file\.

The line of code you added to the /etc/fstab file sets the following\.


| Field | Description | 
| --- | --- | 
|  `mount-target-DNS:/`  |  The Domain Name Server \(DNS\) name for the file system that you want to mount\. This is the same value used in `mount` commands to mount the subdirectory of your EFS file system\.  | 
|  `efs-mount-point`  |  The mount point for the EFS file system on your EC2 instance\.  | 
|  `nfs4`  |  The type of file system\. For EFS, this type is always `nfs4`\.  | 
|  `mount options`  |  Mount options for the file system\. This is a comma\-separated list of the following options: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/mount-fs-auto-mount-onreboot-old.html) For more information, see [Additional Mounting Considerations](mounting-fs-mount-cmd-general.md)\.  | 
|  `0`  |  A nonzero value indicates that the file system should be backed up by `dump`\. For EFS, this value should be `0`\.  | 
|  `0`  |  The order in which `fsck` checks file systems at boot\. For EFS file systems, this value should be `0` to indicate that `fsck` should not run at startup\.  | 