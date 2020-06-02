# Step 3: Mount the Amazon EFS File System on the EC2 Instance and Test<a name="wt1-test"></a>

In this step, you do the following:

**Topics**
+ [Step 3\.1: Gather Information](#wt1-connect-test-gather-info)
+ [Step 3\.2: Install the NFS Client on Your EC2 Instance](#wt1-connect-install-nfs-client)
+ [Step 3\.3: Mount File System on Your EC2 Instance and Test](#wt1-mount-fs-and-test)

## Step 3\.1: Gather Information<a name="wt1-connect-test-gather-info"></a>

Make sure you have the following information as you follow the steps in this section:
+ Public DNS name of your EC2 instance in the following format: 

  ```
  ec2-xx-xxx-xxx-xx.aws-region.compute.amazonaws.com 
  ```
+ DNS name of your file system\. You can construct this DNS name using the following generic form:

  ```
  file-system-id.efs.aws-region.amazonaws.com
  ```

  The EC2 instance on which you mount the file system by using the mount target can resolve the file system's DNS name to the mount target's IP address\.

**Note**  
Amazon EFS doesn't require that your Amazon EC2 instance have either a public IP address or public DNS name\. The requirements listed preceding are just for this walkthrough example to ensure that you can connect by using SSH into the instance from outside the VPC\.

## Step 3\.2: Install the NFS Client on Your EC2 Instance<a name="wt1-connect-install-nfs-client"></a>

You can connect to your EC2 instance from Windows or from a computer running Linux, or macOS X, or any other Unix variant\. 

**To install an NFS client**

1. Connect to your EC2 instance:
   + To connect to your instance from a computer running macOS or Linux, specify the \.pem file for your SSH command with the `-i` option and the path to your private key\.
   + To connect to your instance from a computer running Windows, you can use either MindTerm or PuTTY\. If you plan to use PuTTY, you need to install it and use the following procedure to convert the \.pem file to a \.ppk file\. 

   For more information, see the following topics in the *Amazon EC2 User Guide for Linux Instances*:
   +  [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) 
   +  [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)

1. Execute the following commands on the EC2 instance by using the SSH session:

   1. \(Optional\) Get updates and reboot\.

      ```
      $  sudo yum -y update  
      $  sudo reboot
      ```

      After the reboot, reconnect to your EC2 instance\.

   1. Install the NFS client\.

      ```
      $ sudo yum -y install nfs-utils
      ```
**Note**  
If you choose the **Amazon Linux AMI 2016\.03\.0** Amazon Linux AMI when launching your Amazon EC2 instance, you don't need to install `nfs-utils` because it is already included in the AMI by default\.

## Step 3\.3: Mount File System on Your EC2 Instance and Test<a name="wt1-mount-fs-and-test"></a>

Now you mount the file system on your EC2 instance\. 

1. Make a directory \("efs\-mount\-point"\)\.

   ```
   $ mkdir ~/efs-mount-point 
   ```

1. Mount the Amazon EFS file system\. 

   ```
   $ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport mount-target-DNS:/   ~/efs-mount-point  
   ```

   The EC2 instance can resolve the mount target DNS name to the IP address\. You can optionally specify the IP address of the mount target directly\.

   ```
   $ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport mount-target-ip:/  ~/efs-mount-point
   ```

1. Now that you have the Amazon EFS file system mounted on your EC2 instance, you can create files\.

   1. Change the directory\.

      ```
      $ cd ~/efs-mount-point  
      ```

   1. List the directory contents\. 

      ```
      $ ls -al
      ```

      It should be empty\.

      ```
      drwxr-xr-x 2 root     root     4096 Dec 29 22:33 .
      drwx------ 4 ec2-user ec2-user 4096 Dec 29 22:54 ..
      ```

   1. The root directory of a file system, upon creation, is owned by and is writable by the root user, so you need to change permissions to add files\.

      ```
      $ sudo chmod go+rw .
      ```

      Now, if you try the `ls -al` command you see that the permissions have changed\.

      ```
      drwxrwxrwx 2 root     root     4096 Dec 29 22:33 .
      drwx------ 4 ec2-user ec2-user 4096 Dec 29 22:54 ..
      ```

   1. Create a text file\.

      ```
      $ touch test-file.txt 
      ```

   1. List directory content\. 

      ```
      $ ls -l
      ```

You now have successfully created and mounted an Amazon EFS file system on your EC2 instance in your VPC\.

The file system you mounted doesn't persist across reboots\. To automatically remount the directory, you can use the `fstab` file\. For more information, see [Automatic Remounting on Reboot](accessing-fs-nfs-permissions-per-user-subdirs.md#accessing-fs-nfs-permissions-per-user-subdirs-auto-mount-on-reboot)\. If you are using an Auto Scaling group to launch EC2 instances, you can also set scripts in a launch configuration\. For an example, see [Walkthrough: Set Up an Apache Web Server and Serve Amazon EFS Files](wt2-apache-web-server.md)\.

**Next Step**  
 [Step 4: Clean Up](wt1-clean-up.md) 