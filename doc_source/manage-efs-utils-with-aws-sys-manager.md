# Using AWS Systems Manager to automatically install or update Amazon EFS clients<a name="manage-efs-utils-with-aws-sys-manager"></a>

 You can use AWS Systems Manager to simplify the management of Amazon EFS clients \(amazon\-efs\-utils\)\. AWS Systems Manager is an AWS service that you can use to view and control your infrastructure on AWS\. With AWS Systems Manager you can automate the tasks required to install or update the `amazon-efs-utils` package on your EC2 instances\. The Systems Manager capabilities like Distributor and State Manager enable you to automate the following processes:
+ Maintaining control over Amazon EFS client versioning\.
+ Centrally storing and systematically distributing the Amazon EFS client to your Amazon EC2 instances\.
+ Automate the process of keeping your Amazon EC2 instances in a defined state\. 

For more information, see [https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html)\.

## Systems Manager Distributor supported operating systems<a name="sys-mgr-support-matrix"></a>

Your EC2 instances must be running one of the following operating systems in order to be used with AWS Systems Manager to automatically update or install the Amazon EFS client\.


| Platform | Platform version | Architecture | 
| --- | --- | --- | 
|  Amazon Linux  |  2017\.09, 2018\.03  | x86\_64 | 
|  Amazon Linux 2  |  2\.0  | x86\_64, arm64 \(Amazon Linux 2, A1 instance types\) | 
|  CentOS  |  7, 8  | x86\_64 | 
|  Red Hat Enterprise Linux \(RHEL\)  |  7\.4\-7\.8, 8\.0\-8\.2  | x86\_64, arm64 \(RHEL 7\.6 and later, A1 instance types\) | 
|  Ubuntu Server  |  16\.04, 18\.04, 20\.04  | x86\_64, arm64 \(Ubuntu Server 16 and later, A1 instance types\) | 

## How to use AWS Systems Manager to automatically install or update amazon\-efs\-utils<a name="setting-up-aws-sys-mgr"></a>

There are two one\-time configurations required to setup Systems Manager to automatically install or update the amazon\-efs\-utils package\.

1. Configure an AWS Identity and Access Management \(IAM\) instance profile with the required permissions\.

1. Configure an Association \(including the schedule\) used for installation or updates by the State Manager

### Step 1: Configure an IAM instance profile with the required permissions<a name="configure-sys-mgr-iam-instance-profile"></a>

 By default, AWS Systems Manager doesn't have permission to manage your Amazon EFS clients and install or update the amazon\-efs\-utils package\. You must grant access to Systems Manager by using an AWS Identity and Access Management \(IAM\) instance profile\. An instance profile is a container that passes IAM role information to an Amazon EC2 instance at launch\. 

Use the `AmazonElasticFileSystemsUtils` AWS managed permission policy to assign the appropriate permissions to roles\. You can create a new role for your instance profile or add the `AmazonElasticFileSystemsUtils` permission policy to an existing role\. You must then use this instance profile to launch your Amazon EC2 instances\. For more information, see [Step 4: Create an IAM instance profile for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-profile.html)\.

### Step 2: Configure an Association used by State Manager for installing or updating the Amazon EFS client<a name="config-sys-mgr-association"></a>

The `amazon-efs-utils` package is included with Distributor and is ready for you to deploy to managed EC2 instances\. To view the latest version of `amazon-efs-utils` that is available for installation, you can use the AWS Systems Manager console or your preferred AWS command line tool\. To access Distributor, open the [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/) and choose **Distributor** in the left navigation pane\. Locate **AmazonEFSUtils** in the **Owned by Amazon** section\. Choose **AmazonEFSUtils** to see the package details\. For more information, see [View packages](https://docs.aws.amazon.com/systems-manager/latest/userguide/distributor-view-packages.html)\.

Using State Manager, you can install or update the `amazon-efs-utils` package on your managed EC2 instances immediately or on a schedule\. Additionally, you can ensure that `amazon-efs-utils` is automatically installed on new EC2 instances\. For more information about installation or updating packages using Distributor and State Manager, see [Working with Distributor](https://docs.aws.amazon.com/systems-manager/latest/userguide/distributor-working-with.html)\.

To automatically install or update the amazon\-efs\-utils package on instances using the Systems Manager console, see [Scheduling a package installation or update \(console\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/distributor-working-with-packages-deploy.html#distributor-deploy-sm-pkg-console)\. This will prompt you to create an association for State Manager, which defines the state you want to apply to a set of instances\. Use the following inputs when you create your association:
+ For **Parameters** choose **Action** > **Install** and **Installation Type** > **In\-place update**\.
+ For **Targets** the recommended setting is **Choose all instances** to register all new and existing EC2 instances as targets to automatically install or update **AmazonEFSUtils**\. Alternatively, you can specify instance tags, select instances manually, or choose a resource group to apply the association to a subset of instances\. If you specify instance tags, you must launch your EC2 instances with the tags to allows AWS Systems Manager to automatically install or update the Amazon EFS client\.
+ For **Specify schedule** the recommended setting for **AmazonEFSUtils** is every 30 days\. You can use controls to create a cron or rate schedule for the association\.

To use AWS Systems Manager to mount multiple an Amazon EFS file system to multiple EC2 instances, see [Mounting EFS to multiple EC2 instances using AWS Systems Manager](mount-multiple-ec2-instances.md)\.