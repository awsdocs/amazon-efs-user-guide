# Step 1: Create Amazon EC2 Resources<a name="wt1-create-ec2-resources"></a>

In this step, you do the following: 
+ Create two security groups\.
+  Add rules to the security groups to authorize additional access\.
+ Launch an EC2 instance\. You create and mount an Amazon EFS file system on this instance in the next step\. 

**Topics**
+ [Step 1\.1: Create Two Security Groups](#wt1-create-sg)
+ [Step 1\.2: Add Rules to the Security Groups to Authorize Inbound/Outbound Access](#wt1-update-sg)
+ [Step 1\.3: Launch an EC2 Instance](#wt1-create-ec2-instance)

## Step 1\.1: Create Two Security Groups<a name="wt1-create-sg"></a>

In this section, you create security groups in your VPC for your EC2 instance and Amazon EFS mount target\. Later in the walkthrough, you assign these security groups to an EC2 instance and an Amazon EFS mount target\. For information about security groups, see [Security Groups for EC2\-VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#vpc-security-groups) in the *Amazon EC2 User Guide for Linux Instances*\. 

**To create security groups**

1. Create two security groups using the `create-security-group` CLI command:

   1. Create a security group \(`efs-walkthrough1-ec2-sg`\) for your EC2 instance, and provide your VPC ID\.

      ```
      $ aws ec2 create-security-group \
      --region us-west-2 \
      --group-name efs-walkthrough1-ec2-sg \
      --description "Amazon EFS walkthrough 1, SG for EC2 instance" \
      --vpc-id vpc-id-in-us-west-2 \
      --profile adminuser
      ```

      Write down the security group ID\. The following is an example response\.

      ```
      {
          "GroupId": "sg-aexample"
      }
      ```

      You can find the VPC ID using the following command\.

      ```
      $ aws  ec2 describe-vpcs    
      ```

   1. Create a security group \(`efs-walkthrough1-mt-sg`\) for your Amazon EFS mount target\. You need to provide your VPC ID\.

      ```
      $ aws ec2 create-security-group \
      --region us-west-2 \
      --group-name efs-walkthrough1-mt-sg \
      --description "Amazon EFS walkthrough 1, SG for mount target" \
      --vpc-id vpc-id-in-us-west-2 \
      --profile adminuser
      ```

      Write down the security group ID\. The following is an example response\.

      ```
      {
          "GroupId": "sg-aexample"
      }
      ```

1. Verify the security groups\.

   ```
   aws ec2 describe-security-groups \
   --group-ids list of security group IDs separated by space \
   --profile adminuser \
   --region us-west-2
   ```

   Both should have only one outbound rule that allows all traffic to leave\.

   In the next section, you authorize additional access that enables the following: 
   + Enable you to connect to your EC2 instance\. 
   + Enable traffic between an EC2 instance and an Amazon EFS mount target \(with which you associate these security groups later in this walkthrough\)\.

## Step 1\.2: Add Rules to the Security Groups to Authorize Inbound/Outbound Access<a name="wt1-update-sg"></a>

In this step, you add rules to the security groups to authorize inbound/outbound access\.

**To add rules**

1. Authorize incoming Secure Shell \(SSH\) connections to the security group for your EC2 instance \(`efs-walkthrough1-ec2-sg`\) so you can connect to your EC2 instance using SSH from any host\.

   ```
   $ aws ec2 authorize-security-group-ingress \
   --group-id id of the security group created for EC2 instance \
   --protocol tcp \
   --port 22 \
   --cidr 0.0.0.0/0 \
   --profile adminuser \
   --region us-west-2
   ```

   Verify that the security group has the inbound and outbound rule you added\. 

   ```
   aws ec2 describe-security-groups \
   --region us-west-2 \
   --profile adminuser \
   --group-id security-group-id
   ```

1. Authorize inbound access to the security group for the Amazon EFS mount target \(`efs-walkthrough1-mt-sg`\)\.

   At the command prompt, run the following AWS CLI `authorize-security-group-ingress` command using the adminuser profile to add the inbound rule\.

   ```
   $ aws ec2 authorize-security-group-ingress \
   --group-id ID of the security group created for Amazon EFS mount target \
   --protocol tcp \
   --port 2049 \
   --source-group ID of the security group created for EC2 instance \
   --profile adminuser \
   --region us-west-2
   ```

1. Verify that both security groups now authorize inbound access\.

   ```
   aws ec2 describe-security-groups \
   --group-names efs-walkthrough1-ec2-sg   efs-walkthrough1-mt-sg \
   --profile adminuser \
   --region us-west-2
   ```

## Step 1\.3: Launch an EC2 Instance<a name="wt1-create-ec2-instance"></a>

In this step, you launch an EC2 instance\. 

**To launch an EC2 instance**

1. Gather the following information that you need to provide when launching an EC2 instance:
   + Key pair name:
     + For introductory information, see [Setting Up with Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances*\.
     + For instructions to create a \.pem file, see [Create a Key Pair](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/get-set-up-for-amazon-ec2.html#create-a-key-pair) in the *Amazon EC2 User Guide for Linux Instances*\.
   + The ID of the Amazon Machine Image \(AMI\) you want to launch\. 

     The AWS CLI command that you use to launch an EC2 instance requires the ID of the AMI that you want to deploy as a parameter\. The exercise uses the Amazon Linux HVM AMI\.
**Note**  
You can use most general purpose Linux\-based AMIs\. If you use another Linux AMI, make sure that you use your distribution's package manager to install the NFS client on the instance\. Also, you might need to add software packages as you need them\.

     For the Amazon Linux HVM AMI, you can find the latest IDs at [Amazon Linux AMI](https://aws.amazon.com/amazon-linux-ami/)\. You choose the ID value from the Amazon Linux AMI IDs table as follows:
     + Choose the **US West Oregon** region\. This walkthrough assumes you are creating all resources in the US West \(Oregon\) Region \(us\-west\-2\)\.
     + Choose the **EBS\-backed HVM 64\-bit** type \(because in the CLI command you specify the `t2.micro` instance type, which does not support instance store\)\.
   + ID of the security group you created for an EC2 instance\. 
   + AWS Region\. This walkthrough uses the us\-west\-2 region\.
   + Your VPC subnet ID where you want to launch the instance\. You can get list of subnets using the `describe-subnets` command\. 

     ```
     $ aws ec2 describe-subnets \
     --region us-west-2 \
     --filters "Name=vpc-id,Values=vpc-id" \
     --profile adminuser
     ```

     After you choose the subnet ID, write down the following values from the `describe-subnets` result:
     + **Subnet ID** – You need this value when you create a mount target\. In this exercise, you create a mount target in the same subnet where you launch an EC2 instance\. 
     + **Availability Zone of the subnet** – You need this value to construct your mount target DNS name, which you use to mount a file system on the EC2 instance\. 

1. Run the following AWS CLI `run-instances` command to launch an EC2 instance\.

   ```
   $ aws ec2 run-instances \
   --image-id AMI ID \
   --count 1 \
   --instance-type t2.micro \
   --associate-public-ip-address \
   --key-name key-pair-name \
   --security-group-ids ID of the security group created for EC2 instance \
   --subnet-id VPC subnet ID \
   --region us-west-2 \
   --profile adminuser
   ```

1. Write down the instance ID returned by the `run-instances` command\.

1. The EC2 instance you created must have a public DNS name that you use to connect to the EC2 instance and mount the file system on it\. The public DNS name is of the form:

   ```
   ec2-xx-xx-xx-xxx.compute-1.amazonaws.com
   ```

   Run the following CLI command and write down the public DNS name\. 

   ```
   aws ec2 describe-instances \
   --instance-ids EC2 instance ID \
   --region us-west-2 \ 
   --profile adminuser
   ```

   If you don't find the public DNS name, check the configuration of the VPC in which you launched the EC2 instance\. For more information, see [Before You Begin](wt1-getting-started.md#wt1-prepare)\.

1. \(Optional\) Assign a name to the EC2 instance that you created\. To do so, add a tag with the key name and value set to the name that you want to assign to the instance\. You do this by running the following AWS CLI `create-tags` command\. 

   ```
   $  aws ec2 create-tags \
   --resources  EC2-instance-ID \
   --tags Key=Name,Value=Provide-instance-name  \
   --region us-west-2 \
   --profile adminuser
   ```

**Next Step**  
[Step 2: Create Amazon EFS Resources](wt1-create-efs-resources.md) 