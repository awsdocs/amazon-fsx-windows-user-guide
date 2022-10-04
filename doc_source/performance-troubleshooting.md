# Troubleshooting file system performance issues<a name="performance-troubleshooting"></a>

File system performance depends on several factors, including the traffic that you drive to your file system, how you provision your file system, and any features such as Data Deduplication or Shadow Copies that are enabled\. For information about understanding your file system’s performance, see [FSx for Windows File Server performancePerformance](performance.md)\.

**Topics**
+ [How do I determine the throughput and IOPS limits for my file system?](#perf-throughput-IOPS-limits)
+ [What is the difference between network I/O and disk I/O? Why is my network I/O different from my disk I/O?](#perf-whatis-networkio-diskio)
+ [Why is my CPU or memory usage high, even when my network I/O is low?](#why-cpu-mem-high)
+ [What is bursting? How much bursting is my file system using? What happens when burst credits run out?](#what-is-bursting)
+ [I see a warning on the **Monitoring & performance** page – do I need to change my file system’s configuration?](#warnings)
+ [My metrics were temporarily missing, should I be concerned?](#missing-metrics)

## How do I determine the throughput and IOPS limits for my file system?<a name="perf-throughput-IOPS-limits"></a>

To view a file system's throughput and IOPS limits, refer to the [table showing performance levels](performance.md#performance-table) based on the amount of provision throughput capacity\.

## What is the difference between network I/O and disk I/O? Why is my network I/O different from my disk I/O?<a name="perf-whatis-networkio-diskio"></a>

Amazon FSx file systems include one or more file servers that serve data over the network to the clients accessing the file system\. This is the network I/O\. The file server has a fast, in\-memory cache to enhance performance for the most frequently accessed data\. The file servers also drives traffic to the storage volumes that host your file system data\. This is the disk I/O\. The following diagram illustrates network and disk I/O for an Amazon FSx file system\.

![\[FSx for Windows File Server architecture, showing the relationship of file server and storage volumes performance metrics and their impact on file system performance.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/metrics-overview-FSxW.png)

For more information, see [Monitoring metrics with Amazon CloudWatch](monitoring-cloudwatch.md)\.

## Why is my CPU or memory usage high, even when my network I/O is low?<a name="why-cpu-mem-high"></a>

The file server CPU and memory usage depends not only on the network traffic you drive, but also the features you have enabled on your file system\. How you configure and schedule these features can impact CPU and memory utilization\.

Data Deduplication jobs in progress can consume memory\. You can modify the configuration of deduplication jobs to reduce memory requirements\. For example, you can constrain the optimization to run on specific file types or folders, or set a minimum file size and age for optimization\. We also recommend configuring deduplication jobs to run during idle periods when there is minimal load on your file system\. For more information, see [Data deduplication](using-data-dedup.md)\.

If you have access\-based enumeration enabled, you might see high CPU utilization when your end\-users view or list file shares, or during the Optimization phase of a storage scaling job\. For more information, see [ Enable access\-based enumeration on a namespace](https://docs.microsoft.com/en-us/windows-server/storage/dfs-namespaces/enable-access-based-enumeration-on-a-namespace) in the *Microsoft Storage Documentation*\.

## What is bursting? How much bursting is my file system using? What happens when burst credits run out?<a name="what-is-bursting"></a>

File\-based workloads are typically spiky, characterized by short, intense periods of high I/O with idle time between bursts\. To support these types of workloads, in addition to the baseline speeds that a file system can sustain, Amazon FSx provides the capability to burst to higher speeds for periods of time for both network I/O and disk I/O operations\.

Amazon FSx uses a I/O credit mechanism to allocate throughput and IOPS based on average utilization — file systems accrue credits when their throughput and IOPS usage is below their baseline limits, and can use these credits to burst above the baseline limits \(up to the burst limits\) when required\. For more information about the burst limits and duration for your file system, see [FSx for Windows File Server performancePerformance](performance.md)\.

## I see a warning on the **Monitoring & performance** page – do I need to change my file system’s configuration?<a name="warnings"></a>

The **Monitoring & performance** page includes warnings that indicate when recent workload demands have approached or exceeded resource limits determined by how you’ve configured your file system\. This doesn’t necessarily mean you need to change your configuration, though your file system might be under\-provisioned for your workload if you don’t take the recommended action\.

If the workload that caused the warning was atypical and you do not expect it to continue, it may be safe to take no action and closely monitor your utilization going forward\. However, if the workload that caused the warning is typical and you expect it to continue, or even intensify, we advise following the recommended action to increase file server performance \(by increasing throughput capacity\) or increase storage volume performance \(by increasing storage capacity, or by switching from HDD to SSD storage\)\.

**Note**  
Certain file system events can consume disk I/O performance resources and potentially trigger performance warnings\. For example:  
The optimization phase of storage capacity scaling can generate increased disk throughput, as described in [Storage capacity increases and file system performance](managing-storage-capacity.md#storage-capacity-increase-and-performance)
For Multi\-AZ file systems, events such as throughput capacity scaling, hardware replacement, or Availability Zone disruption result in automatic failover and failback events\. Any data changes that occur during this time need to be synchronized between the primary and secondary file servers, and Windows Server runs a data synchronization job that can consume disk I/O resources\. For more information, see [Managing throughput capacity](managing-throughput-capacity.md)\.

## My metrics were temporarily missing, should I be concerned?<a name="missing-metrics"></a>

Single\-AZ file systems will experience unavailability during file system maintenance, infrastructure component replacement, and when an Availability Zone is unavailable\. During these times, metrics will not be available\.

In a Multi\-AZ deployment, Amazon FSx automatically provisions and maintains a standby file server in a different Availability Zone\. If there is file system maintenance or an unplanned service disruption, Amazon FSx automatically fails over to the secondary file server, allowing you to continue accessing your data without manual intervention\. During the brief period in which your file system is failing over and failing back, metrics may be temporarily unavailable\.