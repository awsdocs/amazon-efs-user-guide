# EFS Mount Helper<a name="efs-mount-helper"></a>

The Amazon EFS mount helper simplifies mounting your file systems\. It includes the Amazon EFS recommended mount options by default\. Additionally, the mount helper has built\-in logging for troubleshooting purposes\. If you encounter an issue with your Amazon EFS file system, you can share these logs with AWS Support\. 

## How It Works<a name="efs-mount-helper-how"></a>

The mount helper defines a new network file system type, called `efs`, which is fully compatible with the standard `mount` command in Linux\. The mount helper also supports mounting an Amazon EFS file system at instance boot time automatically by using entries in the `/etc/fstab` configuration file\.

**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting-efs-mounting.md#automount-fails)\.

When encryption of data in transit is declared as a mount option for your Amazon EFS file system, the mount helper initializes a client stunnel process, and a supervisor process called `amazon-efs-mount-watchdog`\. Stunnel is a multipurpose network relay that is open\-source\. The client stunnel process listens on a local port for inbound traffic, and the mount helper redirects NFS client traffic to this local port\. The mount helper uses TLS version 1\.2 to communicate with your file system\.

Using TLS requires certificates, and these certificates are signed by a trusted Amazon Certificate Authority\. For more information on how encryption works, see [Data Encryption in EFS](encryption.md)\.

## Using the EFS Mount Helper<a name="using-efs-mount-helper"></a>

The mount helper helps you mount your EFS file systems on your Linux EC2 instances\. For more information, see [Mounting EFS file systems](mounting-fs.md)\. 

## Getting Support Logs<a name="mount-helper-logs"></a>

The mount helper has built\-in logging for your Amazon EFS file system\. You can share these logs with AWS Support for troubleshooting purposes\. 

You can find the logs stored in `/var/log/amazon/efs` for systems with the mount helper installed\. These logs are for the mount helper, the stunnel process itself, and for the `amazon-efs-mount-watchdog` process that monitors the stunnel process\.

**Note**  
The watchdog process ensures that each mount's stunnel process is running, and stops the stunnel when the Amazon EFS file system is unmounted\. If for some reason a stunnel process is terminated unexpectedly, the watchdog process restarts it\.

You can change the configuration of your logs in `/etc/amazon/efs/efs-utils.conf`\. However, doing so requires unmounting and then remounting the file system with the mount helper for the changes to take effect\. Log capacity for the mount helper and watchdog logs is limited to 20 MiB\. Logs for the stunnel process are disabled by default\.

**Important**  
You can enable logging for the stunnel process logs\. However, enabling the stunnel logs can use up a nontrivial amount of space on your file system\.

## Using amazon\-efs\-utils with AWS Direct Connect and VPN<a name="amazon-efs-utils-direct"></a>

You can mount your Amazon EFS file systems on your on\-premises data center servers when connected to your Amazon VPC with AWS Direct Connect\. Using amazon\-efs\-utils also makes mounting simpler with the mount helper and allows you to enable encryption of data in transit\. To see how to use amazon\-efs\-utils with AWS Direct Connect to mount Amazon EFS file systems onto on\-premises Linux clients, see [Walkthrough: Create and Mount a File System On\-Premises with AWS Direct Connect and VPN](efs-onpremises.md)\.

## Related Topics<a name="amazon-efs-utils-related"></a>

For more information on the Amazon EFS mount helper, see these related topics:
+ [Data Encryption in EFS](encryption.md)
+ [Mounting EFS file systems](mounting-fs.md)