# Creating Amazon EFS file systems<a name="creating-using-create-fs"></a>

Following, you can find an explanation about how to create an Amazon EFS file system using the AWS Management Console and the AWS CLI\. 

If you are new to Amazon EFS, we recommend you go through the Getting Started exercise, which provides console\-based end\-to\-end instructions to create and access a file system in your VPC\. For more information, see [Getting started](getting-started.md)\. 

**Topics**
+ [Requirements](#reqs-fs-create)
+ [Configuration options when creating a file system](#creating-using-create-fs-part1)
+ [Creating a file system with custom settings using the Amazon EFS console](#creating-using-fs-part1-console)
+ [Creating a file system using the AWS CLI](#creating-using-fs-part1-cli)

## Requirements<a name="reqs-fs-create"></a>

This section describes requirements and prerequisites for creating Amazon EFS file systems\.

### Creation token and idempotency<a name="creation-token"></a>

To create a file system, the only requirement is that you create a token to ensure idempotent operation\. If you use the console, it generates the token for you\. If you use the **Custom Create** flow in the console, the creation token that is generated for you has the following format:

```
"CreationToken": "console-d215fa78-1f83-4651-b026-facafd8a7da7"
```

If you use QuickCreate to create a file system with the service recommended settings, the creation token has the following format:

```
"CreationToken": "quickCreated-d7f56c5f-e433-41ca-8307-9d9c0f8a77a2"
```

### Permissions required<a name="perm-fs-api"></a>

To create EFS resources such as a file system and access points, a user must have AWS Identity and Access Management \(IAM\) permissions for the corresponding API action and resource\.

You can perform any Amazon EFS operations using your AWS account root user credentials, but using root user credentials is not recommended\. We recommend that you create IAM users in your account, and grant them permissions for Amazon EFS actions with user policies\. You can also use roles to grant cross\-account permissions\. Amazon Elastic File System also uses an AWS IAM service\-linked role that includes permissions required to call other AWS services on your behalf\. For more information about managing permissions for the API actions, see [Identity and Access Management for Amazon EFS](auth-and-access-control.md)\.

## Configuration options when creating a file system<a name="creating-using-create-fs-part1"></a>

You can create a file system using the Amazon EFS console or using the AWS Command Line Interface \(AWS CLI\)\. You can also create file systems programmatically using AWS SDKs or the Amazon EFS API directly\.

When creating an Amazon EFS file system using the custom create flow or the AWS CLI, you can choose settings for the following file system features and configuration options\.

### Availability and durability<a name="availabiltydurability"></a>

Availability and durability refer to the redundancy with which an Amazon EFS file system stores data within an AWS Region\. You have the following choices for your file system's availability and durability:
+ Choosing **Regional** creates a file system that uses [Standard storage classes](storage-classes.md#regional-sc) that store file system data and metadata redundantly across all Availability Zones within an AWS Region\. You can also create mount targets in each Availability Zone in the AWS region\. **Regional** offers the highest levels of availability and durability\.
+ Choosing **One Zone** creates a file system that uses [One Zone storage classes](storage-classes.md#one-zone-sc) that store file system data and metadata redundantly within a single Availability Zone\. File systems using One Zone storage classes can have only a single mount target which is located in the Availability Zone in which the file system is created\.

  Amazon EFS One Zone storage classes store data in a single AWS Availability Zone\. Therefore, data stored in these storage classes may be lost in the event of a disaster or other fault that affects all copies of the data within the Availability Zone, or in the event of Availability Zone destruction\.

If you choose One Zone, you can choose the Availability Zone in which the file system is created\.

### Automatic backups<a name="create-fs-autobackup"></a>

Automatic backups are always enabled by default when you create a file system using the console\. When you use the CLI or API to create a file system, automatic backups are enabled by default only when you are creating file systems using One Zone storage classes\. For more information, see [Automatic backups](awsbackup.md#automatic-backups)\.

### Lifecycle management<a name="create-fs-lifecycle-mgmnt"></a>

 Lifecycle management migrates files that have not been accessed for a set period of time to the lower\-cost Infrequent Access \(IA\) storage class\. You can set the policy that Amazon EFS lifecycle management uses to automatically manage cost\-effective file storage for your file systems when you create your file system\. When you create a file system using the console, the policy is set to **30 days since last access** by default\. When you use the CLI or API, there is no default setting\. For more information, see [Amazon EFS lifecycle management](lifecycle-management-efs.md)\.

### Performance modes<a name="create-fs-performance-mode"></a>

When creating a file system, you also choose a performance mode\. There are two performance modes to choose from—*General Purpose* and *Max I/O*\. For the majority of use cases, we recommend that you use the General Purpose performance mode for your file system\. For more information, see [Performance modes](performance.md#performancemodes)\.

**Note**  
For file systems that use One Zone storage classes, only the General Purpose performance mode is available\.

### Throughput modes<a name="create-fs-throughput-mode"></a>

There are two throughput modes to choose from—*Bursting Throughput* and *Provisioned Throughput*\. The default Bursting Throughput mode is simple to work with and is suitable for a majority of applications and a wide range of performance requirements\. Provisioned Throughput mode is for applications that require a greater ratio of throughput to storage capacity than allowed by Bursting Throughput mode\. For more information, see [Throughput modes](performance.md#throughput-modes)\.

**Note**  
Additional charges are associated with using Provisioned Throughput mode\. For more information, see [Amazon EFS pricing](http://aws.amazon.com/efs/pricing/)\.

### Encryption<a name="create-fs-encryption"></a>

You can enable encryption at rest when creating a file system\. If you enable encryption at rest for your file system, all data and metadata stored on it are encrypted\. You can enable encryption in transit later, when you mount the file system\. For more information about Amazon EFS encryption, see [Data encryption in Amazon EFS](encryption.md)\.

To create the file system mount targets in your VPC, you must specify VPC subnets\. The console prepopulates the list of VPCs in your account that are in the selected AWS Region\. First, you select your VPC, and then the console lists the Availability Zones in the VPC\. For each Availability Zone, you can select a subnet from the list, or use the default subnet if it exists\. After you select a subnet, you can either specify an available IP address in the subnet or let Amazon EFS choose an address automatically\.

## Creating a file system with custom settings using the Amazon EFS console<a name="creating-using-fs-part1-console"></a>

This section describes the process of using the Amazon EFS console to create an EFS file system with customized settings instead of using the service recommended settings\. For more information about creating a file system using the service recommended settings, see [Step 1: Create your Amazon EFS file system](gs-step-two-create-efs-resources.md)\.

Creating an Amazon EFS file system using the console is a four\-step process:
+ Step 1 \- Configure general file system settings, including availability and durability, lifecycle management, performance and throughput modes, and encryption of data at rest\.
+ Step 2 \- Configure file system network settings, including the virtual private cloud \(VPC\) and mount targets\. For each mount target, set the Availability Zone, subnet, IP address, and security groups\.
+ Step 3 \- \(Optional\) Create a file system policy to control NFS client access to the file system\.
+ Step 4 \- Review the file system settings, make any changes, and then create the file system\.<a name="config-file-system-settings"></a>

**Step 1: Configure file system settings**

1. Sign in to the AWS Management Console and open the Amazon EFS console at [ https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create file system** to open the **Create file system** dialog box\.  
![\[Amazon EFS Create file system window\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-create-file-system.png)

1. Choose **Customize** to create a customized file system instead of creating a file system using the service recommended settings\. 

   The **File system settings** page appears\.  
![\[Step 1 in creating an EFS file system using the General settings page in the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fscustomcreate-step1-general.png)

1. For **General** settings, enter the following\.

   1. \(Optional\) Enter a **Name** for the file system\.

   1. For **Availability and Durability**, choose one of the following:
      + Choose **Regional** to create a file system that uses Standard storage classes\. Standard storage classes store file system data and metadata redundantly across all Availability Zones within an AWS Region\. **Regional** offers the highest levels of availability and durability\.
      + Choose **One Zone** to create a file system that uses One Zone storage classes\. One Zone storage classes store file system data and metadata redundantly within a single Availability Zone\.

        Amazon EFS One Zone storage classes store data in a single AWS Availability Zone\. Therefore, data stored in these storage classes might be lost in the event of a disaster or other fault that affects all copies of the data within the Availability Zone, or in the event of Availability Zone destruction\.

      If you choose One Zone, choose the **Availability Zone** that you want the file system created in, or keep the default setting\.

      For more information, see [Managing EFS storage classes](storage-classes.md)\.

   1. **Automatic backups** are turned on by default\. You can turn off automatic backups by clearing the check box\. For more information, see [Using AWS Backup with Amazon EFS](awsbackup.md)\.

   1. Choose a **Lifecycle management** policy\. The default policy is **30 days after last access**\. If you don't want to use lifecycle management, choose **None**\. For more information, see [Amazon EFS lifecycle management](lifecycle-management-efs.md)\.

   1. Choose a **Performance mode**, either the default **General Purpose** mode or **Max I/O**\. You can change the performance mode after the file system becomes available\. For more information, see [Performance modes](performance.md#performancemodes)\.
**Note**  
Only the General Purpose mode is available for file systems that are configured for One Zone availability and durability\.

   1. Choose a **Throughput mode**, either the default **Bursting** mode or **Provisioned** mode\. 

      If you choose **Provisioned**, the **Provisioned Throughput \(MiB/s\)** section appears\. Enter the amount of **Throughput** to provision for file system requests\.

      The amount of **Maximum Read Throughput** is displayed at three times the amount of the throughput you enter\. EFS file systems meter read requests at one\-third the rate of other requests\. After you enter the throughput, an estimate of the monthly cost for the file system is displayed\. 

      You can change the amount of write throughput anytime after the file system becomes available\. For more information, see [Throughput modes](performance.md#throughput-modes)\.

   1. For **Encryption**, encryption of data at rest is enabled by default\. It uses your AWS Key Management Service \(AWS KMS\) EFS service key \(`aws/elasticfilesystem`\) by default\. To choose a different KMS key to use for encryption, expand **Customize encryption settings** and choose a key from the list\. Or, enter a KMS key ID or Amazon Resource Name \(ARN\) for the KMS key you want to use\.

      If you need to create a new key, choose **Create an AWS KMS key** to launch the AWS KMS console and create a new key\.

      You can turn off encryption of data at rest by clearing the check box\.

1. \(Optional\) Add tag key\-value pairs to your file system\.

1. Choose **Next** to continue to the **Network Access** step in the configuration process\.<a name="configure-efs-network-access"></a>

**Step 2: Configure network access**

In Step 2, you configure the file system's network settings, including the VPC and mount targets\.  
![\[Step 2 in creating an EFS file system, configuring network settings using the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fscustomcreate-step2-networkaccess.png)

1. Choose the **Virtual Private Cloud \(VPC\)** where you want EC2 instances to connect to your file system\. For more information, see [Managing file system network accessibility](manage-fs-access.md)\.

1. For **Mount targets**, you create one or more mount targets for your file system\. For each mount target, set the following properties:
   + **Availability zone** – By default, a mount target is configured in each Availability Zone in an AWS Region\. If you don't want a mount target in a particular Availability Zone, choose **Remove** to delete the mount target for that zone\. Create a mount target in every Availability Zone that you plan to access your file system from – there is no cost to do so\.
   + **Subnet ID** – Choose from the available subnets in an Availability Zone\. The default subnet is preselected\.
   + **IP Address** – By default, Amazon EFS chooses the IP address automatically from the available addresses in the subnet\. Or, you can enter a specific IP address that's in the subnet\. Although mount targets have a single IP address, they are redundant, highly available network resources\.
   + **Security groups** – You can specify one or more security groups for the mount target\. For more information, see [Using VPC security groups for Amazon EC2 instances and mount targets](network-access.md)\.

     To add another security group, or to change the security group, choose **Choose security groups** and add another security group from the list\. If you don't want to use the default security group, you can delete it\. For more information, see [Creating security groups](accessing-fs-create-security-groups.md)\.

1. Choose **Add mount target** to create a mount target for an Availability Zone that doesn't have one\. If a mount target is configured for each Availability Zone, this choice is not available\.

1. Choose **Next** to continue\. The **File system policy** page is displayed\.

**Step 3: Create a file system policy \(optional\)**

Optionally, you can create a file system policy for your file system\. An EFS file system policy is an IAM resource policy that you use to control NFS client access to the file system\. For more information, see [Using IAM to control file system data access](iam-access-control-nfs-efs.md)\.  
![\[Step 3 in creating an EFS file system, optionally create a file system policy.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fscustomcreate-step3-fsp.png)

1. In **Policy options**, you can choose any combination of the available preconfigured policies:
   + Prevent root access by default
   + Enforce read\-only access by default
   + Enforce in\-transit encryption for all clients

1. Use the **Policy editor** to customize a preconfigured policy or to create your own policy\. When you choose one of the preconfigured policies, the JSON policy definition appears in the policy editor\. You can edit the JSON to create a policy of your choice\. To undo your changes, choose **Clear**\.

   The preconfigured policies become available once again in **Policy options**\.

1. Choose **Next** to continue\. The **Review and create** page appears\.

**Step 4: Review and create**

1. Review each of the file system configuration groups\. You can make changes to each group at this time by choosing **Edit**\.  
![\[File system details page showing the newly created file system and its configuration details.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-fscustomcreate-step4-rvw.png)

1. Choose **Create** to create your file system and return to the **File systems** page\. 

   A banner across the top shows that the new file system is being created\. A link to access the new file system details page appears in the banner when the file system becomes available\.

1. The **File systems** page displays the file system and its configuration details, as shown following\.   
![\[File system details page showing the newly created file system and its configuration details.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-efs-filesystem-details.png)

## Creating a file system using the AWS CLI<a name="creating-using-fs-part1-cli"></a>

When using the AWS CLI, you create these resources in order\. First, you create a file system\. Then, you can create mount targets and any additional optional tags for the file system using corresponding AWS CLI commands\.

The following examples use the `adminuser` as the `profile` parameter values\. You need to use an appropriate user profile to provide your credentials\. For information about the AWS CLI, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.
+ To create an encrypted file system that uses Standard storage classes, with automatic backups enabled, use the Amazon EFS `create-file-system` CLI command \(the corresponding operation is [CreateFileSystem](API_CreateFileSystem.md)\), as shown following\.

  ```
  $ aws efs create-file-system \
  -\-creation-token creation-token \
  -\-encrypted true \
  -\-backup true \
  -\-performance-mode generalPurpose \
  -\-throughput-mode bursting \
  -\-region aws-region \
  -\-tags Key=key,Value=value Key=key1,Value=value1 \
  -\-profile adminuser
  ```

  For example, the following `create-file-system` command creates a file system in the `us-west-2` AWS Region\. The command specifies `MyFirstFS` as the creation token\. For a list of AWS Regions where you can create an Amazon EFS file system, see the [Amazon Web Services General Reference](https://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem_region)\.

  ```
  $  aws efs create-file-system \
  -\-creation-token MyFirstFS \
  -\-backup true \
  -\-encrypted true \
  -\-performance-mode generalPurpose \
  -\-throughput-mode bursting \
  -\-region us-west-2 \
  -\-tags Key=Name,Value="Test File System" Key=developer,Value=rhoward \
  -\-profile adminuser
  ```

  After successfully creating the file system, Amazon EFS returns the file system description as JSON, as shown in the following example\.

  ```
  {
      "OwnerId": "123456789abcd",
      "CreationToken": "MyFirstFS",
      "Encrypted": true,
      "FileSystemId": "fs-c7a0456e",
      "CreationTime": 1422823614.0,
      "LifeCycleState": "creating",
      "Name": "Test File System",
      "NumberOfMountTargets": 0,
      "SizeInBytes": {
          "Value": 6144,
          "ValueInIA": 0,
          "ValueInStandard": 6144
      },
      "PerformanceMode": "generalPurpose",
      "ThroughputMode": "bursting",
      "Tags": [ 
        { 
           "Key": "Name",
           "Value": "Test File System"
        }
     ]
  }
  ```

  The following example creates a file system that uses One Zone storage classes in the `us-west-2a` Availability Zone using the `availability-zone-name` property\.

  ```
  $  aws efs create-file-system \
  -\-creation-token MyFirstFS \
  -\-availability-zone-name us-west-2a \
  -\-backup true \
  -\-encrypted true \
  -\-performance-mode generalPurpose \
  -\-throughput-mode bursting \
  -\-region us-west-2 \
  -\-tags Key=Name,Value="Test File System" Key=developer,Value=rhoward \
  -\-profile adminuser
  ```

  After successfully creating the file system, Amazon EFS returns the file system description as JSON, as shown in the following example\.

  ```
  {
      "AvailabilityZoneId": "usw-az1",
      "AvailabilityZoneName": "us-west-2a",
      "OwnerId": "123456789abcd",
      "CreationToken": "MyFirstFS",
      "Encrypted": true,
      "FileSystemId": "fs-c7a0456e",
      "CreationTime": 1422823614.0,
      "LifeCycleState": "creating",
      "Name": "Test File System",
      "NumberOfMountTargets": 0,
      "SizeInBytes": {
          "Value": 6144,
          "ValueInIA": 0,
          "ValueInStandard": 6144
      },
      "PerformanceMode": "generalPurpose",
      "ThroughputMode": "bursting",
      "Tags": [ 
        { 
           "Key": "Name",
           "Value": "Test File System"
        }
     ]
  }
  ```

  Amazon EFS also provides the `describe-file-systems` CLI command \(the corresponding API operation is [DescribeFileSystems](API_DescribeFileSystems.md)\), which you can use to retrieve a list of file systems in your account, as shown following\.

  ```
  $  aws efs describe-file-systems \
  -\-region aws-region \
  -\-profile adminuser
  ```

  Amazon EFS returns a list of the file systems in your AWS account created in the specified Region\.