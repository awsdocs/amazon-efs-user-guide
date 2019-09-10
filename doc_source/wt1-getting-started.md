# Walkthrough: Create an Amazon EFS File System and Mount It on an Amazon EC2 Instance Using the AWS CLI<a name="wt1-getting-started"></a>

This walkthrough uses the AWS CLI to explore the Amazon EFS API\. In this walkthrough, you create an Amazon EFS file system, mount it on an Amazon EC2 instance in your VPC, and test the setup\.

**Note**  
This walkthrough is similar to the Getting Started exercise\. In the [Getting Started](getting-started.md) exercise, you use the console to create EC2 and Amazon EFS resources\. In this walkthrough, you use the AWS CLI to do the same—primarily to familiarize yourself with the Amazon EFS API\.

In this walkthrough, you create the following AWS resources in your account:
+ Amazon EC2 resources:
  + Two security groups \(for your EC2 instance and Amazon EFS file system\)\.

    You add rules to these security groups to authorize appropriate inbound/outbound access\. Doing this allows your EC2 instance to connect to the file system through the mount target by using a standard NFSv4\.1 TCP port\.
  + An Amazon EC2 instance in your VPC\. 
+ Amazon EFS resources:
  + A file system\.
  + A mount target for your file system\.

    To mount your file system on an EC2 instance you need to create a mount target in your VPC\. You can create one mount target in each of the Availability Zones in your VPC\. For more information, see [Amazon EFS: How It Works](how-it-works.md)\. 

 Then, you test the file system on your EC2 instance\. The cleanup step at the end of the walkthrough provides information for you to remove these resources\. 

The walkthrough creates all these resources in the US West \(Oregon\) Region \(`us-west-2`\)\. Whichever AWS Region you use, be sure to use it consistently\. All of your resources—your VPC, EC2 resources, and Amazon EFS resources—must be in the same AWS Region\. 

## Before You Begin<a name="wt1-prepare"></a>
+ You can use the root credentials of your AWS account to sign in to the console and try the Getting Started exercise\. However, AWS Identity and Access Management \(IAM\) recommends that you do not use the root credentials of your AWS account\. Instead, create an administrator user in your account and use those credentials to manage resources in your account\. For more information, see [Setting Up](setting-up.md)\.
+ You can use a default VPC or a custom VPC that you have created in your account\. For this walkthrough, the default VPC configuration works\. However, if you use a custom VPC, verify the following:
  + DNS hostnames are enabled\. For more information, see [Updating DNS Support for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-viewing) in the *Amazon VPC User Guide*\. 
  + The Internet gateway is attached to your VPC\. For more information, see [Internet Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.
  + The VPC subnets are configured to request public IP addresses for instances launched in the VPC subnets\. For more information, see [IP Addressing in Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html) in the *Amazon VPC User Guide*\.
  + The VPC route table includes a rule to send all Internet\-bound traffic to the Internet gateway\.
+ You need to set up the AWS CLI and add the adminuser profile\.

## Setting Up the AWS CLI<a name="wt1-setup-awscli"></a>

Use the following instructions to set up the AWS CLI and user profile\. 

**To set up the AWS CLI**

1. Download and configure the AWS CLI\. For instructions, see the following topics in the *AWS Command Line Interface User Guide*\. 

    [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) 

    [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) 

   [Configuring the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)

1. Set profiles\.

    You store user credentials in the AWS CLI `config` file\. The example CLI commands in this walkthrough specify the adminuser profile\. Create the adminuser profile in the `config` file\. You can also set the administrator user profile as the default in the `config` file as shown\. 

   ```
   [profile adminuser]
   aws_access_key_id = admin user access key ID
   aws_secret_access_key = admin user secret access key
   region = us-west-2
   
   [default]
   aws_access_key_id = admin user access key ID
   aws_secret_access_key = admin user secret access key
   region = us-west-2
   ```

   The preceding profile also sets the default AWS Region\. If you don't specify a region in the CLI command, the us\-west\-2 region is assumed\.

1. Verify the setup by entering the following command at the command prompt\. Both of these commands don't provide credentials explicitly, so the credentials of the default profile are used\.
   + Try the help command

     You can also specify the user profile explicitly by adding the `--profile` parameter\.

     ```
     aws help
     ```

     ```
     aws help \
     --profile adminuser
     ```

**Next Step**  
[Step 1: Create Amazon EC2 Resources](wt1-create-ec2-resources.md) 