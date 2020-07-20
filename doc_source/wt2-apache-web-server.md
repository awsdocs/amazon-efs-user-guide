# Walkthrough: Set Up an Apache Web Server and Serve Amazon EFS Files<a name="wt2-apache-web-server"></a>

You can have EC2 instances running the Apache web server serving files stored on your Amazon EFS file system\. It can be one EC2 instance, or if your application needs, you can have multiple EC2 instances serving files from your Amazon EFS file system\. The following procedures are described\.
+ [Set up an Apache web server on an EC2 instance](#wt2-apache-web-server-one-ec2-host)\.
+ [ Set up an Apache web server on multiple EC2 instances by creating an Auto Scaling group](#wt2-apache-web-server-auto-scale-group)\. You can create multiple EC2 instances using Amazon EC2 Auto Scaling, an AWS service that allows you to increase or decrease the number of EC2 instances in a group according to your application needs\. When you have multiple web servers, you also need a load balancer to distribute request traffic among them\. 

**Note**  
For both procedures, you create all resources in the US West \(Oregon\) Region \(`us-west-2`\)\. 

## Single EC2 Instance Serving Files<a name="wt2-apache-web-server-one-ec2-host"></a>

Follow the steps to set up an Apache web server on one EC2 instance to serve files you create in your Amazon EFS file system\.

1. Follow the steps in the Getting Started exercise so that you have a working configuration consisting of the following:
   + Amazon EFS file system
   + EC2 instance
   + File system mounted on the EC2 instance

   For instructions, see [Getting Started with Amazon Elastic File System](getting-started.md)\. As you follow the steps, write down the following:
   + Public DNS name of the EC2 instance\.
   + Public DNS name of the mount target created in the same Availability Zone where you launched the EC2 instance\.

1. \(Optional\) You may choose to unmount the file system from the mount point you created in the Getting Started exercise\. 

   ```
   $ sudo umount  ~/efs-mount-point
   ```

   In this walkthrough, you create another mount point for the file system\.

1. On your EC2 instance, install the Apache web server and configure it as follows:

   1. Connect to your EC2 instance and install the Apache web server\.

      ```
      $ sudo yum -y install httpd
      ```

   1. Start the service\.

      ```
      $ sudo service httpd start  
      ```

   1. Create a mount point\.

      First note that the `DocumentRoot` in the `/etc/httpd/conf/httpd.conf` file points to `/var/www/html` \(`DocumentRoot "/var/www/html"`\)\.

      You will mount your Amazon EFS file system on a subdirectory under the document root\. 

      1. Create a subdirectory efs\-mount\-point under /var/www/html\.

         ```
         $ sudo mkdir /var/www/html/efs-mount-point
         ```

      1. Mount your Amazon EFS file system\. You need to update the following mount command using the EFS mount helper utility by providing your file system ID\.

         ```
         $ sudo mount -t efs fs-12345678:/ /var/www/html/efs-mount-point 
         ```

1. Test the setup\.

   1. Add a rule in the EC2 instance security group, which you created in the Getting Started exercise, to allow HTTP traffic on TCP port 80 from anywhere\. 

      After you add the rule, the EC2 instance security group will have the following inbound rules\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/wh2-part1-10.png)

      For instructions, see [Creating security groups using the AWS Management Console](accessing-fs-create-security-groups.md#create-security-groups-console)\. 

   1. Create a sample html file\.

      1. Change directory\.

         ```
         $ cd /var/www/html/efs-mount-point 
         ```

      1.  Make a subdirectory for `sampledir` and change the ownership\. And change directory so you can create files in the `sampledir` subdirectory\. 

         ```
         $  sudo mkdir sampledir  
         $  sudo chown  ec2-user sampledir
         $  sudo chmod -R o+r sampledir
         $  cd sampledir
         ```

      1. Create a sample `hello.html` file\.

         ```
         $ echo "<html><h1>Hello from Amazon EFS</h1></html>" > hello.html    
         ```

   1. Open a browser window and enter the URL to access the file \(it is the public DNS name of the EC2 instance followed by the file name\)\. For example:

      ```
      http://EC2-instance-public-DNS/efs-mount-point/sampledir/hello.html
      ```

   Now you are serving web pages stored on an Amazon EFS file system\.

**Note**  
This setup does not configure the EC2 instance to automatically start httpd \(web server\) on boot, and also does not mount the file system on boot\. In the next walkthrough, you create a launch configuration to set this up\.

## Multiple EC2 Instances Serving Files<a name="wt2-apache-web-server-auto-scale-group"></a>

Follow the steps to serve the same content in your Amazon EFS file system from multiple EC2 instances for improved scalability or availability\.

1. Follow the steps in the [Getting Started](getting-started.md) exercise so that you have an Amazon EFS file system created and tested\.
**Important**  
For this walkthrough, you don't use the EC2 instance that you created in the Getting Started exercise\. Instead, you launch new EC2 instances\. 

1. Create a load balancer in your VPC using the following steps\.

   1. Define a load balancer

      In the **Basic Configuration** section, select your VPC where you also create the EC2 instances on which you mount the file system\.

      In the **Select Subnets** section, select all of the available subnets \. For details, see the `cloud-config` script in the next section\.

   1. Assign security groups

      Create a new security group for the load balancer to allow HTTP access from port 80 from anywhere, as shown following:
      + Type: HTTP
      + Protocol: TCP
      + Port Range: 80
      + Source: Anywhere \(0\.0\.0\.0/0\)
**Note**  
When everything works, you can also update the EC2 instance security group inbound rule access to allow HTTP traffic only from the load balancer\. 

   1. Configure a health check

      Set the **Ping Path** value to `/efs-mount-point/test.html`\. The `efs-mount-point` is the subdirectory where you have the file system mounted\. You add `test.html` page in it later in this procedure\.
**Note**  
Don't add any EC2 instances\. Later, you create an Auto Scaling Group in which you launch EC2 instance and specify this load balancer\.

   For instructions to create a load balancer, see [Getting Started with Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/elb-getting-started.html) in the *Elastic Load Balancing User Guide*\. 

1. Create an Auto Scaling group with two EC2 instances\. First, you create a launch configuration describing the instances\. Then, you create an Auto Scaling group by specifying the launch configuration\. The following steps provide configuration information that you specify to create an Auto Scaling group from the Amazon EC2 console\.

   1. Choose **Launch Configurations** under **AUTO SCALING** from the left hand navigation\.

   1. Choose **Create Auto Scaling group** to launch the wizard\.

   1. Choose **Create launch configuration**\.

   1. From **Quick Start**, select the latest version of the **Amazon Linux \(HVM\)** AMI\. This is same AMI you used in [Step 2: Create Your EC2 Resources and Launch Your EC2 Instance](gs-step-one-create-ec2-resources.md) of the Getting Started exercise\.

   1. In the **Advanced** section, do the following:
      + For **IP Address Type**, choose **Assign a public IP address to every instance**\. 
      + Copy/paste the following script in the **User data** box\. 

        You must update the script by providing values for the *file\-system\-id* and *aws\-region* \(if you followed the Getting Started exercise, you created the file system in the us\-west\-2 region\)\. 

        In the script, note the following:
        + The script installs the NFS client and the Apache web server\.
        + The echo command writes the following entry in the `/etc/fstab` file identifying the file system's DNS name and subdirectory on which to mount it\. This entry ensures that the file gets mounted after each system reboot\. Note that the file system's DNS name is dynamically constructed\. For more information, see [Mounting on Amazon EC2 with a DNS Name](mounting-fs-mount-cmd-dns-name.md)\. 

          ```
          file-system-ID.efs.aws-region.amazonaws.com:/ /var/www/html/efs-mount-point   nfs4   defaults
          ```
        + Creates efs\-mount\-point subdirectory and mounts the file system on it\.
        + Creates a test\.html page so ELB health check can find the file \(when creating a load balancer you specified this file as the ping point\)\.

        For more information about user data scripts, see [Adding User Data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html#instancedata-add-user-data) in the *Amazon EC2 User Guide for Linux Instances*\.

        ```
        #cloud-config
        package_upgrade: true
        packages:
        - nfs-utils
        - httpd
        runcmd:
        - echo "$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone).file-system-id.efs.aws-region.amazonaws.com:/    /var/www/html/efs-mount-point   nfs4    defaults" >> /etc/fstab
        - mkdir /var/www/html/efs-mount-point
        - mount -a
        - touch /var/www/html/efs-mount-point/test.html
        - service httpd start
        - chkconfig httpd on
        ```

   1. For **Assign a security group**, choose **Select an existing security group**, and then choose the security group you created for the EC2 instance\.

   When configuring the Auto Scaling group details, use the following information:

   1. For **Group size**, choose **Start with 2 instances**\. You will create two EC2 instances\.

   1. Select your VPC from the **Network** list\.

   1. Select a subnet in the same Availability Zone that you used when specifying the mount target ID in the User Data script when creating the launch configuration in the preceding step\.

   1. In the Advanced Details section

      1. For **Load Balancing**, choose **Receive traffic from Elastic Load Balancer\(s\)**, and then select the load balancer you created for this exercise\.

      1. For **Health Check Type**, choose **ELB**\.

   Follow the instructions to create an Auto Scaling group at [Set Up a Scaled and Load\-Balanced Application](https://docs.aws.amazon.com/autoscaling/latest/userguide/as-register-lbs-with-asg.html) in the *Amazon EC2 Auto Scaling User Guide*\. Use the information in the preceding tables where applicable\.

1. Upon successful creation of the Auto Scaling group, you have two EC2 instances with `nfs-utils` and the Apache web server installed\. On each instance, verify that you have the `/var/www/html/efs-mount-point` subdirectory with your Amazon EFS file system mounted on it\. For instructions to connect to an EC2 instance, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.
**Note**  
If you choose the **Amazon Linux AMI 2016\.03\.0** Amazon Linux AMI when launching your Amazon EC2 instance, you won't need to install `nfs-utils` because it is already included in the AMI by default\.

1. Create a sample page \(index\.html\)\. 

   1. Change directory\.

      ```
      $ cd /var/www/html/efs-mount-point 
      ```

   1.  Make a subdirectory for `sampledir` and change the ownership\. And change directory so you can create files in the `sampledir` subdirectory\. If you followed the preceding [Single EC2 Instance Serving Files](#wt2-apache-web-server-one-ec2-host), you already created the `sampledir` subdirectory, so you can skip this step\.

      ```
      $  sudo mkdir sampledir  
      $  sudo chown  ec2-user sampledir
      $  sudo chmod -R o+r sampledir
      $  cd sampledir
      ```

   1. Create a sample `index.html` file\.

      ```
      $ echo "<html><h1>Hello from Amazon EFS</h1></html>" > index.html    
      ```

1. Now you can test the setup\. Using the load balancer's public DNS name, access the index\.html page\.

   ```
   http://load balancer public DNS Name/efs-mount-point/sampledir/index.html
   ```

   The load balancer sends a request to one of the EC2 instances running the Apache web server\. Then, the web server serves the file that is stored in your Amazon EFS file system\. 