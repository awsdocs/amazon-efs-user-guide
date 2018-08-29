# Additional Mounting Considerations<a name="mounting-fs-mount-cmd-general"></a>

When mounting your Amazon EFS file system on an Amazon EC2 instance, note the following additional considerations:
+ We recommend the following default Linux mount option values: 

  ```
  rsize=1048576
  wsize=1048576
  hard
  timeo=600
  retrans=2
  noresvport
  _netdev
  ```
+ If you must change the IO size parameters \(`rsize` and `wsize`\), we recommend that you use the largest size possible \(up to `1048576`\) to avoid diminished performance\.
+ If you must change the timeout parameter \(`timeo`\), we recommend that you use a value of at least `150`, which is equivalent to 15 seconds\. The timeo parameter is in deciseconds, so 15 seconds is equal to 150 deciseconds\.
+ We recommend that you use the hard mount option\. However, if you use a soft mount, you need to set the `timeo` parameter to at least `150` deciseconds\.
+ With the `noresvport` option, the NFS client uses a new Transmission Control Protocol \(TCP\) source port when a network connection is reestablished\. Doing this helps ensure uninterrupted availability after a network recovery event\.
+ With the `_netdev` option prevents the system from attempting to mount the file system until the network has been enabled on the system.
+ Avoid setting any other mount options that are different from the defaults\. For example, changing read or write buffer sizes, or disabling attribute caching can result in reduced performance\.
+ Amazon EFS ignores source ports\. If you change Amazon EFS source ports, it doesn't have any effect\.
+ Amazon EFS does not support any of the Kerberos security variants\.  For example, the following will cause a mount to fail:

  ```
   $ mount -t nfs4 -o krb5p <DNS_NAME>:/ /efs/ 
  ```
+ We recommend that you mount your file system using its DNS name, which will resolve to the IP address of the Amazon EFS mount target in the same Availability Zone as your Amazon EC2 instance\. If you use a mount target in a different Availability Zone as your Amazon EC2 instance, you will incur the standard Amazon EC2 data transfer charges for data sent across Availability Zones, and you may see increased latencies for file system operations\.
+ For more mount options, and detailed explanations of the defaults, refer to the `man fstab` and `man nfs` pages\.

## Unmounting File Systems<a name="unmounting-fs"></a>

Before you delete a file system, we recommend that you unmount it from every Amazon EC2 instance that it's connected to\. You can unmount a file system on your Amazon EC2 instance by running the `umount` command on the instance itself\. You can't unmount an Amazon EFS file system through the AWS CLI, the AWS Management Console, or through any of the AWS SDKs\. To unmount an Amazon EFS file system connected to an Amazon EC2 instance running Linux, use the `umount` command as follows:

```
umount /mnt/efs 
```

We recommend that you do not specify any other `umount` options\. Avoid setting any other `umount` options that are different from the defaults\.

You can verify that your Amazon EFS file system has been unmounted by running the `df` command to display the disk usage statistics for the file systems currently mounted on your Linux\-based Amazon EC2 instance\. If the Amazon EFS file system that you want to unmount isn’t listed in the `df` command output, this means that the file system is unmounted\.

**Example Example: Identify the Mount Status of an Amazon EFS File System and Unmount It**  

```
$ df -T
Filesystem Type 1K-blocks Used Available Use% Mounted on 
/dev/sda1 ext4 8123812 1138920 6884644 15% / 
availability-zone.file-system-id.efs.aws-region.amazonaws.com :/ nfs4 9007199254740992 0 9007199254740992 0% /mnt/efs
```

```
$ umount /mnt/efs
```

```
$ df -T 
```

```
Filesystem Type 1K-blocks Used Available Use% Mounted on 
/dev/sda1 ext4 8123812 1138920 6884644 15% /
```
