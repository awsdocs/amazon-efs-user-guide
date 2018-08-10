# Troubleshooting Amazon EFS<a name="troubleshooting"></a>

Following, you can find information on how to troubleshoot issues for Amazon Elastic File System \(Amazon EFS\)\. 

**Topics**
+ [Troubleshooting Amazon EFS: General Issues](#troubleshooting-efs-general)
+ [Troubleshooting File Operation Errors](#troubleshooting-efs-fileop-errors)
+ [Troubleshooting AMI and Kernel Issues](#troubleshooting-efs-ami-kernel)
+ [Troubleshooting Mount Issues](troubleshooting-efs-mounting.md)
+ [Troubleshooting Encryption](troubleshooting-efs-encryption.md)

## Troubleshooting Amazon EFS: General Issues<a name="troubleshooting-efs-general"></a>

Following, you can find information about general troubleshooting issues related to Amazon EFS\. For information on performance, see [Amazon EFS Performance](performance.md)\.

In general, if you encounter issues with Amazon EFS that you have trouble resolving, confirm that you're using a recent Linux kernel\. If you are using an enterprise Linux distribution, we recommend the following:
+ Amazon Linux 2015\.09 or newer
+ RHEL 7\.3 or newer
+ RHEL 6\.9 with kernel 2\.6\.32\-696 or newer
+ All versions of Ubuntu 16\.04
+ Ubuntu 14\.04 with kernel 3\.13\.0\-83 or newer
+ SLES 12 Sp2 or later

If you are using another distribution or a custom kernel, we recommend kernel version 4\.3 or newer\.

**Topics**
+ [Amazon EC2 Instance Hangs](#ec2-instance-hangs)
+ [Application Writing Large Amounts of Data Hangs](#application-large-data-hangs)
+ [Open and Close Operations Are Serialized](#open-close-operations-serialized)
+ [Custom NFS Settings Causing Write Delays](#custom-nfs-settings-write-delays)
+ [Creating Backups with Oracle Recovery Manager Is Slow](#oracle-backup-slow)

### Amazon EC2 Instance Hangs<a name="ec2-instance-hangs"></a>

An Amazon EC2 instance can hang because you deleted a file system mount target without first unmounting the file system\. 

**Action to Take**  
Before you delete a file system mount target, unmount the file system\. For more information about unmounting your Amazon EFS file system, see [Unmounting File Systems](mounting-fs-mount-cmd-general.md#unmounting-fs)\.

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

### Open and Close Operations Are Serialized<a name="open-close-operations-serialized"></a>

Open and close operations that are performed on a file system by a user on a single Amazon EC2 instance are serialized\.

**Action to Take**

To resolve this issue, use NFS protocol version 4\.1 and one of the suggested Linux kernels\. By using NFSv4\.1 when mounting your file systems, you enable parallelized open and close operations on files\. We recommend using **Amazon Linux AMI 2016\.03\.0** as the AMI for the Amazon EC2 instance that you mount your file system to\. 

If you can't use NFSv4\.1, be aware that the Linux NFSv4\.0 client serializes open and close requests by user ID and group IDs\. This serialization happens even if multiple processes or multiple threads issue requests at the same time\. The client only sends one open or close operation to an NFS server at a time, when all of the IDs match\.

In addition, you can perform any of the following actions to resolve this issue:
+ You can run each process from a different user ID on the same Amazon EC2 instance\.
+ You can leave the user IDs the same across all open requests, and modify the set of group IDs instead\.
+ You can run each process from a separate Amazon EC2 instance\.

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

## Troubleshooting File Operation Errors<a name="troubleshooting-efs-fileop-errors"></a>

When you access Amazon EFS file systems, certain limits on the files in the file system apply\. Exceeding these limits causes file operation errors\. For more information on client and file\-based limits in Amazon EFS, see [Limits for Client EC2 Instances ](limits.md#limits-client-specific)\. Following, you can find some common file operation errors and the limits associated with each error\.

### Command Fails with “Disk quota exceeded” Error<a name="diskquotaerror"></a>

 Amazon EFS doesn't currently support user disk quotas\. This error can occur if any of the following limits have been exceeded:
+ Up to 128 active user accounts can have files open at once for an instance\.
+ Up to 32,768 files can be open at once for an instance\.
+ Each unique mount on the instance can acquire up to a total of 8,192 locks across 256 unique file\-process pairs\. For example, a single process can acquire one or more locks on 256 separate files, or eight processes can each acquire one or more locks on 32 files\.

**Action to Take**  
If you encounter this issue, you can resolve it by identifying which of the preceding limits you are exceeding, and then making changes to meet that limit\.

### Command Fails with "I/O error"<a name="ioerror"></a>

This error occurs when you encounter one of the following issues:
+ More than 128 active user accounts for each instance have files open at once\.

**Action to Take**  
If you encounter this issue, you can resolve it by meeting the supported limit of open files on your instances\. To do so, reduce the number of active users that have files from your Amazon EFS file system open simultaneously on your instances\.
+ The AWS KMS key encrypting your file system was deleted\.

**Action to Take**  
If you encounter this issue, you can no longer decrypt the data that was encrypted under that key, which means that data becomes unrecoverable\.

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

**Topics**
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
Update the client software to the latest version\.

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