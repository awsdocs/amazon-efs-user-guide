# Using Metric Math with Amazon EFS<a name="monitoring-metric-math"></a>

Metric Math enables you to query multiple CloudWatch metrics and use math expressions to create new time series based on these metrics\. You can visualize the resulting time series in the CloudWatch console and add them to dashboards\. For example, using Amazon EFS metrics, you could take the sample count of `DataRead` operations divided by 60 to get the average number of reads on your file system for a given 1 minute period\. For more information on Metric Math, see [Use Metric Math](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html) in the Amazon CloudWatch User Guide\.

Following, you'll find some useful Metric Math expressions for Amazon EFS\.

**Topics**
+ [Metric Math: Throughput in MiB/s](#metric-math-throughput-mib)
+ [Metric Math: Percent Throughput](#metric-math-throughput-percent)
+ [Metric Math: Throughput IOPS](#metric-math-throughput-iops)
+ [Metric Math: Percentage of IOPS](#metric-math-iops-percent)
+ [Metric Math: Average IO Size in KiB](#metric-math-average-io)
+ [Metric Math AWS CloudFormation Template for Amazon EFS](#metric-math-cloudformation-template)

## Metric Math: Throughput in MiB/s<a name="metric-math-throughput-mib"></a>

To calculate the average throughput \(MiB/s\) for a period, choose a sum statistic \(`DataReadIOBytes`, `DataWriteIOBytes`, `MetadataIOBytes`, or `TotalIOBytes`\) and, convert the value to MiB, and divide that by the number of seconds in the period\.

**Example Logic:** – \(sum of `TotalIOBytes` ÷ 1048576 \(to convert to MiB\)\) ÷ seconds in the period


**CloudWatch metrics**  

| Id | Usable Metric\(s\) | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sum | 1 minute | 


**Metric Math expression**  

| Id | Expression | 
| --- | --- | 
| e1 | \(m1/1048576\)/PERIOD\(m1\) | 

## Metric Math: Percent Throughput<a name="metric-math-throughput-percent"></a>

To calculate the percent throughput of the different IO types \(`DataReadIOBytes`, `DataWriteIOBytes`, or  `MetadataIOBytes`\) for a period, multiply the respective sum statistic by 100 and divide by the sum statistic of `TotalIOBytes` for the same period\.

**Example Logic:** – \(sum of `DataReadIOBytes` x 100 \(to convert to percentage\)\) ÷ sum of `TotalIOBytes`


**CloudWatch metrics**  

| Id | Usable Metric\(s\) | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sum | 1 minute | 
| m2 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sum | 1 minute | 


**Metric Math expression**  

| Id | Expression | 
| --- | --- | 
| e1 | \(m2\*100\)/m1 | 

## Metric Math: Throughput IOPS<a name="metric-math-throughput-iops"></a>

To calculate the average operations per second \(IOPS\) for a period, divide the sample count statistic \(`DataReadIOBytes`, `DataWriteIOBytes`, `MetadataIOBytes`, or `TotalIOBytes`\) by the number of seconds in the period\.

**Example Logic:** – sample count of `DataWriteIOBytes` ÷ seconds in the period


**CloudWatch metrics**  

| Id | Usable Metric\(s\) | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sample count | 1 minute | 


**Metric Math expression**  

| Id | Expression | 
| --- | --- | 
| e1 | m1/PERIOD\(m1\) | 

## Metric Math: Percentage of IOPS<a name="metric-math-iops-percent"></a>

To calculate the percentage of IOPS per second of the different IO types \(`DataReadIOBytes`, `DataWriteIOBytes`, or `MetadataIOBytes`\) for a period, multiply the respective sample count statistic by 100 and then divide that value by the sample count statistic of `TotalIOBytes` for the same period\.

**Example Logic:** – \(sample count of `MetadataIOBytes` x 100 \(to convert to percentage\)\) ÷ sample count of `TotalIOBytes`


**CloudWatch metrics**  

| Id | Usable Metric\(s\) | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sample count | 1 minute | 
| m2 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sample count | 1 minute | 


**Metric Math expression**  

| Id | Expression | 
| --- | --- | 
| e1 | \(m2\*100\)/m1 | 

## Metric Math: Average IO Size in KiB<a name="metric-math-average-io"></a>

To calculate the average IO size \(in KiB\) for a period, divide the respective sum statistic for the `DataReadIOBytes`, `DataWriteIOBytes`, or `MetadataIOBytes` metric by the same sample count statistic of that metric\.

**Example Logic:** – \(sum of `DataReadIOBytes` ÷ 1024 \(to convert to KiB\)\) ÷ sample count of  `DataReadIOBytes`


**CloudWatch metrics**  

| Id | Usable Metric\(s\) | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sum | 1 minute | 
| m2 | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/monitoring-metric-math.html)  | sample count | 1 minute | 


**Metric Math expression**  

| Id | Expression | 
| --- | --- | 
| e1 | \(m1/1024\)/m2 | 

## Metric Math AWS CloudFormation Template for Amazon EFS<a name="metric-math-cloudformation-template"></a>

Metric Math expressions can also be created through AWS CloudFormation templates\. One such template is available for you to download and customize for use from the [Amazon EFS tutorials](https://github.com/aws-samples/amazon-efs-tutorial) on GitHub\. For more information about using AWS CloudFormation templates, see [Working with AWS CloudFormation Templates](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html) in the AWS CloudFormation User Guide\.