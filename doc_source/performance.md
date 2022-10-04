# FSx for Windows File Server performance<a name="performance"></a>

FSx for Windows File Server offers file systems to meet a variety of performance needs\. Following is an overview of Amazon FSx file system performance, with a discussion of the available performance and throughput options and useful performance tips\.

**Topics**
+ [Overview](#perf-overview)
+ [Performance details](#performance-details-fsxw)
+ [Impact of throughput capacity on performance](#impact-throughput-cap-performance)
+ [Impact of storage capacity on performance](#storage-capacity-and-performance)
+ [Example: storage capacity and throughput capacity](#throughput-example-fsxw)
+ [Measuring performance using CloudWatch metrics](#measure-performance-cw)
+ [Troubleshooting performance issues](#troubleshoot-performance)

## Overview<a name="perf-overview"></a>

File system performance is measured by its latency, throughput, and I/O operations per second \(IOPS\)\.

### Latency<a name="latency-fsxW"></a>

 FSx for Windows File Server file servers employ a fast, in\-memory cache to achieve consistent sub\-millisecond latencies for actively accessed data\. For data that is not in the in\-memory cache, that is, for file operations that need to be served by performing I/O on the underlying storage volumes, Amazon FSx provides sub\-millisecond file operation latencies with solid state drive \(SSD\) storage, and single\-digit millisecond latencies with hard disk drive \(HDD\) storage\. 

### Throughput and IOPS<a name="throughput-and-iops-fsxw"></a>

 Amazon FSx file systems provide up to multiple GB/s of throughput and hundreds of thousands of IOPS\. The specific amount of throughput and IOPS that your workload can drive on your file system depends on the throughput capacity and storage capacity and storage type of your file system, along with the nature of your workload, including the size of the active working set\. 

### Single\-client performance<a name="single-client-performance"></a>

With Amazon FSx, you can get up to the full throughput and IOPS levels for your file system from a single client accessing it\. Amazon FSx supports *SMB Multichannel*\. This feature enables it to provide up to multiple GB/s throughput and hundreds of thousands of IOPS for a single client accessing your file system\. SMB Multichannel uses multiple network connections between the client and server simultaneously to aggregate network bandwidth for maximal utilization\.

## Performance details<a name="performance-details-fsxw"></a>

To understand the Amazon FSx performance model in detail, you can examine the architectural components of an Amazon FSx file system\. Your client compute instances, whether they exist in AWS or on\-premises, access your file system through an elastic network interface \(ENI\)\. This network interface resides in the Amazon VPC that you associate with your file system\. Behind the file system ENI is the Windows file server that is serving data over the network to the clients accessing the file system\. Amazon FSx provides a fast in\-memory cache on the file server to enhance performance for the most frequently accessed data\. Behind the file server are the storage volumes, or disks, hosting your file system data\. Storage volumes can be hard disk drives \(HDD\) or solid state devices \(SSD\)\.

These components are illustrated in the following diagram\.

![\[FSx for Windows File Server architecture, showing the relationship of file server and storage volumes performance metrics and their impact on file system performance.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/performance-metrics-FSxW.png)

 Corresponding with these architectural components–network interface, file server in\-memory cache, and storage volumes–are the three primary performance characteristics of an FSx for Windows File Server file system that determine the overall throughput and IOPS performance\.
+ Network I/O performance: throughput/IOPS of requests between the clients and the file server \(in aggregate\)
+ File server in\-memory cache size: size of active working dataset that can be accommodated for caching
+ Disk I/O performance: HDD throughput/SSD IOPS of requests between the file server and the storage volumes

There are three factors that determine these performance characteristics for your file system: storage type, storage capacity, and throughput capacity\. Network I/O performance and file server in\-memory cache size performance characteristics are determined by a file system's throughput capacity\. Disk I/O performance is determined by a combination of throughput capacity and storage capacity\.

File\-based workloads are typically spiky, characterized by short, intense periods of high I/O with plenty of idle time between bursts\. To support spiky workloads, in addition to the baseline speeds that a file system can sustain 24/7, Amazon FSx provides the capability to burst to higher speeds for periods of time for both network I/O and disk I/O operations\. Amazon FSx uses a network I/O credit mechanism to allocate throughput and IOPS based on average utilization — file systems accrue credits when their throughput and IOPS usage is below their baseline limits, and can use these credits when they perform I/O operations\. 

## Impact of throughput capacity on performance<a name="impact-throughput-cap-performance"></a>

Every Amazon FSx file system has a throughput capacity that you configure when you create the file system\. Throughput capacity determines file system performance in the following categories:
+ Network throughput – the speed at which the file server can serve file data to clients accessing it\.
+ File server CPU and memory resources that are available for serving file data and performing background activities such as data deduplication and shadow copies\.
+ Disk throughput – the speed at which the file server can support I/O between the file server and the storage volumes\.

Higher levels of throughput capacity come with a higher amount of server CPU capacity and memory for caching data, and higher levels of server disk throughput supported by the file server\. 

 When you create a file system using the Amazon Web Services Management Console, Amazon FSx automatically picks the recommended throughput capacity level for your file system based on the amount of storage capacity you configure\. While the recommended throughput capacity should be sufficient for most workloads, you have the option to override the recommendation and select a specific amount of throughput capacity to meet your application's needs\. You can adjust the amount of throughput capacity up or down at any time after you create it\. For more information, see [Managing throughput capacity](managing-throughput-capacity.md)\. 

The following table shows the full set of specifications for throughput capacity, along with baseline and burst levels, and amount of memory on the file server \(memory that is available for caching, and for performing background activities such as data deduplication and shadow copies\)\. 

**Note**  
The following table shows the set of choices you have for selecting the throughput capacity for your file system while using the Amazon FSx console\. While you can select lower levels \(8 MBps or 16 MBps\) for throughput capacity when you use the Amazon FSx API or CLI, keep in mind that the 8 MBps and 16 MBps levels are meant for test and development workloads, not for production workloads\.


| FSx throughput capacity \(MBps\) | Network throughput capacity \(MBps\) | Network IOPS | Memory \(GB\) | Disk throughput \(MBps\) | Disk IOPS | 
| --- |--- |--- |--- |--- |--- |
| **** | **Baseline** | **Burst \(for a few minutes a day\)** | **** | **** | **Baseline** | **Burst \(for 30 mins a day\)** | **Baseline** | **Burst \(for 30 mins a day\)** | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |
| 32 | 32 | 600 | Thousands | 4 | 32 | 260 | 2K | 12K | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |
| 64 | 64 | 600 | Tens of thousands | 8 | 64 | 350 | 4K | 16K | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |
| 128 | 150 | 1,250 | 8 | 128 | 600 | 6K | 20K | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| 256 | 300 | 1,250 | Hundreds of thousands | 16 | 256 | 600 | 10K | 20K | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |
| 512 | 600 | 1,250 | 32 | 512 |  –  | 20K |  –  | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| 1,024 | 1,500 |  –  | 72 | 1,024 |  –  | 40K |  –  | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| 2,048 | 3,125 |  –  | 144 | 2,048 |  –  | 80K |  –  | 
| --- |--- |--- |--- |--- |--- |--- |--- |

### Choosing the right level of throughput capacity<a name="choosing-throughput"></a>

When you create a file system, Amazon FSx provides you with a recommended throughput capacity level to select based on the storage capacity you’ve selected for your file system\. However, you should adjust this level based on the specific performance requirements of your workload – for example, if your workload requires driving 1GBps of traffic to your file system, you should select a throughput capacity of at least 1,024 MBps\.

You should also consider the features you’re planning to enable on your file system in deciding the level of throughput to configure\. For example, enabling [Shadow Copies](shadow-copies-fsxW.md) may require you to increase your throughput capacity to a level up to three times your expected workload to ensure the file server can maintain the shadow copies with the available I/O performance capacity\. If you are enabling [Data Deduplication](data-dedup-ts.md), you should determine the amount of memory associated with your file system's throughput capacity and ensure this amount of memory is sufficient for the size of your data\.

You can monitor your workload’s utilization of file server performance resources, and get recommendations on the throughput capacity to select, by viewing the **Monitoring & performance > Performance ** tab of your Amazon FSx console\. We recommend testing in a pre\-production environment to ensure the configuration you’ve selected meets your workload’s performance requirements\. For more information, see [Accessing FSx for Windows File Server metrics](accessingmetrics.md)\.

## Impact of storage capacity on performance<a name="storage-capacity-and-performance"></a>

Storage type and the amount of storage capacity impact the storage volume performance of your file system\. You need to configure the type and amount of storage capacity necessary for your file system to deliver the desired performance levels for your workload\.

For HDD storage volumes, Amazon FSx uses a burst bucket model for performance\. Volume size determines the baseline throughput of your volume, which is the rate at which the volume accumulates throughput credits\. Volume size also determines the burst throughput of your volume, which is the rate at which you can spend credits when they are available\. Larger volumes have higher baseline and burst throughput\. The more credits your volume has, the longer it can drive I/O at the burst level\.

The available throughput of an HDD storage volume is expressed by the following formula:

```
(Volume size) × (Credit accumulation rate per TiB) = Throughput
```

For a 1\-TiB HDD volume, burst throughput is limited to 80 MiB/s, the bucket fills with credits at 12 MiB/s, and it can hold up to 1 TiB\-worth of credits\.

The maximum disk throughput and IOPS levels your file system can achieve is the lower of:
+ the disk performance level provided by your file server, based on the throughput capacity you select for your file system
+ the disk performance level provided by the type and amount of storage capacity you select for your file system, either SSD or HDD\.

Your file system's storage capacity provides the following levels of disk throughput and IOPS:


| Storage type | Disk throughput \(Megabytes/second per TiB of storage\) | Disk IOPS \(IOPs per TiB of storage\) | 
| --- | --- | --- | 
| SSD | 750 | 3,000 | 
| HDD | 12 baseline; 80 burst \(up to a max\. of 1 GB/s per file system\)  | 12 baseline; 80 burst | 

You can increase a file system's storage capacity at any time\. For more information, see [Managing storage capacity](managing-storage-capacity.md)\.

### Choosing the right level of storage capacity and the right storage type<a name="choosing-storage-capacity"></a>

FSx for Windows File Server offers solid state drive \(SSD\) and magnetic hard disk drive \(HDD\) storage types\. SSD storage is designed for the highest\-performance and most latency\-sensitive workloads, including databases, media processing workloads, and data analytics applications\. HDD storage is designed for a broad spectrum of workloads, including home directories, user and departmental file shares, and content management systems\.

If you need additional storage, you can increase the storage capacity that is configured on your file system\. To change between SSD and HDD storage types, you can restore from an available backup to create a new file system, and select a new storage type\. For more information, see [Restoring backups](using-backups.md#restoring-backups)\.

You can monitor your workload’s utilization of storage volume performance resources, and get recommendations on the throughput capacity to select, by viewing the **Monitoring & performance > Performance** tab in the Amazon FSx console\. We recommend testing in a pre\-production environment to ensure the configuration you’ve selected meets your workload’s performance requirements\. For more information, see [Accessing FSx for Windows File Server metrics](accessingmetrics.md)\.

## Example: storage capacity and throughput capacity<a name="throughput-example-fsxw"></a>

The following example illustrates how storage capacity and throughput capacity impact file system performance\. 

 A file system that is configured with 2 TiB of HDD storage capacity and 32 MBps of throughput capacity has the following throughput levels:
+ Network throughput – 32 MBps baseline and 600 MBps burst \(see throughput capacity table\)
+ Disk throughput – 24 MBps baseline and 160 MBps burst, which is the lower of:
  + the disk throughput levels of 32 MBps baseline and 260 MBps burst supported by the file server, based on the file system's throughput capacity 
  + the disk throughput levels of 24 MBps baseline \(12 MBps per TB \* 2 TiB\) and 160 MBps burst \(80 MBps per TiB \* 2 TiB\) supported by the storage volumes, based on storage type and capacity

 Your workload accessing the file system will therefore be able to drive up to 32 MBps baseline and 600 MBps burst throughput for file operations performed on actively accessed data cached in the file server in\-memory cache, and up to 24 MBps baseline and 160 MBps burst throughput for file operations that need to go all the way to the disk, for example, due to cache misses\. 

## Measuring performance using CloudWatch metrics<a name="measure-performance-cw"></a>

You can use Amazon CloudWatch to measure and monitor your file system's throughput and IOPS\. For more information, see [Monitoring metrics with Amazon CloudWatch](monitoring-cloudwatch.md)\.

## Troubleshooting performance issues<a name="troubleshoot-performance"></a>

For help in troubleshooting common performance issues, see [Troubleshooting file system performance issues](performance-troubleshooting.md)\.