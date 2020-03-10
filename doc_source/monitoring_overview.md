# Monitoring Amazon FSx for Windows File Server<a name="monitoring_overview"></a>

With Amazon FSx for Windows File Server, you can monitor activity for your file systems using Amazon CloudWatch metrics\.

**Topics**
+ [Monitoring with Amazon CloudWatch](#monitoring-cloudwatch)
+ [Amazon FSx for Windows File Server Dimensions](#fsx-dimensions)
+ [How to Use Amazon FSx for Windows File Server Metrics](#how_to_use_metrics)
+ [Accessing CloudWatch Metrics](#accessingmetrics)
+ [Creating CloudWatch Alarms to Monitor Amazon FSx](creating_alarms.md)
+ [Logging Amazon FSx for Windows File Server API Calls with AWS CloudTrail](logging-using-cloudtrail-win.md)

## Monitoring with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor file systems using Amazon CloudWatch, which collects and processes raw data from Amazon FSx for Windows File Server into readable, near real\-time metrics\. These statistics are retained for a period of 15 months, so that you can access historical information and gain a better perspective on how your web application or service is performing\. By default, Amazon FSx for Windows File Server metric data is automatically sent to CloudWatch at 1\-minute periods\. For more information about CloudWatch, see [What Are Amazon CloudWatch, Amazon CloudWatch Events, and Amazon CloudWatch Logs?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

As with Amazon EFS, Amazon S3, and Amazon EBS, Amazon FSx CloudWatch metrics are reported as raw *Bytes*\. Bytes are not rounded to either a decimal or binary multiple of the unit\.

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

Amazon FSx for Windows File Server metrics use the `FSx` namespace and provide metrics for a single dimension, `FileSystemId`\. A file system's ID can be found using the `aws fsx describe-file-systems` AWS CLI command, and it takes the form of *fs\-0123456789abcdef0*\.

## How to Use Amazon FSx for Windows File Server Metrics<a name="how_to_use_metrics"></a>

The metrics reported by Amazon FSx provide information that you can analyze in different ways\. The list following shows some common uses for the metrics\. These are suggestions to get you started, not a comprehensive list\.


| How Do I Determine\.\.\. | Relevant Metrics | 
| --- | --- | 
| My file system's throughput? | SUM\(DataReadBytes \+ DataWriteBytes\)/Period \(in seconds\)  | 
| My file system's IOPS? | Total IOPS = SUM\(DataReadOperations \+ DataWriteOperations \+ MetadataOperations\)/Period \(in seconds\) | 

## Accessing CloudWatch Metrics<a name="accessingmetrics"></a>

You can see Amazon FSx metrics for CloudWatch in the following ways\. 
+ The Amazon FSx console\.
+ The the CloudWatch console\.
+ The CloudWatch CLI \(Command Line Interface\)\.
+ The the CloudWatch API\.

The following procedures show you how to access the metrics using these various tools\.

**Using the Amazon FSx console**

**To view metrics using the Amazon FSx console**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the navigation pane, choose **File systems**, then choose the file system whose metrics you want to view\.

1. Choose **Actions** and choose **View details**\. 

1. On the **Summary** page, choose **Monitoring** to see the metrics for your file system\. 

**Using the CloudWatch console\.**

**To view metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch)\.

1. In the navigation pane, choose **Metrics**\. 

1. Select the **FSx** namespace\.

1. \(Optional\) To view a metric, type its name in the search field\.

1. \(Optional\) To filter by dimension, select **FileSystemId**\.

**Using the CloudWatch CLI**

**To access metrics from the AWS CLI**
+ Use the [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command with the `--namespace "AWS/FSx"` namespace\. For more information, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)\.

**Using the CloudWatch API**

**To access metrics from the CloudWatch API**
+ Call `[GetMetricStatistics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetMetricStatistics.html)`\. For more information, see [Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)\. 