# Monitoring Amazon EFS<a name="monitoring_overview"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon EFS and your AWS solutions\. You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multi\-point failure if one occurs\. Before you start monitoring Amazon EFS, however, you should create a monitoring plan that includes answers to the following questions:
+ What are your monitoring goals?
+ What resources will you monitor?
+ How often will you monitor these resources?
+ What monitoring tools will you use?
+ Who will perform the monitoring tasks?
+ Who should be notified when something goes wrong?

The next step is to establish a baseline for normal Amazon EFS performance in your environment, by measuring performance at various times and under different load conditions\. As you monitor Amazon EFS, you should consider storing historical monitoring data\. This stored data will give you a baseline to compare against with current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues\.

For example, with Amazon EFS, you can monitor network throughput, I/O for read, write, and/or metadata operations, client connections, and burst credit balances for your file systems\. When performance falls outside your established baseline, you might need to change the size of your file system or the number of connected clients to optimize the file system for your workload\.

To establish a baseline you should, at a minimum, monitor the following items:
+ Your file system's network throughput\.
+ The number of client connections to a file system\.
+ The number of bytes for each file system operation, including data read, data write, and metadata operations\.