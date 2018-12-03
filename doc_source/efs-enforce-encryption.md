# Walkthrough: Enforcing Encryption on an Amazon EFS File System at Rest<a name="efs-enforce-encryption"></a>

Following, you can find details about how to enforce encryption at rest using Amazon CloudWatch and AWS CloudTrail\. This walkthrough is based upon the AWS whitepaper [Encrypt Data at Rest with Amazon EFS Encrypted File Systems](https://d1.awsstatic.com/whitepapers/Security/amazon-efs-encrypted-filesystems.pdf)\. 

**Note**  
Currently, you can't enforce encryption in transit\.

## Enforcing Encryption at Rest<a name="efs-enforce-overview"></a>

Your organization might require the encryption at rest of all data that meets a specific classification or that is associated with a particular application, workload, or environment\. You can enforce policies for data encryption at rest for Amazon EFS file systems by using detective controls\. These controls detect the creation of a file system and verify that encryption at rest is enabled\. 

If a file system that doesn't have encryption at rest is detected, you can respond in a number of ways\. These range from deleting the file system and mount targets to notifying an administrator\.

If you want to delete an unencrypted\-at\-rest file system but want to retain the data, first create a new encrypted\-at\-rest file system\. Next, copy the data over to the new encrypted\-at\-rest file system\. After the data is copied over, you can delete the unencrypted\-at\-rest file system\. 

### Detecting Files Systems That Are Unencrypted at Rest<a name="efs-detecting-unencrypted"></a>

You can create an CloudWatch alarm to monitor CloudTrail logs for the `CreateFileSystem` event\. You can then trigger the alarm to notify an administrator if the file system that was created was unencrypted at rest\.

### Create a Metric Filter<a name="efs-create-unencrypted-filter"></a>

To create a CloudWatch alarm that is triggered when an unencrypted Amazon EFS file system is created, use the following procedure\. 

Before you begin, you must have an existing trail created that is sending CloudTrail logs to a CloudWatch Logs log group\. For more information, see [Sending Events to CloudWatch Logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/send-cloudtrail-events-to-cloudwatch-logs.html) in the *AWS CloudTrail User Guide*\.

**To create a metric filter**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. In the list of log groups, choose the log group that you created for CloudTrail log events\.

1. Choose **Create Metric Filter**\.

1. On the** Define Logs Metric Filter** page, choose **Filter Pattern** and then type the following:

   ```
   { ($.eventName = CreateFileSystem) && ($.responseElements.encrypted IS FALSE) } 
   ```

1. Choose **Assign Metric**\.

1. For **Filter Name**, type **UnencryptedFileSystemCreated**\.

1. For **Metric Namespace**, type **CloudTrailMetrics**\.

1. For **Metric Name**, type **UnencryptedFileSystemCreatedEventCount**\.

1. Choose **Show advanced metric settings**\.

1. For **Metric Value**, type **1**\.

1. Choose **Create Filter**\.

### Create an Alarm<a name="efs-create-unencrypted-alarm"></a>

After you create the metric filter, use the following procedure to create an alarm\.

**To create an alarm**

1. On the **Filters** for the **Log\_Group\_Name** page, next to the **UnencryptedFileSystemCreated** filter name, choose **Create Alarm**\.

1. On the **Create Alarm** page, set the following parameters:
   + For **Name**, type **Unencrypted File System Created**
   + For **Whenever**, do the following:
     + Set **is** to *> = 1*
     + Set **for:** to *1* consecutive period\(s\)\.
   + For **Treat missing data as**, choose **good \(not breaching threshold\)**\.
   + For **Actions**, do the following:
     + For **Whenever this alarm**, choose **State is ALARM**\. 
     + For **Send notification to**, choose **NotifyMe**, choose **New list**, and then type a unique topic name for this list\.
     + For **Email list**, type in the email address where you want notifications sent\. You should receive an email at this address to confirm that you created this alarm\.
   + For **Alarm Preview**, do the following:
     + For **Period**, choose **1 Minute**\.
     + For **Statistic**, choose **Standard** and **Sum**\.

1. Choose **Create Alarm**\.

### Test the Alarm for the Creation of Unencrypted File Systems<a name="efs-test-unencrypted-alarm"></a>

You can test the alarm by creating an unencrypted\-at\-rest file system, as follows\.

**To test the alarm by creating an unencrypted\-at\-rest file system**

1. Open the Amazon EFS console at [https://console\.aws\.amazon\.com/efs](https://console.aws.amazon.com/efs)\.

1. Choose **Create File System**\.

1. From the **VPC** list, choose your default VPC\.

1. Choose all the Availability Zones\. Ensure that the default subnets, automatic IP addresses, and the default security groups are chosen\. These are your mount targets\.

1. Choose **Next Step**\.

1. Name your file system and keep **Enable encryption** unchecked to create an unencrypted file system\.

1. Choose **Next Step**\.

1. Choose **Create File System**\.

Your trail logs the `CreateFileSystem` operation and delivers the event to your CloudWatch Logs log group\. The event triggers your metric alarm and CloudWatch Logs sends you a notification about the change\.