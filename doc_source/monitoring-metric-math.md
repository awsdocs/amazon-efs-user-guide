# Using metric math with Amazon EFS<a name="monitoring-metric-math"></a>

Using metric math, you can query multiple CloudWatch metrics and use math expressions to create new time series based on these metrics\. You can visualize the resulting time series in the CloudWatch console and add them to dashboards\. For example, you can use Amazon EFS metrics to take the sample count of `DataRead` operations divided by 60\. The result is the average number of reads per second on your file system for a given 1\-minute period\. For more information on metric math, see [Use Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html) in the* Amazon CloudWatch User Guide\.*

Following, find some useful metric math expressions for Amazon EFS\.

**Topics**
+ [Metric math: Throughput in MiB/second](#metric-math-throughput-mib)
+ [Metric math: Percent throughput](#metric-math-throughput-percent)
+ [Metric Math: Percentage of permitted throughput utilization](#metric-math-throughput-utilization)
+ [Metric math: Throughput IOPS](#metric-math-throughput-iops)
+ [Metric math: Percentage of IOPS](#metric-math-iops-percent)
+ [Metric math: Average I/O size in KiB](#metric-math-average-io)
+ [Using metric math through an AWS CloudFormation template for Amazon EFS](#metric-math-cloudformation-template)

## Metric math: Throughput in MiB/second<a name="metric-math-throughput-mib"></a>

To calculate the average throughput \(in MiB/second\) for a time period, first choose a sum statistic \(`DataReadIOBytes`, `DataWriteIOBytes`, `MetadataIOBytes`, or `TotalIOBytes`\)\. Then convert the value to MiB, and divide that by the number of seconds in the period\.

Suppose that your example logic is this: \(sum of `TotalIOBytes` ÷ 1,048,576 \(to convert to MiB\)\) ÷ seconds in the period

Then your CloudWatch metric information is the following\.


| ID | Usable metrics | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sum | 1 minute | 

Your metric math ID and expression are the following\.


| ID | Expression | 
| --- | --- | 
| e1 | \(m1/1048576\)/PERIOD\(m1\) | 

## Metric math: Percent throughput<a name="metric-math-throughput-percent"></a>

To calculate the percent throughput of the different I/O types \(`DataReadIOBytes`, `DataWriteIOBytes`, or `MetadataIOBytes`\) for a time period, first multiply the respective sum statistic by 100\. Then divide the result by the sum statistic of `TotalIOBytes` for the same period\.

Suppose that your example logic is this: \(sum of `DataReadIOBytes` x 100 \(to convert to percentage\)\) ÷ sum of `TotalIOBytes`

Then your CloudWatch metric information is the following\.


| ID | Usable metric or metrics | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sum | 1 minute | 
| m2 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sum | 1 minute | 

Your metric math ID and expression are the following\.


| ID | Expression | 
| --- | --- | 
| e1 | \(m2\*100\)/m1 | 

## Metric Math: Percentage of permitted throughput utilization<a name="metric-math-throughput-utilization"></a>

To calculate the percentage of permitted throughput utilization \(`TotalIOBytes`\) for a time period, first multiply the throughput in MiB/second by 100\. Then divide the result by the sum statistic of `PermittedThroughput` converted to MiB for the same period\.

Suppose that your example logic is this: \(metric math expression for throughput in MiB/second x 100 \(to convert to percentage\)\) ÷ \(sum of `PermittedThroughput` ÷ 1,048,576 \(to convert bytes to MiB\)\)

Then your CloudWatch metric information is the following\.


| ID | Usable metric or metrics | Statistic | Period | 
| --- | --- | --- | --- | 
| e1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  |  |  | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sum | 1 minute | 

Your metric math ID and expression are the following\.


| ID | Expression | 
| --- | --- | 
| e2 | \(e1\*100\)/\(m1/1048576\) | 

## Metric math: Throughput IOPS<a name="metric-math-throughput-iops"></a>

To calculate the average operations per second \(IOPS\) for a time period, divide the sample count statistic \(`DataReadIOBytes`, `DataWriteIOBytes`, `MetadataIOBytes`, or `TotalIOBytes`\) by the number of seconds in the period\.

Suppose that your example logic is this: sample count of `DataWriteIOBytes` ÷ seconds in the period

Then your CloudWatch metric information is the following\.


| ID | Usable metrics | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sample count | 1 minute | 

Your metric math ID and expression are the following\.


| ID | Expression | 
| --- | --- | 
| e1 | m1/PERIOD\(m1\) | 

## Metric math: Percentage of IOPS<a name="metric-math-iops-percent"></a>

To calculate the percentage of IOPS per second of the different I/O types \(`DataReadIOBytes`, `DataWriteIOBytes`, or `MetadataIOBytes`\) for a time period, first multiply the respective sample count statistic by 100\. Then divide that value by the sample count statistic of `TotalIOBytes` for the same period\.

Suppose that your example logic is this: \(sample count of `MetadataIOBytes` x 100 \(to convert to percentage\)\) ÷ sample count of `TotalIOBytes`

Then your CloudWatch metric information is the following\.


| ID | Usable metrics | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sample count | 1 minute | 
| m2 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sample count | 1 minute | 

Your metric math ID and expression are the following\.


| ID | Expression | 
| --- | --- | 
| e1 | \(m2\*100\)/m1 | 

## Metric math: Average I/O size in KiB<a name="metric-math-average-io"></a>

To calculate the average I/O size \(in KiB\) for a period, divide the respective sum statistic for the `DataReadIOBytes`, `DataWriteIOBytes`, or `MetadataIOBytes` metric by the same sample count statistic of that metric\.

Suppose that your example logic is this: \(sum of `DataReadIOBytes` ÷ 1,024 \(to convert to KiB\)\) ÷ sample count of  `DataReadIOBytes`

Then your CloudWatch metric information is the following\.


| ID | Usable metrics | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sum | 1 minute | 
| m2 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sample count | 1 minute | 

Your metric math ID and expression are the following\.


| ID | Expression | 
| --- | --- | 
| e1 | \(m1/1024\)/m2 | 

## Using metric math through an AWS CloudFormation template for Amazon EFS<a name="metric-math-cloudformation-template"></a>

You can also create metric math expressions through AWS CloudFormation templates\. One such template is available for you to download and customize for use from the [Amazon EFS tutorials](https://github.com/aws-samples/amazon-efs-tutorial) on GitHub\. For more information about using AWS CloudFormation templates, see [Working with AWS CloudFormation Templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html) in the *AWS CloudFormation User Guide\.*