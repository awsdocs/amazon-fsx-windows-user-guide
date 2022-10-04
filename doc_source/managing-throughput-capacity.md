# Managing throughput capacity<a name="managing-throughput-capacity"></a>

 Every FSx for Windows File Server file system has a throughput capacity that is configured when you create the file system\. You can modify your file system's throughput capacity at any time, as needed\. Throughput capacity is one factor that determines the speed at which the file server hosting the file system can serve file data\. Higher levels of throughput capacity also come with higher levels of I/O operations per second \(IOPS\) and more memory for caching of data on the file server\. For more information, see [FSx for Windows File Server performancePerformance](performance.md)\. 

 When you modify your file system's throughput capacity, behind the scenes, Amazon FSx switches out the file system's file server\. For Multi\-AZ file systems, it results in an automatic failover and failback while Amazon FSx switches out the preferred and secondary file servers\. For single\-AZ systems, your file system will be unavailable for a few minutes during throughput capacity scaling\. You are billed for the new amount of throughput capacity once it is available to your file system\.

**Note**  
During a maintenance operation on the back end, system modifications \(such as a modification to your throughput capacity\) may be delayed\. Maintenance can cause these changes to queue up until they are next to be processed\.

**Topics**
+ [When to modify throughput capacity](#when-to-modify-throughput-capacity)
+ [How to modify throughput capacity](#increase-throughput-capacity)
+ [Monitoring throughput capacity changes](#monitoring-throughput-capacity-changes)

## When to modify throughput capacity<a name="when-to-modify-throughput-capacity"></a>

Amazon FSx integrates with Amazon CloudWatch, enabling you to monitor your file system's ongoing throughput usage levels\. The performance \(throughput and IOPS\) that you can drive through your file system depends on your specific workload’s characteristics, in addition to your file system’s throughput capacity, storage capacity, and storage type\. You can use CloudWatch metrics to determine which of these dimensions to change to improve performance\. For more information, see [Monitoring metrics with Amazon CloudWatch](monitoring-cloudwatch.md)\.

For Multi\-AZ file systems, throughput capacity scaling results in an automatic failover and failback while Amazon FSx switches out the preferred and secondary file servers, and any data changes during this time need to be synchronized between file servers\. Your file system will continue to be available during this time, but in order to reduce the duration of data synchronization, we recommend modifying throughput capacity during idle periods when there is minimal load on your file system\.

## How to modify throughput capacity<a name="increase-throughput-capacity"></a>

You can modify a file system's throughput capacity using the Amazon FSx console, the AWS Command Line Interface \(AWS CLI\), or the Amazon FSx API\.

### To modify a file system's throughput capacity \(console\)<a name="increase-throughput-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems**, and choose the Windows file system that you want to increase the throughput capacity for\.

1. For **Actions**, choose **Update throughput**\. Or, in the **Summary** panel, choose **Update** next to the file system's **Throughput capacity**\. 

   The **Update throughput capacity** window appears\.

1. Choose the new value for **Throughput capacity** from the list\.  
![\[Console screen shot showing the Update throughput capacity window\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/update-throughput-capacity-modal.png)

1. Choose **Update** to initiate the throughput capacity update\.
**Note**  
Multi\-AZ file systems fail over and fail back when updating throughput scaling, and are fully available\. Single\-AZ file systems experience a very brief period of unavailability during the update\.

1. You can monitor the update progress on the **File systems** detail page, in the **Updates** tab\.

   You can monitor the progress of the update by using the Amazon FSx console, the AWS CLI, and the API\. For more information, see [Monitoring throughput capacity changes](#monitoring-throughput-capacity-changes)\.

### To modify a file system's throughput capacity \(CLI\)<a name="increase-throughput-console"></a>

To modify a file system's throughput capacity, use the AWS CLI command [update\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html)\. Set the following parameters:
+ `--file-system-id` to the ID of the file system that you are updating\.
+ `ThroughputCapacity` to the desired value to update the file system to\.

You can monitor the progress of the update by using the Amazon FSx console, the AWS CLI, and the API\. For more information, see [Monitoring throughput capacity changes](#monitoring-throughput-capacity-changes)\.

## Monitoring throughput capacity changes<a name="monitoring-throughput-capacity-changes"></a>

You can monitor the progress of a throughput capacity modification using the Amazon FSx console, the API, and the AWS CLI\.

### Monitoring throughput capacity changes in the console<a name="monitor-throughput-action-console"></a>

In the **Updates** tab in the **File system details** window, you can view the 10 most recent update actions for each update action type\.

![\[Console screen shot showing the file system updates window.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/fs-updates-panel.png)

For throughput capacity update actions, you can view the following information\.

****Update type****  
Supported types are **Throughput capacity**, **Storage capacity**, and **Storage optimization**\.

****Target value****  
The desired value to change the file system's throughput capacity to\.

****Status****  
The current status of the update\. For throughput capacity updates, the possible values are as follows:  
+ **Pending** – Amazon FSx has received the update request, but has not started processing it\.
+ **In progress** – Amazon FSx is processing the update request\.
+ **Completed** – The throughput capacity update completed successfully\.
+ **Failed** – The throughput capacity update failed\. Choose the question mark \(**?**\) to see details on why the throughput update failed\.

****Request time****  
The time that Amazon FSx received the update request\.

### Monitoring changes with the AWS CLI and API<a name="monitor-throughput-action-cli-api"></a>

You can view and monitor file system throughput capacity modification requests using the [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) CLI command and the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API action\. The `AdministrativeActions` array lists the 10 most recent update actions for each administrative action type\. When you modify a file system's throughput capacity, a `FILE_SYSTEM_UPDATE` administrative action is generated\. 

The following example shows the response excerpt of a `describe-file-systems` CLI command\. The file system has a throughput capacity of 8 MB/s, and the target throughput capacity of 256 MB/s\.

```
.
.
.
    "ThroughputCapacity": 8,
"AdministrativeActions": [
    {
        "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
        "RequestTime": 1581694764.757,
        "Status": "PENDING",
        "TargetFileSystemValues": {
          "WindowsConfiguration": {
            "ThroughputCapacity": 256
          }
        }
    }
]
```

When Amazon FSx completes processing the action successfully, the status changes to `COMPLETED`\. The new throughput capacity is then available to the file system, and shows in the `ThroughputCapacity` property\. This is shown in the following response excerpt of a describe\-file\-systems CLI command\.

```
.
.
.
    "ThroughputCapacity": 256,
"AdministrativeActions": [
    {
        "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
        "RequestTime": 1581694764.757,
        "Status": "COMPLETED",
        "TargetFileSystemValues": {
          "WindowsConfiguration": {
            "ThroughputCapacity": 256
          }
        }
    }
]
```

If the throughput capacity modification fails, the status changes to `FAILED`, and the `FailureDetails` property provides information about the failure\. For information about troubleshooting failed actions, see [Storage or throughput capacity updates fail](admin-actions-ts.md)\.