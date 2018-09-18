# On\-Premises Performance Considerations<a name="performance-onpremises"></a>

The Bursting Throughput model for Amazon EFS file systems remains the same whether accessed from your on\-premises servers or your Amazon EC2 instances\. However, when accessing Amazon EFS file data from your on\-premises servers, the maximum throughput is also constrained by the bandwidth of the AWS Direct Connect connection\.

Because of the propagation delay tied to data traveling over long distances, the network latency of an AWS Direct Connect connection between your on\-premises data center and your Amazon VPC can be tens of milliseconds\. If your file operations are serialized, the latency of the AWS Direct Connect connection directly impacts your read and write throughput\. In essence, the volume of data you can read or write during a period of time is bounded by the amount of time it takes for each read and write operation to complete\. To maximize your throughput, parallelize your file operations so that multiple reads and writes are processed by Amazon EFS concurrently\. Standard tools like [GNU parallel](https://www.gnu.org/software/parallel/) enable you to parallelize the copying of file data\.

## Architecting for High Availability<a name="onpremises-availability"></a>

To ensure continuous availability between your on\-premises data center and your Amazon VPC, we recommend configuring two AWS Direct Connect connections\. For more information, see [Step 4: Configure Redundant Connections with AWS Direct Connect](https://docs.aws.amazon.com/directconnect/latest/UserGuide/getstarted.html#RedundantConnections) in the AWS Direct Connect User Guide\.

To ensure continuous availability between your application and Amazon EFS, we recommend that your application be designed to recover from potential connection interruptions\. In general, there are two scenarios for on\-premises applications connected to an Amazon EFS file system; highly available and not highly available\.

In the first, your application is highly available \(HA\) and uses multiple on\-premises servers in its HA cluster\. In this case, ensure that each on\-premises server in the HA cluster connects to a mount target in a different Availability Zone \(AZ\) in your Amazon VPC\. If your on\-premises server can't access the mount target because the AZ in which the mount target exists becomes unavailable, your application should fail over to a server with an available mount target\.

In the second, your application is not highly available, and your on\-premises server can't access the mount target because the AZ in which the mount target exists becomes unavailable\. In this case, your application should implement restart logic and connect to a mount target in a different AZ\.