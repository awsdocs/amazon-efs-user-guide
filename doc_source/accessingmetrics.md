# Access CloudWatch Metrics<a name="accessingmetrics"></a>

 There are many ways to see the Amazon EFS metrics for CloudWatch\. You can view them through the CloudWatch console, or you can access them using the CloudWatch CLI or the CloudWatch API\. The following procedures show you how to access the metrics using these various tools\.

**To view metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch)\.

1. In the navigation pane, choose **Metrics**\. 

1. Select the **EFS** namespace\.

1. \(Optional\) To view a metric, type its name in the search field\.

1. \(Optional\) To filter by dimension, select **FileSystemId**\.

**To access metrics from the AWS CLI**

+  Use the [http://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html](http://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command with the `--namespace "AWS/EFS"` namespace\. For more information, see the [AWS Command Line Interface Reference](http://docs.aws.amazon.com/cli/latest/reference/)\. 

**To access metrics from the CloudWatch API**

+  Call `[GetMetricStatistics](http://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetMetricStatistics.html)`\. For more information, see [Amazon CloudWatch API Reference](http://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)\. 