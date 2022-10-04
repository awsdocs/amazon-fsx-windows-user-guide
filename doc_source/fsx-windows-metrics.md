# Metrics and dimensions<a name="fsx-windows-metrics"></a>

FSx for Windows File Server publishes the following metrics into the AWS/FSx namespace in Amazon CloudWatch for all file systems:
+ `DataReadBytes`
+ `DataWriteBytes`
+ `DataReadOperations`
+ `DataWriteOperations`
+ `MetadataOperations`
+ `FreeStorageCapacity`

FSx for Windows File Server publishes the metrics described in the following into the AWS/FSx namespace in Amazon CloudWatch for file systems configured with a throughput capacity of at least 32 MBps\.

**Topics**
+ [FSx for Windows network I/O metrics](#fsx-networkio-metrics)
+ [FSx for Windows file server metrics](#fsx-file-server-metrics)
+ [FSx for Windows disk I/O metrics](#fsx-diskio-metrics)
+ [FSx for Windows storage volume metrics](#fsx-storage-volume-metrics)
+ [FSx for Windows storage capacity metrics](#fsx-storage-capacity-metrics)
+ [FSx for Windows dimensions](#fsx-dimensions)

## FSx for Windows network I/O metrics<a name="fsx-networkio-metrics"></a>

The `AWS/FSx` namespace includes the following network I/O metrics\.


| Metric | Description | 
| --- | --- | 
| DataReadBytes |  The number of bytes for read operations for clients accessing the file system\. Units: Bytes Valid statistics: `Sum`  | 
| DataWriteBytes |  The number of bytes for write operations for clients accessing the file system\. Units: Bytes Valid statistics: `Sum`  | 
| DataReadOperations |  The number of read operations for clients accessing the file system\. Units: Count Valid statistics: `Sum`  | 
| DataWriteOperations |  The number of write operations for clients accessing the file system\. Units: Count Valid statistics: `Sum`  | 
| MetadataOperations |  The number of metadata operations for clients accessing the file system\. Units: Count Valid statistics: `Sum`  | 
| ClientConnections | The number of active connections between clients and the file server\. Units: Count | 

## FSx for Windows file server metrics<a name="fsx-file-server-metrics"></a>

The `AWS/FSx` namespace includes the following file server metrics\.


| Metric | Description | 
| --- | --- | 
| NetworkThroughputUtilization | The network throughput for clients accessing the file system, as a percentage of the provisioned limit\. Units: Percent | 
| CPUUtilization | The percentage utilization of your file server’s CPU resources\. Units: Percent | 
| MemoryUtilization | The percentage utilization of your file server’s memory resources\. Units: Percent | 
| FileServerDiskThroughputUtilization | The disk throughput between your file server and its storage volumes, as a percentage of the provisioned limit determined by throughput capacity\. Units: Percent | 
| FileServerDiskThroughputBalance | The percentage of available burst credits for disk throughput between your file server and its storage volumes\. Valid for file systems provisioned with throughput capacity of 256 MBps or less\. Units: Percent | 
| FileServerDiskIopsUtilization | The disk IOPS between your file server and storage volumes, as a percentage of the provisioned limit determined by throughput capacity\. Units: Percent | 
| FileServerDiskIopsBalance | The percentage of available burst credits for disk IOPS between your file server and its storage volumes\. Valid for file systems provisioned with throughput capacity of 256 MBps or less\. Units: Percent | 

## FSx for Windows disk I/O metrics<a name="fsx-diskio-metrics"></a>

The `AWS/FSx` namespace includes the following disk I/O metrics\.


| Metric | Description | 
| --- | --- | 
| DiskReadBytes | The number of bytes for read operations that access storage volumes\. Units: Bytes Valid statistics: Sum  | 
| DiskWriteBytes | The number of bytes for write operations that access storage volumes\. Units: Bytes Valid statistics: Sum  | 
| DiskReadOperations |  The number of read operations for the file server accessing storage volumes\. Units: Count Valid statistics: `Sum`  | 
| DiskWriteOperations |  The number of write operations for the file server accessing storage volumes\. Units: Count Valid statistics: `Sum`  | 

## FSx for Windows storage volume metrics<a name="fsx-storage-volume-metrics"></a>

The `AWS/FSx` namespace includes the following storage volume metrics\.


| Metric | Description | 
| --- | --- | 
| DiskThroughputUtilization | \(HDD only\) The disk throughput between your ﬁle server and its storage volumes, as a percentage of the provisioned limit determined by the storage volumes\. Units: Percent | 
| DiskThroughputBalance | \(HDD only\) The percentage of available burst credits for disk throughput for the storage volumes\. Units: Percent | 
| DiskIopsUtilization | \(SSD only\) The disk IOPS between your ﬁle server and storage volumes, as a percentage of the provisioned IOPS limit determined by the storage volumes\. Units: Percent | 

## FSx for Windows storage capacity metrics<a name="fsx-storage-capacity-metrics"></a>

The `AWS/FSx` namespace includes the following storage capacity metrics\.


| Metric | Description | 
| --- | --- | 
| FreeStorageCapacity |  The amount of available storage capacity\. Units: Bytes Valid statistics: `Average`, `Minimum`  | 
| StorageCapacityUtilization | Used physical storage capacity as a percentage of total storage capacity\. Units: Percent | 
| DeduplicationSavedStorage | The amount of storage space saved by data deduplication, if it is enabled\. Units: Bytes | 

## FSx for Windows dimensions<a name="fsx-dimensions"></a>

FSx for Windows File Server metrics use the `FSx` namespace and provide metrics for a single dimension, `FileSystemId`\. You can find a file system's ID using the [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command or the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API command\. A file system ID takes the form of *fs\-0123456789abcdef0*\.