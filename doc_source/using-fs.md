# Using File Systems<a name="using-fs"></a>

Amazon Elastic File System presents a standard file system interface that support full file system access semantics\. Using NFSv4\.1, you can mount your Amazon EFS file system on any Amazon Elastic Compute Cloud \(Amazon EC2\) Linux\-based instance\. Once mounted, you can work with the files and directories just like you would with a local file system\. For more information on mounting, see [Mounting File Systems](mounting-fs.md)\.

After you create a file system and mount it on your EC2 instance, there are a few things you need to know in order to use it effectively:
+ **Users, groups, and related NFS\-Level permissions management** – When you first create the file system, there is only one root directory at `/`\. By default, only the root user \(UID 0\) has read\-write\-execute permissions\. In order for other users to modify the file system, the root user must explicitly grant them access\. For more information, see [Network File System \(NFS\)–Level Users, Groups, and Permissions](accessing-fs-nfs-permissions.md)\.

## Related Topics<a name="using-fs-related-topics"></a>

[Amazon EFS: How It Works](how-it-works.md)

[Getting Started](getting-started.md)

[Walkthroughs](walkthroughs.md)