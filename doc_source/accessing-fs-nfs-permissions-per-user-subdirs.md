# Walkthrough: Create Writable Per\-User Subdirectories and Configure Automatic Remounting on Reboot<a name="accessing-fs-nfs-permissions-per-user-subdirs"></a>

After you create an Amazon EFS file system and mount it locally on your EC2 instance, it exposes an empty directory called the *file system root*\. One common use case is to create a "writable" subdirectory under this file system root for each user you create on the EC2 instance, and mount it on the user's home directory\. All files and subdirectories the user creates in their home directory are then created on the Amazon EFS file system\. 

In this walkthrough, you first create a user "mike" on your EC2 instance\. You then mount an Amazon EFS subdirectory onto user mike's home directory\. The walkthrough also explains how to configure automatic remounting of subdirectories if the system reboots\.

Suppose you have an Amazon EFS file system created and mounted on a local directory on your EC2 instance\. Let's call it *EFSroot*\. 

**Note**  
You can follow the [Getting Started](getting-started.md) exercise to create and mount an Amazon EFS file system on your EC2 instance\.

In the following steps, you create a user \(mike\), create a subdirectory for the user \(*EFSroot*`/mike`\), make user mike the owner of the subdirectory, granting him full permissions, and finally mount the Amazon EFS subdirectory on the user's home directory \(`/home/mike`\)\.

1. Create user mike:

   1. Log in to your EC2 instance\. Using root privileges \(in this case, using the `sudo` command\), create user `mike` and assign a password\. 

     ```
     $ sudo useradd -c "Mike Smith" mike
     $ sudo passwd mike
     ```

     This also creates a home directory, /home/mike, for the user\.

1. Create a subdirectory under *EFSroot* for user `mike`:

   1. Create subdirectory `mike` under *EFSroot*\.

      ```
      $  sudo mkdir /EFSroot/mike
      ```

      You will need to replace *EFSroot* with your local directory name\.

   1. The root user and root group are the owners of the `/mike` subdirectory \(you can verify this by using the `ls -l` command\)\. To enable full permissions for user `mike` on this subdirectory, grant `mike` ownership of the directory\.

      ```
      $ sudo chown mike:mike /EFSroot/mike 
      ```  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/nfs-perm-30.png)

1. Use the `mount` command to mount the *EFSroot*/mike subdirectory onto mike's home directory\.

   ```
   $  sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport mount-target-DNS:/mike  /home/mike
   ```

   The *mount\-target\-DNS* address identifies the remote Amazon EFS file system root\. 

Now user mike's home directory is a subdirectory, writable by mike, in the Amazon EFS file system\. If you unmount this mount target, the user can't access their EFS directory without remounting, which requires root permissions\. 

## Automatic Remounting on Reboot<a name="accessing-fs-nfs-permissions-per-user-subdirs-auto-mount-on-reboot"></a>

 You can use the file `fstab` to automatically remount your file system after any system reboots\. For more information, see [Mounting your Amazon EFS file system automatically](mount-fs-auto-mount-onreboot.md)\. 