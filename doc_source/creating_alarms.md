# Creating CloudWatch alarms to monitor Amazon EFS<a name="creating_alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period you specify, and performs one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon SNS topic or Auto Scaling policy\.

Alarms invoke actions for sustained state changes only\. CloudWatch alarms don't invoke actions simply because they are in a particular state; the state must have changed and been maintained for a specified number of periods\. 

One important use of CloudWatch alarms for Amazon EFS is to enforce encryption at rest for your file system\. You can enable encryption at rest for an Amazon EFS file system when it's created\. To enforce data encryption\-at\-rest policies for Amazon EFS file systems, you can use Amazon CloudWatch and AWS CloudTrail to detect the creation of a file system and verify that encryption at rest is enabled\. For more information, see [Walkthrough: Enforcing Encryption on an Amazon EFS File System at Rest](efs-enforce-encryption.md)\.

**Note**  
Currently, you can't enforce encryption in transit\.

The following procedures outline how to create alarms for Amazon EFS\.

**To set alarms using the CloudWatch console**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1.  Choose **Create Alarm**\. This launches the **Create Alarm Wizard**\. 

1. Choose **EFS Metrics** and scroll through the Amazon EFS metrics to locate the metric you want to place an alarm on\. To display just the Amazon EFS metrics in this dialog box, search on the file system id of your file system\. Select the metric to create an alarm on and choose **Next**\.

1.  Fill in the **Name**, **Description**, **Whenever** values for the metric\. 

1. If you want CloudWatch to send you an email when the alarm state is reached, in the **Whenever this alarm:** field, choose **State is ALARM**\. In the **Send notification to:** field, choose an existing SNS topic\. If you select **Create topic**, you can set the name and email addresses for a new email subscription list\. This list is saved and appears in the field for future alarms\.
**Note**  
 If you use **Create topic** to create a new Amazon SNS topic, the email addresses must be verified before they receive notifications\. Emails are only sent when the alarm enters an alarm state\. If this alarm state change happens before the email addresses are verified, they do not receive a notification\.

1.  At this point, the **Alarm Preview** area gives you a chance to preview the alarm you’re about to create\. Choose **Create Alarm**\. 

**To set an alarm using the AWS CLI**
+ Call `[put\-metric\-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)`\. For more information, see *[AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)*\.

**To set an alarm using the CloudWatch API**
+ Call `[PutMetricAlarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html)`\. For more information, see *[Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)* 