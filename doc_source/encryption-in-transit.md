# Encrypting Data in Transit<a name="encryption-in-transit"></a>

You can encrypt data in transit using an Amazon EFS file system, without needing to modify your applications\.

## Encrypting Data in Transit with TLS<a name="encrypt-mount"></a>

Enabling encryption of data in transit for your Amazon EFS file system is done by enabling Transport Layer Security \(TLS\) when you mount your file system using the Amazon EFS mount helper\. For more information, see [Mounting with the EFS mount helper](mounting-fs.md#mounting-fs-mount-helper)\.

When encryption of data in transit is declared as a mount option for your Amazon EFS file system, the mount helper initializes a client stunnel process\. Stunnel is an open source multipurpose network relay\. The client stunnel process listens on a local port for inbound traffic, and the mount helper redirects Network File System \(NFS\) client traffic to this local port\. The mount helper uses TLS version 1\.2 to communicate with your file system\.

**To mount your Amazon EFS file system with the mount helper with encryption of data in transit enabled**

1. Access the terminal for your instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Run the following command to mount your file system\.

   ```
   sudo mount -t efs  -o tls fs-12345678:/ /mnt/efs
   ```

## How Encrypting in Transit Works<a name="how-encrypt-transit"></a>

To enable encryption of data in transit, you connect to Amazon EFS using TLS\. We recommend using the mount helper because it's the simplest option\.

If you're not using the mount helper, you can still enable encryption of data in transit\. At a high level, the following are the steps to do so\.

**To enable encryption of data in transit without the mount helper**

1. Download and install stunnel, and note the port that the application is listening on\.

1. Run stunnel to connect to your Amazon EFS file system on port 2049 using TLS\.

1. Using the NFS client, mount `localhost:port`, where `port` is the port that you noted in the first step\.

Because encryption of data in transit is configured on a per\-connection basis, each configured mount has a dedicated stunnel process running on the instance\. By default, the stunnel process used by the mount helper listens on local ports 20049 and 20449, and it connects to Amazon EFS on port 2049\.

**Note**  
By default, when using the Amazon EFS mount helper with TLS, the mount helper enforces certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux versions, mounting an Amazon EFS file system using TLS fails\.  
After you've installed the amazon\-efs\-utils package, to upgrade your system's version of stunnel see [Upgrading Stunnel](using-amazon-efs-utils.md#upgrading-stunnel)\.  
For issues with encryption, see [Troubleshooting Encryption](troubleshooting-efs-encryption.md)\.

When using encryption of data in transit, your NFS client setup is changed\. When you inspect your actively mounted file systems, you see one mounted to 127\.0\.0\.1, or localhost, as in the following example\.

```
$ mount | column -t
127.0.0.1:/  on  /home/ec2-user/efs        type  nfs4         (rw,relatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,port=20127,timeo=600,retrans=2,sec=sys,clientaddr=127.0.0.1,local_lock=none,addr=127.0.0.1)
```

When mounting with TLS and the Amazon EFS mount helper, you are reconfiguring your NFS client to mount to a local port\. The mount helper starts a client stunnel process that is listening on this local port, and stunnel is opening an encrypted connection to EFS using TLS\. The EFS mount helper is responsible for setting up and maintaining this encrypted connection and associated configuration\.

To determine which Amazon EFS file system ID corresponds to which local mount point, you can use the following command\. Replace `efs-mount-point` with the local path where you mounted your file system\.

```
grep -E "Successfully mounted.*efs-mount-point" /var/log/amazon/efs/mount.log | tail -1
```

When you use the mount helper for encryption of data in transit, it also creates a process called `amazon-efs-mount-watchdog`\. This process ensures that each mount's stunnel process is running, and stops the stunnel when the Amazon EFS file system is unmounted\. If for some reason a stunnel process is terminated unexpectedly, the watchdog process restarts it\.