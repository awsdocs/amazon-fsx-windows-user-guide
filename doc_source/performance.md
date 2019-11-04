# Getting the Best Performance in Amazon FSx for Windows File Server<a name="performance"></a>

Amazon FSx for Windows File Server uses high\-performance SSD storage to provide consistent submillisecond latencies for file operations\.

## Baseline Throughput<a name="fsx-baseline-throughput"></a>

Every Amazon FSx file system has a throughput capacity that you configure when the file system is created\. This throughput capacity determines the speed at which the file server hosting your file system can serve file data\. Higher levels of throughput capacity also come with higher levels of I/O operations per second \(IOPS\) and more memory for caching of data on the file server\.

File\-based workloads are typically spiky, with short, intense periods of high I/O and plenty of idle time between bursts\. To support spiky workloads, Amazon FSx provides a baseline speed associated with each throughput capacity level, and the capability to burst to higher speeds for periods of time\.

A file system can always achieve at least its baseline throughput\. File systems provisioned with smaller levels of throughput capacity are also supplied with burst credits that provide at least 30 minutes of bursting each day\. These burst credits are maintained in a burst credit bucket\. In addition to the credits providing 30 minutes of burst a day, for any periods of time during a day in which your file system is driving less throughput than the baseline, additional burst credits are added to the bucket – enabling your file system to burst for even longer periods of time than 30 minutes each day\.


|  |  |  |  |  |  |  |  |  |  | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |--- |
| Throughput capacity \(MBps\) | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 
| Baseline throughput \(MBps\) | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 
| Maximum burst throughput \(MBps\) | 192 | 192 | 192 | 256 | 438 | 438 | N/A | N/A | N/A | 

**Note**  
These details apply to almost all file system configurations\. However, if configuring high levels of throughput capacity \(file server capacity\) with small amounts of storage, keep in mind that the storage volumes for a file system have a maximum throughput of 750 KBps per GB; for small file systems with high levels of throughput, the storage volume throughput may impact the maximum throughput level your file system can achieve\.

Also note that for SSD based storage, FSx supports 3 IOPS per GB of storage.

## Higher Throughput with Caching<a name="fsx-higher-throughput"></a>

Amazon FSx also provides in\-memory caching on the Windows file server\. Depending on your workload's access patterns, you may observe even higher levels of throughput \(between 600 MBps and 3 GBps\) by benefiting from this server\-side caching\.

## Related Topics<a name="perf-related"></a>
+ [Scaling Out Performance with Shards](scale-out-performance.md)
+ [Scaling Out Read Performance with Read Replicas](scale-out-read.md)
