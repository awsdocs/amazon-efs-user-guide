# Step 2: Create Amazon EFS Resources<a name="wt1-create-efs-resources"></a>

In this step, you do the following:
+ Create an Amazon EFS file system\. 
+ Create a mount target in the Availability Zone where you have your EC2 instance launched\.

**Topics**
+ [Step 2\.1: Create Amazon EFS File System](#wt1-create-file-system)
+ [Step 2\.2: Create a Mount Target](#wt1-create-mount-target)

## Step 2\.1: Create Amazon EFS File System<a name="wt1-create-file-system"></a>

In this step, you create an Amazon EFS file system\. Write down the `FileSystemId` to use later when you create mount targets for the file system in the next step\.

**To create a file system**

1. Create a file system and add the optional `Name` tag\.

   1. At the command prompt, run the following AWS CLI `create-file-system` command\. 

      ```
      $  aws efs create-file-system \
      --creation-token FileSystemForWalkthrough1 \
      --region us-west-2 \
      --profile adminuser
      ```

   1. Verify the file system creation by calling the `describe-file-systems` CLI command\.

      ```
      $  aws efs describe-file-systems \
      --region us-west-2 \
      --profile adminuser
      ```

      Here's an example response:

      ```
      {
          "FileSystems": [
              {
                  "SizeInBytes": {
                      "Timestamp": 1418062014.0,
                      "Value": 1024
                  },
                  "CreationToken": "FileSystemForWalkthrough1",
                  "CreationTime": 1418062014.0,
                  "FileSystemId": "fs-cda54064",
                  "PerformanceMode" : "generalPurpose",
                  "NumberOfMountTargets": 0,
                  "LifeCycleState": "available",
                  "OwnerId": "account-id"
              }
          ]
      }
      ```

   1. Note the `FileSystemId` value\. You need this value when you create a mount target for this file system in the next step\.

1. \(Optional\) Add a tag to the file system you created using the `create-tag` CLI command\. 

   You don't need to create a tag for your file system to complete this walkthrough\. But you are exploring the Amazon EFS API, so let's test the Amazon EFS API for creating and managing tags\. For more information, see [CreateTags](API_CreateTags.md)\.

   1. Add a tag\.

      ```
      $ aws efs create-tags \
      --file-system-id File-System-ID \
      --tags Key=Name,Value=SomeExampleNameValue \
      --region us-west-2 \
      --profile adminuser
      ```

   1. Retrieve a list of tags added to the file system by using the `describe-tags` CLI command\.

      ```
      $ aws efs describe-tags \
      --file-system-id File-System-ID \
      --region us-west-2 \
      --profile adminuser
      ```

      Amazon EFS returns tags list in the response body\.

      ```
      {
          "Tags": [
              {
                  "Value": "SomeExampleNameValue",
                  "Key": "Name"
              }
          ]
      }
      ```

## Step 2\.2: Create a Mount Target<a name="wt1-create-mount-target"></a>

In this step, you create a mount target for your file system in the Availability Zone where you have your EC2 instance launched\. 

1. Make sure you have the following information:
   + ID of the file system \(for example, `fs-example`\) for which you are creating the mount target\. 
   + VPC subnet ID where you launched the EC2 instance in [Step 1](http://docs.aws.amazon.com/efs/latest/ug/wt1-create-ec2-resources.html)\. 

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

   You get this response:

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

**Next Step**  
[Step 3: Mount the Amazon EFS File System on the EC2 Instance and Test](wt1-test.md)