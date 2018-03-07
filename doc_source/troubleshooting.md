# Troubleshooting Amazon EFS<a name="troubleshooting"></a>

Following, you can find information on how to troubleshoot issues for Amazon Elastic File System \(Amazon EFS\)\. For optimal performance and to avoid a variety of known NFS client bugs, we recommend a Linux kernel that is version 4\.0 or newer\.


+ [Troubleshooting Amazon EFS: General Issues](#troubleshooting-efs-general)
+ [File Operation Errors](#troubleshooting-efs-fileop-errors)
+ [Troubleshooting AMI and Kernel Issues](#troubleshooting-efs-ami-kernel)
+ [Troubleshooting Encrypted File Systems](#troubleshooting-efs-encryption)

## Troubleshooting Amazon EFS: General Issues<a name="troubleshooting-efs-general"></a>

Following, you can find information about general troubleshooting issues related to Amazon EFS\. For information on performance, see [Amazon EFS Performance](performance.md)\.


+ [Mounting Multiple Amazon EFS File Systems in /etc/fstab Fails](#automount-fix-multiple-fs)
+ [Mount Command Fails with "wrong fs type" Error Message](#mount-error-wrong-fs)
+ [Mount Command Fails with "incorrect mount option" Error Message](#mount-error-incorrect-mount)
+ [File System Mount Fails Immediately After File System Creation](#mount-fails-propegation)
+ [File System Mount Hangs and Then Fails with Timeout Error](#mount-hangs-fails-timeout)
+ [File System Mount Using DNS Name Fails](#mount-fails-dns-name)
+ [Amazon EC2 Instance Hangs](#ec2-instance-hangs)
+ [Mount Target Lifecycle State Is Stuck](#mount-target-lifecycle-stuck)
+ [File System Mount on Windows Instance Fails](#mount-windows-instance-fails)
+ [Application Writing Large Amounts of Data Hangs](#application-large-data-hangs)
+ [Mount Does Not Respond](#mount-unresponsive)
+ [Open and Close Operations Are Serialized](#open-close-operations-serialized)
+ [Operations on Newly Mounted File System Return "bad file handle" Error](#operations-return-bad-file-handle)
+ [Custom NFS Settings Causing Write Delays](#custom-nfs-settings-write-delays)
+ [Creating Backups with Oracle Recovery Manager Is Slow](#oracle-backup-slow)

### Mounting Multiple Amazon EFS File Systems in /etc/fstab Fails<a name="automount-fix-multiple-fs"></a>

For instances that use the systemd init system with two or more Amazon EFS entries at `/etc/fstab`, there might be times where some or all of these entries are not mounted\. In this case, the `dmesg` output shows one or more lines similar to the following\.

```
NFS: nfs4_discover_server_trunking unhandled error -512. Exiting with error EIO
```

**Action to Take**  
We recommend that you create a new systemd service file in `/etc/systemd/system/mount-nfs-sequentially.service` with the following contents\.

```
[Unit]
Description=Workaround for mounting NFS file systems sequentially at boot time
After=remote-fs.target

[Service]
Type=oneshot
ExecStart=/bin/mount -avt nfs4
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

After you do so, run the following two commands:

1. `sudo systemctl daemon-reload`

1. `sudo systemctl enable mount-nfs-sequentially.service`

Finally, restart your Amazon EC2 instance\. The file systems are mounted on demand, generally within a second\.

### Mount Command Fails with "wrong fs type" Error Message<a name="mount-error-wrong-fs"></a>

The mount command fails with the following error message\.

```
mount: wrong fs type, bad option, bad superblock on 10.1.25.30:/, 
missing codepage or helper program, or other error (for several filesystems 
(e.g. nfs, cifs) you might need a /sbin/mount.<type> helper program)
In some cases useful info is found in syslog - try dmesg | tail or so.
```

**Action to Take**  
Install the `nfs-utils` \(or `nfs-common` on Ubuntu\) package\. For more information, see [Installing the NFS Client](mounting-fs.md#mounting-fs-install-nfsclient)\.

### Mount Command Fails with "incorrect mount option" Error Message<a name="mount-error-incorrect-mount"></a>

The mount command fails with the following error message\.

```
mount.nfs: an incorrect mount option was specified
```

**Action to Take**  
This error message most likely means that your Linux distribution doesn't support Network File System versions 4\.0 and 4\.1 \(NFSv4\)\. To confirm this is the case, you can run the following command:

```
$ grep CONFIG_NFS_V4_1 /boot/config*
```

If the preceding command returns `# CONFIG_NFS_V4_1 is not set`, NFSv4\.1 is not supported on your Linux distribution\. For a list of the Amazon Machine Images \(AMIs\) for Amazon Elastic Compute Cloud \(Amazon EC2\) that support NFSv4\.1, see [NFS Support](mounting-fs.md#mounting-fs-nfs-info)\. 

### File System Mount Fails Immediately After File System Creation<a name="mount-fails-propegation"></a>

It can take up to 90 seconds after creating a mount target for the Domain Name Service \(DNS\) records to propagate fully in a region\.

**Action to Take**  
If you're programmatically creating and mounting file systems, for example with an AWS CloudFormation template, we recommend that you implement a wait condition\.

### File System Mount Hangs and Then Fails with Timeout Error<a name="mount-hangs-fails-timeout"></a>

The file system mount command hangs for a minute or two, and then fails with a timeout error\. The following code shows an example\.

```
$ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 mount-target-ip:/ mnt

[2+ minute wait here]
mount.nfs: Connection timed out
$Â 
```

**Action to Take**

+ This error can occur because either the Amazon EC2 instance or the mount target security groups are not configured properly\. For more information, see [Creating Security Groups](accessing-fs-create-security-groups.md)\.

+ Verify that the mount target IP address that you specified is valid\. If you specify the wrong IP address and there is nothing else at that IP address to reject the mount, you can experience this issue\.

### File System Mount Using DNS Name Fails<a name="mount-fails-dns-name"></a>

A file system mount that is using a DNS name fails\. The following code shows an example\.

```
$ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 file-system-id.efs.aws-region.amazonaws.com:/ mnt   
mount.nfs: Failed to resolve server file-system-id.efs.aws-region.amazonaws.com: 
  Name or service not known.   

$ 
```

**Action to Take**

Check your VPC configuration\. If you are using a custom VPC, make sure that DNS settings are enabled\. For more information, see [Using DNS with Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-dns.html) in the *Amazon VPC User Guide*\. 

To specify a DNS name in the `mount` command, you must do the following:

+ Ensure that there's an Amazon EFS mount target in the same Availability Zone as the Amazon EC2 instance\.

+ Connect your Amazon EC2 instance inside an Amazon VPC configured to use the DNS server provided by Amazon\. For more information, see [DHCP Options Sets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.

+ Ensure that the Amazon VPC of the connecting Amazon EC2 instance has DNS host names enabled\. For more information, see [Updating DNS Support for Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-dns.html#vpc-dns-updating) in the *Amazon VPC User Guide*\.

### Amazon EC2 Instance Hangs<a name="ec2-instance-hangs"></a>

An Amazon EC2 instance can hang because you deleted a file system mount target without first unmounting the file system\. 

**Action to Take**  
Before you delete a file system mount target, unmount the file system\. For more information about unmounting your Amazon EFS file system, see [Unmounting File Systems](mounting-fs-mount-cmd-general.md#unmounting-fs)\.

### Mount Target Lifecycle State Is Stuck<a name="mount-target-lifecycle-stuck"></a>

The mount target lifecycle state is stuck in the **creating** or **deleting** state\.

**Action to Take**  
Retry the `CreateMountTarget` or `DeleteMountTarget` call\.

### File System Mount on Windows Instance Fails<a name="mount-windows-instance-fails"></a>

A file system mount on an Amazon EC2 instance on Microsoft Windows fails\.

**Action to Take**  
Don't use Amazon EFS with Windows EC2 instances, which isn't supported\.

### Application Writing Large Amounts of Data Hangs<a name="application-large-data-hangs"></a>

An application that writes a large amount of data to Amazon EFS hangs and causes the instance to reboot\.

**Action to Take**

If an application takes too long to write all of its data to Amazon EFS, Linux might reboot because it appears that the process has become unresponsive\. Two kernel configuration parameters define this behavior, `kernel.hung_task_panic` and `kernel.hung_task_timeout_secs`\.

In the example following, the state of the hung process is reported by the `ps` command with `D` before the instance reboot, indicating that the process is waiting on I/O\.

```
$ ps aux | grep large_io.py
root 33253 0.5 0.0 126652 5020 pts/3 D+ 18:22 0:00 python large_io.py
/efs/large_file
```

To prevent a reboot, increase the timeout period or disable kernel panics when a hung task is detected\. The following command disables hung task kernel panics on most Linux systems\.

```
$ sudo sysctl -w kernel.hung_task_panic=0
```

### Mount Does Not Respond<a name="mount-unresponsive"></a>

An Amazon EFS mount appears unresponsive\. For example, commands like `ls` hang\.

**Action to Take**

This error can occur if another application is writing large amounts of data to the file system\. Access to the files that are being written might be blocked until the operation is complete\. In general, any commands or applications that attempt to access files that are being written to might appear to hang\. For example, the `ls` command might hang when it gets to the file that is being written\. This is because some Linux distributions alias the `ls` command so that it retrieves file attributes in addition to listing the directory contents\.

To resolve this issue, verify that another application is writing files to the Amazon EFS mount, and that it is in the `Uninterruptible sleep` \(`D`\) state, as in the following example:

```
$ ps aux | grep large_io.py 
root 33253 0.5 0.0 126652 5020 pts/3 D+ 18:22 0:00 python large_io.py /efs/large_file
```

After you've verified that this is the case, you can address the issue by waiting for the other write operation to complete, or by implementing a workaround\. In the example of `ls`, you can use the `/bin/ls` command directly, instead of an alias, which allows the command to proceed without hanging on the file being written\. In general, if the application writing the data can force a data flush periodically, perhaps by using `fsync(2)`, this might help improve the responsiveness of your file system for other applications\. However, this improvement might be at the expense of performance when the application writes data\.

### Open and Close Operations Are Serialized<a name="open-close-operations-serialized"></a>

Open and close operations that are performed on a file system by a user on a single Amazon EC2 instance are serialized\.

**Action to Take**

This issue can be resolved by using NFS protocol version 4\.1, and an Amazon EC2 Amazon Machine Image \(AMI\) that includes a Linux kernel version 4\.0 or newer\. By using NFSv4\.1 when mounting your file systems, you enable parallelized open and close operations on files\. We recommend using **Amazon Linux AMI 2016\.03\.0** as the AMI for the Amazon EC2 instance that you mount your file system to\. 

If you can't use NFSv4\.1, note that the Linux NFSv4\.0 client serializes open and close requests by user ID and group IDs\. This serialization happens even if multiple processes or multiple threads issue requests at the same time\. The client only sends one open or close operation to an NFS server at a time, when all of the IDs match\.

In addition, you can perform any of the following actions to resolve this issue:

+ You can run each process from a different user ID on the same Amazon EC2 instance\.

+ You can leave the user IDs the same across all open requests, and modify the set of group IDs instead\.

+ You can run each process from a separate Amazon EC2 instance\.

### Operations on Newly Mounted File System Return "bad file handle" Error<a name="operations-return-bad-file-handle"></a>

Operations performed on a newly mounted file system return a `bad file handle` error\. 

This error can happen if an Amazon EC2 instance was connected to one file system and one mount target with a specified IP address, and then that file system and mount target were deleted\. If you create a new file system and mount target to connect to that Amazon EC2 instance with the same mount target IP address, this issue can occur\. 

**Action to Take**  
You can resolve this error by unmounting the file system, and then remounting the file system on the Amazon EC2 instance\. For more information about unmounting your Amazon EFS file system, see [Unmounting File Systems](mounting-fs-mount-cmd-general.md#unmounting-fs)\.

### Custom NFS Settings Causing Write Delays<a name="custom-nfs-settings-write-delays"></a>

You have custom NFS client settings, and it takes up to three seconds for an Amazon EC2 instance to see a write operation performed on a file system from another Amazon EC2 instance\.

**Action to Take**

If you encounter this issue, you can resolve it in one of the following ways:

+ If the NFS client on the Amazon EC2 instance that's reading data has attribute caching activated, unmount your file system\. Then remount it with the `noac` option to disable attribute caching\. Attribute caching in NFSv4\.1 is enabled by default\.
**Note**  
Disabling client\-side caching can potentially reduce your application's performance\.

+ You can also clear your attribute cache on demand by using a programming language compatible with the NFS procedures\. To do this, you can send an `ACCESS` procedure request immediately before a read request\.

   For example, using the Python programming language, you can construct the following call\.

  ```
  # Does an NFS ACCESS procedure request to clear the attribute cache, given a path to the file
  import os
  os.access(path, os.W_OK)
  ```

### Creating Backups with Oracle Recovery Manager Is Slow<a name="oracle-backup-slow"></a>

Creating backups with Oracle Recovery Manager can be slow if Oracle Recovery Manager pauses for 120 seconds before starting a backup job\.

**Action to Take**

If you encounter this issue, disable Oracle Direct NFS, as described in [Enabling and Disabling Direct NFS Client Control of NFS](https://docs.oracle.com/database/122/HPDBI/enabling-and-disabling-direct-nfs-client-control-of-nfs.htm#HPDBI-GUID-27DDB55B-F79E-4F40-8228-5D94456E620B) in the Oracle Help Center\.

**Note**  
Amazon EFS doesn't support Oracle Direct NFS\.

## File Operation Errors<a name="troubleshooting-efs-fileop-errors"></a>

When you access Amazon EFS file systems, certain limits on the files in the file system apply\. Exceeding these limits causes file operation errors\. For more information on client and file\-based limits in Amazon EFS, see [Limits for Client EC2 Instances ](limits.md#limits-client-specific)\. Following, you can find some common file operation errors and the limits associated with each error\.

### Command Fails with “Disk quota exceeded” Error<a name="diskquotaerror"></a>

 Amazon EFS doesn't currently support user disk quotas\. This error can occur if any of the following limits have been exceeded:

+ Up to 128 active user accounts can have files open at once for an instance\.

+ Up to 32,768 files can be open at once for an instance\.

+ Each unique mount on the instance can acquire up to a total of 8,192 locks across 256 unique file\-process pairs\. For example, a single process can acquire one or more locks on 256 separate files, or eight processes can each acquire one or more locks on 32 files\.

**Action to Take**  
If you encounter this issue, you can resolve it by identifying which of the preceding limits you are exceeding, and then making changes to meet that limit\.

### Command Fails with "I/O error"<a name="ioerror"></a>

 This error occurs when more than 128 active user accounts for each instance have files open at once\.

**Action to Take**  
If you encounter this issue, you can resolve it by meeting the supported limit of open files on your instances\. To do so, reduce the number of active users that have files from your Amazon EFS file system open simultaneously on your instances\.

### Command Fails with "File name is too long" Error<a name="filenametoolong"></a>

This error occurs when the size of a file name or its symbolic link \(symlink\) is too long\. File names have the following limits:

+ A name can be up to 255 bytes long\.

+ A symlink can be up to 4080 bytes in size\.

**Action to Take**  
If you encounter this issue, you can resolve it by reducing the size of your file name or symlink length to meet the supported limits\.

### Command Fails with "Too many links" Error<a name="hardlinkerror"></a>

This error occurs when there are too many hard links to a file\. You can have up to 177 hard links in a file\.

**Action to Take**  
If you encounter this issue, you can resolve it by reducing the number of hard links to a file to meet the supported limit\.

### Command Fails with "File too large" Error<a name="filesizeerror"></a>

This error occurs when a file is too large\. A single file can be up to 52,673,613,135,872 bytes \(47\.9 TiB\) in size\.

**Action to Take**  
If you encounter this issue, you can resolve it by reducing the size of a file to meet the supported limit\.

### Command Fails with "Try again" Error<a name="toomanyuserserror"></a>

This error occurs when too many users or applications try to access a single file\. When an application or user accesses a file, a lock is placed on the file\. Any one particular file can have up to 87 locks among all users and applications of the file system\. You can mount a file system on one or more Amazon EC2 instances, but the 87\-lock limit for a file still applies\.

**Action to Take**  
If you encounter this issue, you can resolve it by reducing the number of applications or users accessing the file until that number meets the number of allowed locks or lower\.

## Troubleshooting AMI and Kernel Issues<a name="troubleshooting-efs-ami-kernel"></a>

Following, you can find information about troubleshooting issues related to certain Amazon Machine Image \(AMI\) or kernel versions when using Amazon EFS from an Amazon EC2 instance\.


+ [Unable to chown](#chown-kernal)
+ [File System Keeps Performing Operations Repeatedly Due to Client Bug](#file-system-stuck-client-bug)
+ [Deadlocked Client](#deadlocked-client)
+ [Listing Files in a Large Directory Takes a Long Time](#long-time-listing)

### Unable to chown<a name="chown-kernal"></a>

You're unable to change the ownership of a file/directory using the Linux `chown` command\.

**Kernel Versions with This Bug**  
2\.6\.32

**Action to Take**

You can resolve this error by doing the following: 

+ If you're performing `chown` for the one\-time setup step necessary to change ownership of the EFS root directory, you can run the `chown` command from an instance running a newer kernel\. For example, use the newest version of Amazon Linux\.

+ If `chown` is part of your production workflow, you must update the kernel version to use `chown`\.

### File System Keeps Performing Operations Repeatedly Due to Client Bug<a name="file-system-stuck-client-bug"></a>

A file system gets stuck performing repeated operations due to a client bug\.

**Action to Take**  
Update the client software to the latest version \(currently, **Linux kernel version 4\.1**\)\. 

### Deadlocked Client<a name="deadlocked-client"></a>

A client becomes deadlocked\.

**Kernel Versions with This Bug**

+ CentOS\-7 with kernel Linux 3\.10\.0\-229\.20\.1\.el7\.x86\_64

+ Ubuntu 15\.10 with kernel Linux 4\.2\.0\-18\-generic

**Action to Take**  
Do one of the following:

+ Upgrade to a newer kernel version\. For CentOS\-7, kernel version **Linux 3\.10\.0\-327** or later contains the fix\.

+ Downgrade to an older kernel version\.

### Listing Files in a Large Directory Takes a Long Time<a name="long-time-listing"></a>

This can happen if the directory is changing while your NFS client iterates through the directory to finish the list operation\. Whenever the NFS client notices that the contents of the directory changed during this iteration, the NFS client restarts iterating from the beginning\. As a result, the ls command can take a long time to complete for a large directory with frequently changing files\.

**Kernel Versions with This Bug**  
CentOS kernel versions lower than 2\.6\.32\-696\.1\.1\.el6

**Action to Take**  
To resolve this issue, upgrade to a newer kernel version\.

## Troubleshooting Encrypted File Systems<a name="troubleshooting-efs-encryption"></a>

### Encrypted File System Can't Be Created<a name="unable-to-encrypt"></a>

You've tried to create a new encrypted file system\. However, you get an error message saying that AWS KMS is unavailable\.

**Action to Take**  
This error can occur in the rare case that AWS KMS becomes temporarily unavailable in your region\. If this happens, wait until AWS KMS returns to full availability, and then try again to create the file system\.

### Unusable Encrypted File System<a name="unusable-encrypt"></a>

An encrypted file system consistently returns NFS server errors\. These errors can occur when EFS can't retrieve your master key from AWS KMS for one of the following reasons:

+ The key was disabled\.

+ The key was deleted\.

+ Permission for Amazon EFS to use the key was revoked\.

+ AWS KMS is temporarily unavailable\.

**Action to Take**  
First, confirm that the AWS KMS key is enabled\. You can do so by viewing the keys in the console\. For more information, see [Viewing Keys](http://docs.aws.amazon.com/kms/latest/developerguide/viewing-keys.html) in the *AWS Key Management Service Developer Guide*\.

If the key is not enabled, enable it\. For more information, see [Enabling and Disabling Keys](http://docs.aws.amazon.com/kms/latest/developerguide/enabling-keys.html) in the *AWS Key Management Service Developer Guide*\.

If the key is pending deletion, then this status disables the key\. You can cancel the deletion, and re\-enable the key\. For more information, see [Scheduling and Canceling Key Deletion](http://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html#deleting-keys-scheduling-key-deletion) in the *AWS Key Management Service Developer Guide*\.

If the key is enabled, and you're still experiencing an issue, or if you encounter an issue re\-enabling your key, contact AWS Support\.