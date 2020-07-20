# Using file systems in Amazon EFS<a name="using-fs"></a>

Amazon Elastic File System presents a standard file\-system interface that supports full file\-system access semantics\. Using Network File System \(NFS\) version 4\.1 \(NFSv4\.1\), you can mount your Amazon EFS file system on any Amazon Elastic Compute Cloud \(Amazon EC2\) Linux\-based instance\. After your system is mounted, you can work with the files and directories just as you do with a local file system\. For more information on mounting, see [Mounting EFS file systems](mounting-fs.md)\.

After you create a file system and mount it on your EC2 instance, to use your file system effectively you need to know about managing NFS\-level permissions for users, groups, and related resources\. When you first create your file system, there is only one root directory at `/`\. By default, only the root user \(UID 0\) has read\-write\-execute permissions\. For other users to modify the file system, the root user must explicitly grant them access\. You use EFS access points to provision directories that are writable from a specific application\. For more information, see [Working with Users, Groups, and Permissions at the Network File System \(NFS\) Level](accessing-fs-nfs-permissions.md) and [Working with Amazon EFS Access Points](efs-access-points.md)\.

## Related topics<a name="using-fs-related-topics"></a>

[Amazon EFS: How It Works](how-it-works.md)

[Getting Started](getting-started.md)

[Walkthroughs](walkthroughs.md)