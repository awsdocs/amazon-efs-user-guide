# Step 3: Connect to Your Amazon EC2 Instance and Mount the Amazon EFS File System<a name="gs-step-three-connect-to-ec2-instance"></a>

You can connect to your Amazon EC2 instance from a computer running Windows or Linux\. To connect to your Amazon EC2 instance and mount the Amazon EFS file system, you need the **File system ID** value for the mount target for your Amazon EFS file system\. You made a note of this value at the end of [Step 2: Create Your Amazon EFS File System](gs-step-two-create-efs-resources.md)\.

**To connect to your Amazon EC2 instance and mount the Amazon EFS file system**

1. Connect to your Amazon EC2 instance\. For more information, see [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) or [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. After you've connected, install the amazon\-efs\-utils package, which has the Amazon EFS mount helper\.

   Run the following command to install amazon\-efs\-utils\.

   ```
   sudo yum install -y amazon-efs-utils
   ```
**Note**  
For more information on the amazon\-efs\-utils package, including installation instructions for other Linux distributions, see [Using the amazon\-efs\-utils Tools](using-amazon-efs-utils.md)\.

1. Make a directory for the mount point with the following command\.

   ```
   $ sudo mkdir /mnt/efs
   ```

1. Mount the Amazon EFS file system to the directory that you created\. Use the following command and replace `file-system-id` with your **File System ID** value\.

   ```
   sudo mount -t efs fs-12345678:/ /mnt/efs
   ```
**Note**  
We recommend that you wait 90 seconds after creating a mount target before you mount your file system\.

1. Change directories to the new directory that you created with the following command\.

   ```
   $ cd /mnt/efs
   ```

1. Make a subdirectory and change the ownership of that subdirectory to your EC2 instance user\. Then navigate to that new directory with the following commands\.

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

**Note**  
You can configure the EFS file system to mount on your EC2 instance automatically\. For more information, see [Configuring an EFS File System to Mount Automatically at EC2 Instance Launch](mount-fs-auto-mount-onreboot.md#mount-fs-auto-mount-on-creation)\.