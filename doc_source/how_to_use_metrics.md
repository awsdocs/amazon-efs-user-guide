# How do I use Amazon EFS metrics?<a name="how_to_use_metrics"></a>

The metrics reported by Amazon EFS provide information that you can analyze in different ways\. The list below shows some common uses for the metrics\. These are suggestions to get you started, not a comprehensive list\.


| How do I? | Relevant metrics | 
| --- | --- | 
| How can I determine my throughput? | You can monitor the daily `Sum` statistic of the `TotalIOBytes` metric to see your throughput\.  | 
| How can I track the number of Amazon EC2 instances that are connected to a file system? | You can monitor the `Sum` statistic of the `ClientConnections` metric\. To calculate the average `ClientConnections` for periods greater than one minute, divide the sum by the number of minutes in the period\. | 
| How can I see my burst credit balance? | You can see your balance by monitoring the `BurstCreditBalance` metric for your file system\. For more information on bursting and burst credits, see [Throughput Scaling with Bursting Mode](performance.md#bursting)\.  | 