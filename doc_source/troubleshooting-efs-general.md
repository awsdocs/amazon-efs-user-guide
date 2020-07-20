# Troubleshooting Amazon EFS: General Issues<a name="troubleshooting-efs-general"></a>

Use this information to troubleshoot general Amazon EFS issues\. For information about performance, see [Amazon EFS Performance](performance.md)\.

In general, if you encounter issues with Amazon EFS that you have trouble resolving, confirm that you're using a recent Linux kernel\. If you are using an enterprise Linux distribution, we recommend the following:
+ Amazon Linux 2
+ Amazon Linux 2015\.09 or newer
+ RHEL 7\.3 or newer
+ All versions of Ubuntu 16\.04
+ Ubuntu 14\.04 with kernel 3\.13\.0\-83 or newer
+ SLES 12 Sp2 or later

If you are using another distribution or a custom kernel, we recommend kernel version 4\.3 or newer\.

**Note**  
RHEL 6\.9 might be suboptimal for certain workloads due to [Poor Performance When Opening Many Files in Parallel](#open-close-operations-serialized)\.

**Topics**
+ [Amazon EC2 Instance Hangs](#ec2-instance-hangs)
+ [Application Writing Large Amounts of Data Hangs](#application-large-data-hangs)
+ [Poor Performance When Opening Many Files in Parallel](#open-close-operations-serialized)
+ [Custom NFS Settings Causing Write Delays](#custom-nfs-settings-write-delays)
+ [Creating Backups with Oracle Recovery Manager Is Slow](#oracle-backup-slow)

## Amazon EC2 Instance Hangs<a name="ec2-instance-hangs"></a>

An Amazon EC2 instance can hang because you deleted a file system mount target without first unmounting the file system\. 

**Action to Take**  
Before you delete a file system mount target, unmount the file system\. For more information about unmounting your Amazon EFS file system, see [Unmounting file systems](mounting-fs-mount-cmd-general.md#unmounting-fs)\.

## Application Writing Large Amounts of Data Hangs<a name="application-large-data-hangs"></a>

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

## Poor Performance When Opening Many Files in Parallel<a name="open-close-operations-serialized"></a>

Applications that open multiple files in parallel do not experience the expected increase in performance of I/O parallelization\.

**Action to Take**

This issue occurs on Network File System version 4 \(NFSv4\) clients and on RHEL 6 clients using NFSv4\.1 because these NFS clients serialize NFS OPEN and CLOSE operations\. Use NFS protocol version 4\.1 and one of the suggested [Linux distributions](#recommend.linux.dist) that does not have this issue\.

If you can't use NFSv4\.1, be aware that the Linux NFSv4\.0 client serializes open and close requests by user ID and group IDs\. This serialization happens even if multiple processes or multiple threads issue requests at the same time\. The client only sends one open or close operation to an NFS server at a time, when all of the IDs match\. To work around these issues, you can perform any of the following actions:
+ You can run each process from a different user ID on the same Amazon EC2 instance\.
+ You can leave the user IDs the same across all open requests, and modify the set of group IDs instead\.
+ You can run each process from a separate Amazon EC2 instance\.

## Custom NFS Settings Causing Write Delays<a name="custom-nfs-settings-write-delays"></a>

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

## Creating Backups with Oracle Recovery Manager Is Slow<a name="oracle-backup-slow"></a>

Creating backups with Oracle Recovery Manager can be slow if Oracle Recovery Manager pauses for 120 seconds before starting a backup job\.

**Action to Take**

If you encounter this issue, disable Oracle Direct NFS, as described in [Enabling and Disabling Direct NFS Client Control of NFS](https://docs.oracle.com/database/122/HPDBI/enabling-and-disabling-direct-nfs-client-control-of-nfs.htm#HPDBI-GUID-27DDB55B-F79E-4F40-8228-5D94456E620B) in the Oracle Help Center\.

**Note**  
Amazon EFS doesn't support Oracle Direct NFS\.