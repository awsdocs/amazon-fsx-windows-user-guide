# How to use FSx for Windows File Server metrics<a name="how_to_use_metrics"></a>

There are two primary architectural components of each Amazon FSx file system:
+ The **file server** that serves data to clients accessing the file system\.
+ The **storage volumes** that host the data in your file system\.

FSx for Windows File Server reports metrics in CloudWatch that track the performance and resource utilization for your file system's file server and storage volumes\. The following diagram illustrates an Amazon FSx file system with its architectural components, and the performance and resource CloudWatch metrics available for monitoring\. The key property shown for a set of metrics is the file system property that determines the capacity for those metrics\. Adjusting that property modifies the file system's performance for that set of metrics\.

![\[FSx for Windows File Server reports metrics in CloudWatch that monitor network I/O, file server performance, and storage volume performance.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/file-server-metrics-FSxW.png)

Use the **Monitoring & performance** panel in the Amazon FSx console to view the FSx for Windows File Server CloudWatch metrics described in the following table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/how_to_use_metrics.html)

**Note**  
We recommend that you maintain an average throughput capacity utilization under 50% to ensure that you have enough spare throughput capacity for unexpected spikes in your workload, as well as for any background Windows storage operations \(such as storage synchronization, deduplication, or shadow copies\)\.