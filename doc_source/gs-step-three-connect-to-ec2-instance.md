# Step 3: Connect to Your Amazon EC2 Instance and Mount the Amazon EFS File System<a name="gs-step-three-connect-to-ec2-instance"></a>

You can connect to your Amazon EC2 instance from a computer running Windows or Linux\. To connect to your Amazon EC2 instance and mount the Amazon EFS file system, you need the following information:

+ The **Public DNS** name of the Amazon EC2 instance\. You made a note of this value at the end of [Step 1: Create Your EC2 Resources and Launch Your EC2 Instance](gs-step-one-create-ec2-resources.md)\.

+ The **File system ID** value for the mount target for your Amazon EFS file system\. You made a note of this value at the end of [Step 2: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md)\.

**To connect to your Amazon EC2 instance and mount the Amazon EFS file system**

1. Connect to your Amazon EC2 instance\. For more information, see [Connecting to Your Linux Instance from Windows Using PuTTY](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) or [Connecting to Your Linux Instance Using SSH](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. After you've connected, install the Network File System \(NFS\) client\.

   If you're using an Amazon Linux AMI or RedHat Linux AMI, install the NFS client with the following command\.

   ```
   $ sudo yum -y install nfs-utils
   ```

   If you're using an Ubuntu AMI, install the NFS client with the following command\.

   ```
   $ sudo apt-get -y install nfs-common
   ```

1. Make a directory for the mount point with the following command\.

   ```
   $ sudo mkdir efs
   ```

1. Mount the Amazon EFS file system to the directory that you created\. Use the following command and replace the *file\-system\-id* and *aws\-region* placeholders with your **File System ID** value and AWS Region, respectively\.

   ```
   $ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 file-system-id.efs.aws-region.amazonaws.com:/ efs
   ```
**Note**  
We recommend that you wait 90 seconds after creating a mount target before you mount the file system, as the DNS records propagate fully in the region\.

1. Change directories to the new directory that you created with the following command\.

   ```
   $ cd efs
   ```

1. Make a subdirectory and change the ownership of that subdirectory to your EC2 instance user\. Then, navigate to that new directory with the following commands\.

   ```
   $ sudo mkdir getting-started
   $ sudo chown ec2-user getting-started
   $ cd getting-started
   ```

1. Create a text file with the following command\.

   ```
   $ touch test-file.txt
   ```

1. List the directory contents with the following command\.

   ```
   $ ls -al
   ```

As a result, the following file is created\.

```
-rw-rw-r-- 1 ec2-user ec2-user 0 Aug 15 15:32 test-file.txt
```