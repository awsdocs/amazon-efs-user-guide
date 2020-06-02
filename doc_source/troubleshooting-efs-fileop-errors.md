# Troubleshooting File Operation Errors<a name="troubleshooting-efs-fileop-errors"></a>

When you access Amazon EFS file systems, certain limits on the files in the file system apply\. Exceeding these limits causes file operation errors\. For more information on client and file\-based limits in Amazon EFS, see [Quotas for NFS Clients](limits.md#limits-client-specific)\. Following, you can find some common file operation errors and the limits associated with each error\.

**Topics**
+ [Command Fails with “Disk quota exceeded” Error](#diskquotaerror)
+ [Command Fails with "I/O error"](#ioerror)
+ [Command Fails with "File name is too long" Error](#filenametoolong)
+ [Command Fails with "File not found" Error](#filenotfound)
+ [Command Fails with "Too many links" Error](#hardlinkerror)
+ [Command Fails with "File too large" Error](#filesizeerror)

## Command Fails with “Disk quota exceeded” Error<a name="diskquotaerror"></a>

 Amazon EFS doesn't currently support user disk quotas\. This error can occur if any of the following limits have been exceeded:
+ Up to 128 active user accounts can have files open at once for an instance\.
+ Up to 32,768 files can be open at once for an instance\.
+ Each unique mount on the instance can acquire up to a total of 8,192 locks across 256 unique file\-process pairs\. For example, a single process can acquire one or more locks on 256 separate files, or eight processes can each acquire one or more locks on 32 files\.

**Action to Take**  
If you encounter this issue, you can resolve it by identifying which of the preceding limits you are exceeding, and then making changes to meet that limit\.

## Command Fails with "I/O error"<a name="ioerror"></a>

This error occurs when you encounter one of the following issues:
+ More than 128 active user accounts for each instance have files open at once\.

**Action to Take**  
If you encounter this issue, you can resolve it by meeting the supported limit of open files on your instances\. To do so, reduce the number of active users that have files from your Amazon EFS file system open simultaneously on your instances\.
+ The AWS KMS key encrypting your file system was deleted\.

**Action to Take**  
If you encounter this issue, you can no longer decrypt the data that was encrypted under that key, which means that data becomes unrecoverable\.

## Command Fails with "File name is too long" Error<a name="filenametoolong"></a>

This error occurs when the size of a file name or its symbolic link \(symlink\) is too long\. File names have the following limits:
+ A name can be up to 255 bytes long\.
+ A symlink can be up to 4080 bytes in size\.

**Action to Take**  
If you encounter this issue, you can resolve it by reducing the size of your file name or symlink length to meet the supported limits\.

## Command Fails with "File not found" Error<a name="filenotfound"></a>

This error occurs because some older 32\-bit versions of Oracle E\-Business suite use 32\-bit file I/O interfaces, and EFS uses 64\-bit inode numbers\. System calls that may fail include `stat\(\)` and `readdir\(\)`\.

**Action to Take**  
If you encounter this error, you can resolve it by using the nfs\.enable\_ino64=0 kernel boot option\. This option compresses the 64\-bit EFS inode numbers to 32 bits\. Kernel boot options are handled differently for different Linux distributions\. On Amazon Linux, turn on this option by adding `nfs.enable_ino64=0 kernel` to the `GRUB_CMDLINE_LINUX_DEFAULT` variable in `/etc/default/grub`\. Please consult your distribution for specific documentation on how to turn on kernel boot options\.

## Command Fails with "Too many links" Error<a name="hardlinkerror"></a>

This error occurs when there are too many hard links to a file\. You can have up to 177 hard links in a file\.

**Action to Take**  
If you encounter this issue, you can resolve it by reducing the number of hard links to a file to meet the supported limit\.

## Command Fails with "File too large" Error<a name="filesizeerror"></a>

This error occurs when a file is too large\. A single file can be up to 52,673,613,135,872 bytes \(47\.9 TiB\) in size\.

**Action to Take**  
If you encounter this issue, you can resolve it by reducing the size of a file to meet the supported limit\.