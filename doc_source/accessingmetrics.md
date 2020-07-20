# Accessing CloudWatch metrics<a name="accessingmetrics"></a>

You can see Amazon EFS metrics for CloudWatch in many ways\. They are available in the **Monitoring** section of the **File system details** page\. You can view them through the CloudWatch console, or you can access them using the CloudWatch CLI or the CloudWatch API\. The following procedures show you how to access the metrics using these various tools\.

**To view CloudWatch metrics and alarms in the Amazon EFS console**

1. Sign in to the AWS Management Console and open the Amazon EFS console at [ https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File systems**\.

1. Choose the file system that you want to view CloudWatch metrics for\.

1. Choose **Monitoring ** to display the **File system metrics** page\.  
![\[The File system metrics page displays CloudWatch metrics and alarms, if configured, for the file system.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-monitoring-panel.png)

   The **File system metrics** page displays the following CloudWatch metrics for the file system:
   + Throughput utilization \(%\) \* – not a CloudWatch metric, it is derived using CloudWatch Metric Math\.
   + IOPS by type \*
   + Throughput by type \*
   + Average IP size \(KiB\) \*
   + Percent IO limit \*
   + Client connections \*
   + Permitted throughput
   + Burst credit balance
   + Data write IO bytes
   + Data read IO bytes
   + Metadata IO bytes

   \* These metrics are displayed by default\.

   Any alarms that you have configured appear against these metrics\.

   For metric definitions, see [Amazon CloudWatch metrics for Amazon EFS](monitoring-cloudwatch.md#efs-metrics)\.

   On the **File system metrics** page, you can use the controls do the following:  
![\[Setting controls for metrics.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-metrics-panel-controls.png)
   + Choose which metrics are displayed\. To adjust which metrics are displayed, select the gears option on the upper right to display the **Metric display settings** dialog box\.
   + Show or hide any CloudWatch alarms configured for the file system\.
   + Toggle the **Display mode** between **Time series** or **Single value**\.
   + Add the displayed metrics to your CloudWatch dashboard\. Choose **Add to dashboard** to open your CloudWatch dashboard\.
   + Adjust the metric time window displayed from 1 hour to 1 week\.

**To view metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch)\.

1. In the navigation pane, choose **Metrics**\. 

1. Select the **EFS** namespace\.

1. \(Optional\) To view a metric, enter its name in the search field\.

1. \(Optional\) To filter by dimension, select **FileSystemId**\.

**To access metrics from the AWS CLI**
+  Use the [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command with the `--namespace "AWS/EFS"` namespace\. For more information, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)\. 

**To access metrics from the CloudWatch API**
+  Call `[GetMetricStatistics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetMetricStatistics.html)`\. For more information, see [Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)\. 