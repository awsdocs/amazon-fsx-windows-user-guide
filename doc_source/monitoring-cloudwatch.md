# Monitoring metrics with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor FSx for Windows File Server file systems using Amazon CloudWatch, which collects and processes raw data from FSx for Windows File Server into readable, near real\-time metrics\. These statistics are retained for a period of 15 months, so that you can access historical information and gain perspectives on how your web application or file system is performing\.

FSx for Windows File Server publishes CloudWatch metrics in the following domains:
+ Network I/O metrics measure activity between clients accessing the file system and the file server\.
+ File server metrics measure network throughput utilization, file server CPU and memory, and file server disk throughput and IOPS utilization\.
+ Disk I/O metrics measure activity between the file server and the storage volumes\.
+ Storage volume metrics measure disk throughput utilization for HDD storage volumes, and IOPS utilization for SSD storage volumes\.
+ Storage capacity metrics measure storage usage, including storage savings due to Data Deduplication\.

The following diagram illustrates an FSx for Windows File Server file system, its components, and the metric domains\.

![\[FSx for Windows File Server reports metrics in CloudWatch that monitor network I/O, file server performance, and storage volume performance.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/metrics-overview-FSxW.png)

By default, Amazon FSx for Windows File Server sends metric data to CloudWatch at 1\-minute periods, with the following exceptions that are emitted in 5\-minute intervals:
+ `FileServerDiskThroughputBalance`
+ `FileServerDiskIopsBalance`

For more information about CloudWatch, see [What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

Metrics might not be published for Single\-AZ ﬁle systems during ﬁle system maintenance or infrastructure component replacement, and for Multi\-AZ file systems during failover and failback between the primary and secondary file servers\.

Some Amazon FSx CloudWatch metrics are reported as raw *Bytes*\. Bytes are not rounded to either a decimal or binary multiple of the unit\.

**Topics**
+ [Metrics and dimensions](fsx-windows-metrics.md)
+ [How to use FSx for Windows File Server metrics](how_to_use_metrics.md)
+ [Performance warnings and recommendations](performance-insights-FSxW.md)
+ [Accessing FSx for Windows File Server metrics](accessingmetrics.md)
+ [Creating CloudWatch alarms to monitor Amazon FSx](creating_alarms.md)