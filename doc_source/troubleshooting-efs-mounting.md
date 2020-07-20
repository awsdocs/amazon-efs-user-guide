# Troubleshooting Mount Issues<a name="troubleshooting-efs-mounting"></a>

**Topics**
+ [File System Mount on Windows Instance Fails](#mount-windows-instance-fails)
+ [Access Denied by Server](#mount-fail-access-denied-by-server)
+ [Automatic Mounting Fails and the Instance Is Unresponsive](#automount-fails)
+ [Mounting Multiple Amazon EFS File Systems in /etc/fstab Fails](#automount-fix-multiple-fs)
+ [Mount Command Fails with "wrong fs type" Error Message](#mount-error-wrong-fs)
+ [Mount Command Fails with "incorrect mount option" Error Message](#mount-error-incorrect-mount)
+ [File System Mount Fails Immediately After File System Creation](#mount-fails-propegation)
+ [File System Mount Hangs and Then Fails with Timeout Error](#mount-hangs-fails-timeout)
+ [File System Mount Using DNS Name Fails](#mount-fails-dns-name)
+ [File System Mount Fails with "nfs not responding"](#tcp-reconnect-nfs-not-responding)
+ [Mount Target Lifecycle State Is Stuck](#mount-target-lifecycle-stuck)
+ [Mount Does Not Respond](#mount-unresponsive)
+ [Operations on Newly Mounted File System Return "bad file handle" Error](#operations-return-bad-file-handle)
+ [Unmounting a File System Fails](#troubleshooting-unmounting)

## File System Mount on Windows Instance Fails<a name="mount-windows-instance-fails"></a>

A file system mount on an Amazon EC2 instance on Microsoft Windows fails\.

**Action to Take**  
Don't use Amazon EFS with Windows EC2 instances, which isn't supported\.

## Access Denied by Server<a name="mount-fail-access-denied-by-server"></a>

A file system mount fails with the following message:

```
/efs mount.nfs4: access denied by server while mounting 127.0.0.1:/
```

This issue can occur if your NFS client does not have permission to mount the file system\. 

**Action to Take**  
 If you are attempting to mount the file system using IAM, make sure you are using the `-o iam` option in your mount command\. This tells the EFS mount helper to pass your credentials to the EFS mount target\. If you still don't have access, check your file system policy and your identity policy to ensure there are no DENY clauses that apply to your connection, and that there is at least one ALLOW clause that applies to the connection\. 

## Automatic Mounting Fails and the Instance Is Unresponsive<a name="automount-fails"></a>

This issue can occur if the file system was mounted automatically on an instance and the `_netdev` option wasn't declared\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\.

**Action to Take**  
If this issue occurs, contact AWS Support\.

## Mounting Multiple Amazon EFS File Systems in /etc/fstab Fails<a name="automount-fix-multiple-fs"></a>

For instances that use the systemd init system with two or more Amazon EFS entries at `/etc/fstab`, there might be times where some or all of these entries are not mounted\. In this case, the `dmesg` output shows one or more lines similar to the following\.

```
NFS: nfs4_discover_server_trunking unhandled error -512. Exiting with error EIO
```

**Action to Take**  
In this case, we recommend that you create a new systemd service file in `/etc/systemd/system/mount-nfs-sequentially.service` with the following contents\.

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

Then restart your Amazon EC2 instance\. The file systems are mounted on demand, generally within a second\.

## Mount Command Fails with "wrong fs type" Error Message<a name="mount-error-wrong-fs"></a>

The mount command fails with the following error message\.

```
mount: wrong fs type, bad option, bad superblock on 10.1.25.30:/, 
missing codepage or helper program, or other error (for several filesystems 
(e.g. nfs, cifs) you might need a /sbin/mount.<type> helper program)
In some cases useful info is found in syslog - try dmesg | tail or so.
```

**Action to Take**  
If you receive this message, install the `nfs-utils` \(or `nfs-common` on Ubuntu\) package\. For more information, see [Installing the NFS Client](mounting-fs-old.md#mounting-fs-install-nfsclient)\.

## Mount Command Fails with "incorrect mount option" Error Message<a name="mount-error-incorrect-mount"></a>

The mount command fails with the following error message\.

```
mount.nfs: an incorrect mount option was specified
```

**Action to Take**  
This error message most likely means that your Linux distribution doesn't support Network File System versions 4\.0 and 4\.1 \(NFSv4\)\. To confirm this is the case, you can run the following command\.

```
$ grep CONFIG_NFS_V4_1 /boot/config*
```

If the preceding command returns `# CONFIG_NFS_V4_1 is not set`, NFSv4\.1 is not supported on your Linux distribution\. For a list of the Amazon Machine Images \(AMIs\) for Amazon Elastic Compute Cloud \(Amazon EC2\) that support NFSv4\.1, see [NFS Support](mounting-fs-old.md#mounting-fs-nfs-info)\. 

## File System Mount Fails Immediately After File System Creation<a name="mount-fails-propegation"></a>

It can take up to 90 seconds after creating a mount target for the Domain Name Service \(DNS\) records to propagate fully in an AWS Region\.

**Action to Take**  
If you're programmatically creating and mounting file systems, for example with an AWS CloudFormation template, we recommend that you implement a wait condition\.

## File System Mount Hangs and Then Fails with Timeout Error<a name="mount-hangs-fails-timeout"></a>

The file system mount command hangs for a minute or two, and then fails with a timeout error\. The following code shows an example\.

```
$ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport mount-target-ip:/ mnt

[2+ minute wait here]
mount.nfs: Connection timed out
$Â 
```

**Action to Take**

This error can occur because either the Amazon EC2 instance or the mount target security groups are not configured properly\. Make sure that the mount target security group has an inbound rule that allows NFS access from the EC2 security group\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/mnt-tgt-sg-inbound-rules.png)

For more information, see [Creating security groups](accessing-fs-create-security-groups.md)\.

Verify that the mount target IP address that you specified is valid\. If you specify the wrong IP address and there is nothing else at that IP address to reject the mount, you might experience this issue\.

## File System Mount Using DNS Name Fails<a name="mount-fails-dns-name"></a>

A file system mount that is using a DNS name fails\. The following code shows an example\.

```
$ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport file-system-id.efs.aws-region.amazonaws.com:/ mnt   
mount.nfs: Failed to resolve server file-system-id.efs.aws-region.amazonaws.com: 
  Name or service not known.   

$ 
```

**Action to Take**

Check your VPC configuration\. If you are using a custom VPC, make sure that DNS settings are enabled\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the *Amazon VPC User Guide*\. 

To specify a DNS name in the `mount` command, you must do the following:
+ Ensure that there's an Amazon EFS mount target in the same Availability Zone as the Amazon EC2 instance\.
+ Ensure that there's a mount target in the same VPC as the Amazon EC2 instance\. Otherwise, you can't use DNS name resolution for EFS mount targets that are in another VPC\. For more information, see [Mounting EFS file systems from another account or VPC](manage-fs-access-vpc-peering.md)\.
+ Connect your Amazon EC2 instance inside an Amazon VPC configured to use the DNS server provided by Amazon\. For more information, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.
+ Ensure that the Amazon VPC of the connecting Amazon EC2 instance has DNS hostnames enabled\. For more information, see [Updating DNS Support for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-updating) in the *Amazon VPC User Guide*\.

## File System Mount Fails with "nfs not responding"<a name="tcp-reconnect-nfs-not-responding"></a>

An Amazon EFS file system mount fails on a Transmission Control Protocol \(TCP\) reconnection event with `"nfs: server_name still not responding"`\.

**Action to Take**

Use the `noresvport` mount option to make sure that the NFS client uses a new TCP source port when a network connection is reestablished\. Doing this helps ensure uninterrupted availability after a network recovery event\.

## Mount Target Lifecycle State Is Stuck<a name="mount-target-lifecycle-stuck"></a>

The mount target lifecycle state is stuck in the **creating** or **deleting** state\.

**Action to Take**  
Retry the `CreateMountTarget` or `DeleteMountTarget` call\.

## Mount Does Not Respond<a name="mount-unresponsive"></a>

An Amazon EFS mount appears unresponsive\. For example, commands like `ls` hang\.

**Action to Take**

This error can occur if another application is writing large amounts of data to the file system\. Access to the files that are being written might be blocked until the operation is complete\. In general, any commands or applications that attempt to access files that are being written to might appear to hang\. For example, the `ls` command might hang when it gets to the file that is being written\. This result is because some Linux distributions alias the `ls` command so that it retrieves file attributes in addition to listing the directory contents\.

To resolve this issue, verify that another application is writing files to the Amazon EFS mount, and that it is in the `Uninterruptible sleep` \(`D`\) state, as in the following example:

```
$ ps aux | grep large_io.py 
root 33253 0.5 0.0 126652 5020 pts/3 D+ 18:22 0:00 python large_io.py /efs/large_file
```

After you've verified that this is the case, you can address the issue by waiting for the other write operation to complete, or by implementing a workaround\. In the example of `ls`, you can use the `/bin/ls` command directly, instead of an alias\. Doing this allows the command to proceed without hanging on the file being written\. In general, if the application writing the data can force a data flush periodically, perhaps by using `fsync(2)`, doing so can help improve the responsiveness of your file system for other applications\. However, this improvement might be at the expense of performance when the application writes data\.

## Operations on Newly Mounted File System Return "bad file handle" Error<a name="operations-return-bad-file-handle"></a>

Operations performed on a newly mounted file system return a `bad file handle` error\. 

This error can happen if an Amazon EC2 instance was connected to one file system and one mount target with a specified IP address, and then that file system and mount target were deleted\. If you create a new file system and mount target to connect to that Amazon EC2 instance with the same mount target IP address, this issue can occur\. 

**Action to Take**  
You can resolve this error by unmounting the file system, and then remounting the file system on the Amazon EC2 instance\. For more information about unmounting your Amazon EFS file system, see [Unmounting file systems](mounting-fs-mount-cmd-general.md#unmounting-fs)\.

## Unmounting a File System Fails<a name="troubleshooting-unmounting"></a>

If your file system is busy, you can't unmount it\.

**Action to Take**  
You can resolve this issue in the following ways:
+ Wait for all read and write operations to finish, and then attempt the `umount` command again\.
+ Force the `umount` command to finish with the `-f` option\.
**Warning**  
Forcing an unmount interrupts any data read or write operations that are currently in process for the file system\.