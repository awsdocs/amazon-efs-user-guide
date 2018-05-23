# Network File System \(NFS\)–Level Users, Groups, and Permissions<a name="accessing-fs-nfs-permissions"></a>

**Topics**
+ [Example Amazon EFS File System Use Cases and Permissions](#accessing-fs-nfs-permissions-ex-scenarios)
+ [User and group ID permissions on files and directories within a file system](#accessing-fs-nfs-permissions-uid-gid)
+ [No Root Squashing](#accessing-fs-nfs-permissions-root-user)
+ [Permissions Caching](#accessing-fs-nfs-permissions-caching)
+ [Changing File System Object Ownership](#accessing-fs-nfs-permissions-chown-restricted)

After creating a file system, by default, only the root user \(UID 0\) has read\-write\-execute permissions\. In order for other users to modify the file system, the root user must explicitly grant them access\.

Amazon EFS file system objects have a Unix\-style mode associated with them\. This value defines the permissions for performing actions on that object, and users familiar with Unix\-style systems can easily understand how Amazon EFS behaves with respect to these permissions\.

Additionally, on Unix\-style systems, users and groups are mapped to numeric identifiers, which Amazon EFS uses to represent file ownership\. File system objects \(that is, files, directories, etc\.\) on Amazon EFS are owned by a single owner and a single group\. Amazon EFS uses these numeric IDs to check permissions when a user attempts to access a file system object\. 

This section provides examples of permissions and discusses Amazon EFS–specific NFS permissions considerations\. 

## Example Amazon EFS File System Use Cases and Permissions<a name="accessing-fs-nfs-permissions-ex-scenarios"></a>

After you create an Amazon EFS file system and mount targets for the file system in your VPC, you can mount the remote file system locally on your Amazon EC2 instance\. The `mount` command can mount any directory in the file system\. However, when you first create the file system, there is only one root directory at `/`\. 

The following `mount` command mounts the root directory of an Amazon EFS file system, identified by the file system DNS name, on the `/efs-mount-point` local directory\.

```
sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport file-system-id.efs.aws-region.amazonaws.com:/ efs-mount-point
```

 Note that the root user and root group own the mounted directory\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/nfs-perm-10.png)

The initial permissions mode allows: 
+ `read-write-execute` permissions to the owner *root*
+ `read-execute` permissions to the group *root* 
+ `read-execute` permissions to others

Note that only the root user can modify this directory\. The root user can also grant other users permissions to write to this directory\. For example:
+ Create writable per\-user subdirectories\. For step\-by\-step instructions, see [Walkthrough 3: Create Writable Per\-User Subdirectories and Configure Automatic Remounting on Reboot](accessing-fs-nfs-permissions-per-user-subdirs.md)\.
+ Allow users to write to the Amazon EFS file system root\. A user with root privileges can grant other users access to the file system\. 
  + To change the Amazon EFS file system ownership to a non\-*root* user and group, use the following:

    ```
    $ sudo chown user:group /EFSroot
    ```
  + To change permissions of the file system to something more permissive, use the following:

    ```
    $ sudo chmod 777 /EFSroot
    ```

    This command grants read\-write\-execute privileges to all users on all EC2 instances that have the file system mounted\.

## User and group ID permissions on files and directories within a file system<a name="accessing-fs-nfs-permissions-uid-gid"></a>

Files and directories in an Amazon EFS file system support standard Unix\-style read/write/execute permissions based on the user ID and group ID asserted by the mounting NFSv4\.1 client\. When a user attempts to access files and directories, Amazon EFS checks their user ID and group IDs to verify the user has permission to access the objects\. Amazon EFS also uses these IDs as the owner and group owner for new files and directories the user creates\. Amazon EFS does not examine user or group names—it only uses the numeric identifiers\.

**Note**  
When you create a user on an EC2 instance, you can assign any numeric UID and GID to the user\. The numeric user IDs are set in the `/etc/passwd` file on Linux systems\. The numeric group IDs are in the `/etc/group` file\. These files define the mappings between names and IDs\. Outside of the EC2 instance, Amazon EFS does not perform any authentication of these IDs, including the root ID of 0\.

If a user accesses an Amazon EFS file system from two different EC2 instances, depending on whether the UID for the user is the same or different on those instances, you see different behavior as follows:
+ If the user IDs are the same on both EC2 instances, Amazon EFS considers them to be the same user, regardless of the EC2 instance they use\. The user experience when accessing the file system is the same from both EC2 instances\.
+ If the user IDs are not the same on both EC2 instances, Amazon EFS considers them to be different users, and the user experience will not be the same when accessing the Amazon EFS file system from the two different EC2 instances\.
+ If two different users on different EC2 instances share an ID, Amazon EFS considers them the same user\. 

You might consider managing user ID mappings across EC2 instances consistently\. Users can check their numeric ID using the `id` command, as shown following:

```
$ id 

uid=502(joe) gid=502(joe) groups=502(joe)
```

### Turn Off the ID Mapper<a name="accessing-fs-nfs-permissions-id-mapper"></a>

The NFS utilities in the operating system include a daemon called an ID Mapper that manages mapping between user names and IDs\. In Amazon Linux, the daemon is called `rpc.idmapd` and on Ubuntu is called `idmapd`\. It translates user and group IDs into names, and vice versa\. However, Amazon EFS deals only with numeric IDs\. We recommend you turn this process off on your EC2 instances \(on Amazon Linux the mapper is usually disabled, in which case don't enable the ID mapper\), as shown following:

```
$  service rpcidmapd status
$  sudo service rpcidmapd stop
```

## No Root Squashing<a name="accessing-fs-nfs-permissions-root-user"></a>

When root squashing is enabled, the root user is converted to a user with limited permissions on the NFS server\.

Amazon EFS behaves like a Linux NFS server with `no_root_squash`\. If a user or group ID is 0, Amazon EFS treats that user as the `root` user, and bypasses permissions checks \(allowing access and modification to all file system objects\)\.

## Permissions Caching<a name="accessing-fs-nfs-permissions-caching"></a>

Amazon EFS caches file permissions for a small time period\. As a result, there may be a brief window where a user who had access to a file system object but the access was revoked recently can still access that object\. 

## Changing File System Object Ownership<a name="accessing-fs-nfs-permissions-chown-restricted"></a>

Amazon EFS enforces the POSIX `chown_restricted` attribute\. This means only the root user can change the owner of a file system object\. While the root or the owner user can change the owner group of a file system object, unless the user is root, the group can only be changed to one that the owner user is a member of\. 