# Mounting on Amazon EC2 with a DNS Name<a name="mounting-fs-mount-cmd-dns-name"></a>

You can mount an Amazon EFS file system on an Amazon EC2 instance using DNS names\. You can do this with a DNS name for the file system, or a DNS name for a mount target\.

+ **File system DNS name** – Using the file system's DNS name is your simplest mounting option\. The file system DNS name will automatically resolve to the mount target’s IP address in the Availability Zone of the connecting Amazon EC2 instance\. You can get this DNS name from the console, or if you have the file system ID, you can construct it using the following convention:

  ```
  file-system-id.efs.aws-region.amazonaws.com
  ```

  Using the file system DNS name, you can mount a file system on your Amazon EC2 instance with the following command:

  ```
  sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 file-system-id.efs.aws-region.amazonaws.com:/ efs-mount-point
  ```

+ **Mount target DNS name** – In December 2016, we introduced file system DNS names\. We continue to provide a DNS name for each Availability Zone mount target for backward compatibility\. If you delete a mount target and then create a new one in the same Availability Zone, the DNS name for that new mount target in that Availability Zone remains the same as the DNS name for the old mount target\. The generic form of a mount target DNS name is as follows:

  ```
  availability-zone.file-system-id.efs.aws-region.amazonaws.com 
  ```

For a list of regions that support Amazon EFS, see [Amazon Elastic File System ](http://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem-region) in the *AWS General Reference*\.

To be able to use a DNS name in the `mount` command, the following must be true:

+ The connecting EC2 instance must be inside a VPC and must be configured to use the DNS server provided by Amazon\. For information about Amazon DNS server, see [DHCP Options Sets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\. 

+ The VPC of the connecting EC2 instance must have DNS host names enabled\. For more information, see [Viewing DNS Hostnames for Your EC2 Instance](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-dns.html#vpc-dns-viewing) in the *Amazon VPC User Guide*\. 

**Note**  
We recommend that you wait 90 seconds after creating a mount target before you mount the file system, as the DNS records propagate fully in the region\.

## Mounting on On\-Premises Servers with a DNS Name<a name="dns-mounting-locally"></a>

Although you can mount your file system on your on\-premises server through AWS Direct Connect with a DNS name, we recommend using IP addresses for simplicity\. To use DNS names, you need to integrate your DNS services in your Amazon VPC with your on\-premises DNS domains\. Specifically, you need to update your on\-premises DNS server to forward the DNS requests for Amazon EFS mount targets to a DNS server in the Amazon VPC over the AWS Direct Connect connection\. For more information, see [How to Set Up DNS Resolution Between On\-Premises Networks and AWS Using AWS Directory Service and Amazon Route 53](https://aws.amazon.com/blogs/security/how-to-set-up-dns-resolution-between-on-premises-networks-and-aws-using-aws-directory-service-and-amazon-route-53/), in the AWS Security Blog\.