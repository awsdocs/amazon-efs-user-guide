# Monitoring EFS with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor file systems using Amazon CloudWatch, which collects and processes raw data from Amazon EFS into readable, near real\-time metrics\. These statistics are recorded for a period of 15 months, so that you can access historical information and gain a better perspective on how your web application or service is performing\.

By default, Amazon EFS metric data is automatically sent to CloudWatch at 1\-minute periods, unless noted for some individual metrics\. The Amazon EFS console displays a series of graphs based on the raw data from Amazon CloudWatch\. Depending on your needs, you might prefer to get data for your file systems from CloudWatch instead of the graphs in the console\.

For more information about Amazon CloudWatch, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)\.

**Topics**
+ [Amazon CloudWatch metrics for Amazon EFS](efs-metrics.md)
+ [How do I use Amazon EFS metrics?](how_to_use_metrics.md)
+ [Using metric math with Amazon EFS](monitoring-metric-math.md)
+ [How do I monitor my EFS file system's mount status?](how-to-monitor-mount-status.md)
+ [Accessing CloudWatch metrics](accessingmetrics.md)
+ [Creating CloudWatch alarms to monitor Amazon EFS](creating_alarms.md)