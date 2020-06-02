# Troubleshooting AMI and Kernel Issues<a name="troubleshooting-efs-ami-kernel"></a>

Following, you can find information about troubleshooting issues related to certain Amazon Machine Image \(AMI\) or kernel versions when using Amazon EFS from an Amazon EC2 instance\.

**Topics**
+ [Unable to chown](#chown-kernal)
+ [File System Keeps Performing Operations Repeatedly Due to Client Bug](#file-system-stuck-client-bug)
+ [Deadlocked Client](#deadlocked-client)
+ [Listing Files in a Large Directory Takes a Long Time](#long-time-listing)

## Unable to chown<a name="chown-kernal"></a>

You're unable to change the ownership of a file/directory using the Linux `chown` command\.

**Kernel Versions with This Bug**  
2\.6\.32

**Action to Take**

You can resolve this error by doing the following: 
+ If you're performing `chown` for the one\-time setup step necessary to change ownership of the EFS root directory, you can run the `chown` command from an instance running a newer kernel\. For example, use the newest version of Amazon Linux\.
+ If `chown` is part of your production workflow, you must update the kernel version to use `chown`\.

## File System Keeps Performing Operations Repeatedly Due to Client Bug<a name="file-system-stuck-client-bug"></a>

A file system gets stuck performing repeated operations due to a client bug\.

**Action to Take**  
Update the client software to the latest version\.

## Deadlocked Client<a name="deadlocked-client"></a>

A client becomes deadlocked\.

**Kernel Versions with This Bug**
+ CentOS\-7 with kernel Linux 3\.10\.0\-229\.20\.1\.el7\.x86\_64
+ Ubuntu 15\.10 with kernel Linux 4\.2\.0\-18\-generic

**Action to Take**  
Do one of the following:
+ Upgrade to a newer kernel version\. For CentOS\-7, kernel version **Linux 3\.10\.0\-327** or later contains the fix\.
+ Downgrade to an older kernel version\.

## Listing Files in a Large Directory Takes a Long Time<a name="long-time-listing"></a>

This can happen if the directory is changing while your NFS client iterates through the directory to finish the list operation\. Whenever the NFS client notices that the contents of the directory changed during this iteration, the NFS client restarts iterating from the beginning\. As a result, the ls command can take a long time to complete for a large directory with frequently changing files\.

**Kernel Versions with This Bug**  
CentOS and RHEL kernel versions lower than 2\.6\.32\-696\.el6

**Action to Take**  
To resolve this issue, upgrade to a newer kernel version\.