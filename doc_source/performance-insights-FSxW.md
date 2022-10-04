# Performance warnings and recommendations<a name="performance-insights-FSxW"></a>

FSx for Windows provides you with performance warnings for file systems configured with a throughput capacity of at least 32 MBps\. Amazon FSx displays a warning for a set of the CloudWatch metrics whenever one of these metrics has approached or crossed a predetermined threshold for multiple consecutive data points\. These warnings provide you with actionable recommendations that you can use to optimize your file system's performance\.

Warnings are accessible in several areas of the **Monitoring & performance** dashboard\. All active or recent Amazon FSx performance warnings and any CloudWatch alarms configured for the file system that are in an ALARM state appear in the **Monitoring & performance** panel in the **Summary** section\. The warning also appears in the section of the dashboard that the metric graph is displayed\.

You can create CloudWatch alarms for any of the Amazon FSx metrics\. For more information, see [Creating CloudWatch alarms to monitor Amazon FSx](creating_alarms.md)\.

## Use performance warnings to improve file system performance<a name="resolve-warnings"></a>

Amazon FSx provides actionable recommendations that you can use to optimize your file system's performance\. These recommendations describe how you can address a potential performance bottle neck\. You can take the recommended action if you expect the activity to continue, or if it's causing an impact to your file system's performance\. Depending on which metric has triggered a warning, you can resolve it by increasing either the file system's throughput capacity or storage capacity, as described in the following table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/performance-insights-FSxW.html)

**Note**  
Certain file system events can consume disk I/O performance resources and potentially trigger performance warnings\. For example:  
The optimization phase of storage capacity scaling can generate increased disk throughput, as described in [Storage capacity increases and file system performance](managing-storage-capacity.md#storage-capacity-increase-and-performance)
For Multi\-AZ file systems, events such as throughput capacity scaling, hardware replacement, or Availability Zone disruption result in automatic failover and failback events\. Any data changes that occur during this time need to be synchronized between the primary and secondary file servers, and Windows Server runs a data synchronization job that can consume disk I/O resources\. For more information, see [Managing throughput capacity](managing-throughput-capacity.md)\.

For more information file system performance, see [FSx for Windows File Server performancePerformance](performance.md)\.