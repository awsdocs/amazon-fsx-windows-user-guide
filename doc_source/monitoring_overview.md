# Monitoring Amazon FSx for Windows File Server<a name="monitoring_overview"></a>

With Amazon FSx for Windows File Server, you can monitor activity for your file systems using Amazon CloudWatch metrics\.

## Monitoring with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor file systems using Amazon CloudWatch, which collects and processes raw data from Amazon FSx for Windows File Server into readable, near real\-time metrics\. These statistics are retained for a period of 15 months, so that you can access historical information and gain a better perspective on how your web application or service is performing\. By default, Amazon FSx for Windows File Server metric data is automatically sent to CloudWatch at 1\-minute periods\. For more information about CloudWatch, see [What Are Amazon CloudWatch, Amazon CloudWatch Events, and Amazon CloudWatch Logs?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring//WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

As with Amazon EFS, Amazon S3, and Amazon EBS, Amazon FSx CloudWatch metrics are reported as raw *Bytes*\. Bytes are not rounded to either a decimal or binary multiple of the unit\.

**Important**  
Amazon CloudWatch metrics are available for Amazon FSx for Windows File Server file systems created after May 1, 2019\. If you want to use CloudWatch on a file system created before May 1, 2019, create a new file system from a recent backup\. To learn more, see [Restoring Backups](using-backups.md#restoring-backups)\.

Amazon FSx for Windows File Server publishes the following metrics into the `AWS/FSx` namespace in CloudWatch\. For each metric, Amazon FSx for Windows File Server emits a data point per file system per minute\.


| Metric | Description | 
| --- | --- | 
| DataReadBytes |  The number of bytes for file system read operations\. The `Sum` statistic is the total number of bytes associated with read operations during the period\. To calculate the average throughput \(Bytes per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Bytes Valid statistics: `Sum`  | 
| DataWriteBytes |  The number of bytes for file system write operations\. The `Sum` statistic is the total number of bytes associated with write operations during the period\. To calculate the average throughput \(Bytes per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Bytes Valid statistics: `Sum`  | 
| DataReadOperations |  The number of read operations\. The `Sum` statistic is the count of read operations during the time period\. To calculate the average number of read operations \(operations per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Count Valid statistics: `Sum`  | 
| DataWriteOperations |  The number of write operations\. The `Sum` statistic is the count of write operations during the time period\. To calculate the average number of write operations \(operations per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Count Valid statistics: `Sum`  | 
| MetadataOperations |  The number of metadata operations\. The `Sum` statistic is the count of metadata operations during the time period\. To calculate the average number of metadata operations \(operations per second\) for a period, divide the `Sum` statistic by the number of seconds in the period\. Units: Count Valid statistics: `Sum`  | 
| FreeStorageCapacity |  The amount of available storage capacity\. Units: Bytes Valid statistics: `Average`, `Minimum`  | 

## Amazon FSx for Windows File Server Dimensions<a name="fsx-dimensions"></a>

Amazon FSx for Windows File Server metrics use the `FSx` namespace and provide metrics for a single dimension, `FileSystemId`\. A file system's ID can be found using the `aws fsx describe-file-systems` AWS CLI command, and it takes the form of *fs\-01234567890123456*\.