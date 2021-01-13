# Accessing CloudWatch metrics<a name="accessingmetrics"></a>

You can view Amazon EFS metrics for CloudWatch in several ways:
+ In the Amazon EFS console
+ In the CloudWatch console
+ Using the CloudWatch CLI
+ Using the CloudWatch API

The following procedures show you how to access the metrics using these various tools\.

**To view CloudWatch metrics and alarms in the Amazon EFS console**

1. Sign in to the AWS Management Console and open the Amazon EFS console at [ https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File systems**\.

1. Choose the file system that you want to view CloudWatch metrics for\.

1. Choose **Monitoring** to display the **File system metrics** page\.

   The **File system metrics** page displays a default set of CloudWatch metrics for the file system\. Any CloudWatch alarms that you have configured also display with these metrics\. For file systems that use Max I/O performance mode, the default set of metrics includes Burst Credit balance in place of Percent IO limit\. You can override the default settings using the **Metrics settings** dialog box, accessed by choosing the settings icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/settings.png)\)\. 
**Note**  
The Throughput utilization \(%\) metric is not a CloudWatch metric, it is derived using CloudWatch metric math\.

1. You can adjust the way metrics and alarms are displayed using the controls on the **File system metric** page, as follows\.  
![\[Console screen shot showing setting controls for metrics and alarms.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-metrics-panel-controls.png)
   + Toggle the **Display mode** between **Time series** or **Single value**\.
   + Show or hide any CloudWatch alarms configured for the file system\.
   + Choose **See more in CloudWatch** to view the metrics in CloudWatch\.
   + Choose **Add to dashboard** to open your CloudWatch dashboard and add the displayed metrics\.
   + Adjust the metric time window displayed from 1 hour to 1 week\.

**To view metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch)\.

1. In the navigation pane, choose **Metrics**\. 

1. Select the **EFS** namespace\.

1. \(Optional\) To view a metric, enter its name in the search field\.

1. \(Optional\) To filter by dimension, select **FileSystemId**\.

**To access metrics from the AWS CLI**
+ Use the [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command with the `--namespace "AWS/EFS"` namespace\. For more information, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)\.

**To access metrics from the CloudWatch API**
+  Call `[GetMetricStatistics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetMetricStatistics.html)`\. For more information, see [Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)\. 