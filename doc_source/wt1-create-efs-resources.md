# Step 2: Create Amazon EFS resources<a name="wt1-create-efs-resources"></a>

In this step, you do the following:
+ Create an encrypted Amazon EFS file system\. 
+ Enable lifecycle management\.
+ Create a mount target in the Availability Zone where you have your EC2 instance launched\.

**Topics**
+ [Step 2\.1: Create an Amazon EFS file system](#wt1-create-file-system)
+ [Step 2\.2: Enable lifecycle management](#wt1-lifecycle-management)
+ [Step 2\.3: Create a mount target](#wt1-create-mount-target)

## Step 2\.1: Create an Amazon EFS file system<a name="wt1-create-file-system"></a>

In this step, you create an Amazon EFS file system\. Write down the `FileSystemId` to use later when you create mount targets for the file system in the next step\.

**To create a file system**
+ Create a file system with the optional `Name` tag\.

  1. At the command prompt, run the following AWS CLI `create-file-system` command\. 

     ```
     $  aws efs create-file-system \
     --encrypted \
     --creation-token FileSystemForWalkthrough1 \
     --tags Key=Name,Value=SomeExampleNameValue \
     --region us-west-2 \
     --profile adminuser
     ```

     You get the following response\.

     ```
     {
         "OwnerId": "111122223333",
         "CreationToken": "FileSystemForWalkthrough1",
         "FileSystemId": "fs-c657c8bf",
         "CreationTime": 1548950706.0,
         "LifeCycleState": "creating",
         "NumberOfMountTargets": 0,
         "SizeInBytes": {
             "Value": 0,
             "ValueInIA": 0,
             "ValueInStandard": 0
         },
         "PerformanceMode": "generalPurpose",
         "Encrypted": true,
         "KmsKeyId": "arn:aws:kms:us-west-2:111122223333:a5c11222-7a99-43c8-9dcc-abcdef123456",
         "ThroughputMode": "bursting",
         "Tags": [
             {
                 "Key": "Name",
                 "Value": "SomeExampleNameValue"
             }
         ]
     }
     ```

  1. Note the `FileSystemId` value\. You need this value when you create a mount target for this file system in [Step 2\.3: Create a mount target](#wt1-create-mount-target)\.

## Step 2\.2: Enable lifecycle management<a name="wt1-lifecycle-management"></a>

In this step, you enable lifecycle management on your ﬁle system in order to use the Infrequent Access storage class\. To learn more, see [Amazon EFS lifecycle management](lifecycle-management-efs.md) and [EFS storage classes](storage-classes.md)\.

**To enable lifecycle management**
+ At the command prompt, run the following AWS CLI `put-lifecycle-configuration` command\.

  ```
  $  aws efs put-lifecycle-configuration \
  --file-system-id fs-c657c8bf \
  --lifecycle-policies TransitionToIA=AFTER_30_DAYS \
  --region us-west-2 \
  --profile adminuser
  ```

  You get the following response\.

  ```
  {
    "LifecyclePolicies": [
      {
          "TransitionToIA": "AFTER_30_DAYS"
      }
    ]
  }
  ```

## Step 2\.3: Create a mount target<a name="wt1-create-mount-target"></a>

In this step, you create a mount target for your file system in the Availability Zone where you have your EC2 instance launched\. 

1. Make sure you have the following information:
   + ID of the file system \(for example, `fs-example`\) for which you are creating the mount target\. 
   + VPC subnet ID where you launched the EC2 instance in [Step 1](https://docs.aws.amazon.com/efs/latest/ug/wt1-create-ec2-resources.html)\. 

     For this walkthrough, you create the mount target in the same subnet in which you launched the EC2 instance, so you need the subnet ID \(for example, `subnet-example`\)\. 
   + ID of the security group you created for the mount target in the preceding step\.

1. At the command prompt, run the following AWS CLI `create-mount-target` command\. 

   ```
   $ aws efs create-mount-target \
   --file-system-id file-system-id \
   --subnet-id  subnet-id \
   --security-group ID-of-the security-group-created-for-mount-target \
   --region us-west-2 \
   --profile adminuser
   ```

   You get the following response\.

   ```
   {
       "MountTargetId": "fsmt-example",
       "NetworkInterfaceId": "eni-example",
       "FileSystemId": "fs-example",
       "PerformanceMode" : "generalPurpose",
       "LifeCycleState": "available",
       "SubnetId": "fs-subnet-example",
       "OwnerId": "account-id",
       "IpAddress": "xxx.xx.xx.xxx"
   }
   ```

1. You can also use the `describe-mount-targets` command to get descriptions of mount targets you created on a file system\.

   ```
   $ aws efs describe-mount-targets \
   --file-system-id file-system-id \
   --region us-west-2 \
   --profile adminuser
   ```

**Next step**  
[Step 3: Mount the file system on the EC2 instance and test](wt1-test.md)