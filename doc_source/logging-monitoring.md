# Logging and Monitoring in Amazon EFS<a name="logging-monitoring"></a>

 Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon EFS and your AWS solutions\. You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multi\-point failure if one occurs\. AWS provides several tools for monitoring your Amazon EFS resources and responding to potential incidents: 

**Amazon CloudWatch Alarms**  
Using Amazon CloudWatch events, you watch a single metric over a time period that you specify\. If the metric exceeds a given threshold, a notification is sent to an Amazon Simple Notification Service \(Amazon SNS\) topic or Amazon EC2 Auto Scaling policy\. CloudWatch events do not invoke actions simply because they are in a particular state; the state must have changed and been maintained for a specified number of periods\. For more information, see [Monitoring EFS with Amazon CloudWatch](monitoring-cloudwatch.md)\. 

**Amazon CloudWatch Logs**  
You can use Amazon CloudWatch Logs to monitor, store, and access your log files from AWS CloudTrail or other sources\. For more information, see [Monitoring Log Files](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) in the *Amazon CloudWatch User Guide*\.

**Amazon CloudWatch Events**  
 Match events and route them to one or more target functions or streams to make changes, capture state information, and take corrective action\. For more information, see [What is Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html) in the *Amazon CloudWatch User Guide*\. 

**AWS CloudTrail Log Monitoring**  
 CloudTrail provides a record of actions taken by a user, role, or an AWS service in Amazon EFS\. Using the information collected by CloudTrail, you can determine the request that was made to Amazon EFS, the IP address from which the request was made, who made the request, when it was made, and additional details\. For more information, see [Logging Amazon EFS API Calls with AWS CloudTrail](logging-using-cloudtrail.md)\. 