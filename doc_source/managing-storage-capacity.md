# Managing storage capacity<a name="managing-storage-capacity"></a>

As you need additional storage, you can increase the storage capacity that is configured on your FSx for Windows File Server file system\. You can do so using the Amazon FSx console, the Amazon FSx API, or the AWS Command Line Interface \(AWS CLI\)\. 

**Note**  
You can only *increase* the amount of storage capacity for a file system; you cannot decrease storage capacity\.

**Note**  
Increasing storage capacity is not available for file systems created before June 23, 2019 or file systems restored from a backup belonging to a file system that was created before June 23, 2019\.

When you increase the storage capacity of your Amazon FSx file system, behind the scenes, Amazon FSx adds a new, larger set of disks to your file system\. The new capacity is available for use within minutes\. When the new storage capacity becomes available, you are billed only for the new storage capacity\. 

Amazon FSx runs a storage optimization process in the background to transparently migrate data from the old disks to the new, larger disks\. For most file systems, storage optimization takes a few hours up to a few days, with minimal noticeable impact on your workload performance\. 

The following illustration shows the four main steps of the process that Amazon FSx uses when increasing a file system's storage capacity\.

![\[Diagram of 4 steps: 1. storage capacity increase request, 2. FSx adds new larger disks, 3. FSx migrates data, and 4. FSx removes old disks.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/storage-scaling-flow.png)

You can track the storage optimization progress at any time using the Amazon FSx console, CLI, and API\. For more information, see [Monitoring storage capacity increases](#monitoring-storage-capacity-increase)\.

**Topics**
+ [Important points to know when increasing storage capacity](#storage-capacity-important-to-know)
+ [When to increase storage capacity](#when-to-modify-storage-capacity)
+ [Storage capacity increases and file system performance](#storage-capacity-increase-and-performance)
+ [How to increase storage capacity](#increase-storage-capacity)
+ [Monitoring storage capacity increases](#monitoring-storage-capacity-increase)
+ [Increasing the storage capacity of an FSx for Windows File Server file system dynamically](#automate-storage-capacity-increase)

## Important points to know when increasing storage capacity<a name="storage-capacity-important-to-know"></a>

 Here are a few important items to consider when increasing storage capacity: 
+ **Increase only** – You can only *increase* the amount of storage capacity for a file system; you cannot decrease storage capacity\.
+ **Minimum increase** – Each storage capacity increase must be a minimum of 10 percent of the file system's current storage capacity, up to the maximum allowed value of 65,536 GiB\.
+ **Minimum throughput capacity** – To increase storage capacity, a file system must have a minimum throughput capacity of 16 MB/s\. This is because the storage optimization step is a throughput\-intensive process\.
+ **Time between increases** – You can't make further storage capacity increases on a file system until 6 hours after the last increase was requested, or until the storage optimization process has completed, whichever time is longer\. Storage optimization can take from a few hours up to a few days to complete\. To minimize the time it takes for storage optimization to complete, we recommend increasing your file system's throughput capacity before increasing storage capacity \(the throughput capacity can be scaled back down after storage scaling completes\), and increasing storage capacity when there is minimal traffic on the file system\.

## When to increase storage capacity<a name="when-to-modify-storage-capacity"></a>

Increase your file system's storage capacity when it's running low on free storage capacity\. Use the `FreeStorageCapacity` CloudWatch metric to monitor the amount of free storage available on the file system\. You can create an Amazon CloudWatch alarm on this metric and get notified when it drops below a specific threshold\. For more information, see [Monitoring with Amazon CloudWatch](monitoring-cloudwatch.md)\.

You can automatically increase your file system's storage capacity when the amount free storage capacity falls below a defined threshold you specify\. Use the AWS‐developed custom AWS CloudFormation template to deploy all the components required to implement the automated solution\. For more information, see [Increasing storage capacity dynamically](#automate-storage-capacity-increase)\.

## Storage capacity increases and file system performance<a name="storage-capacity-increase-and-performance"></a>

Most workloads experience minimal performance impact while Amazon FSx runs the storage optimization process in the background after the new storage capacity is available\. Write\-heavy applications with large active datasets could temporarily experience up to a one\-half reduction in the write performance\. For these cases, you can first increase your file system's throughput capacity *before* increasing storage capacity\. This enables you to continue providing the same level of throughput to meet your application’s performance needs\. For more information, see [Managing throughput capacity](managing-throughput-capacity.md)\.

## How to increase storage capacity<a name="increase-storage-capacity"></a>

You can increase a file system's storage capacity using the Amazon FSx console, the AWS CLI, or the Amazon FSx API\.

### To increase storage capacity for a file system \(console\)<a name="increase-storage-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems** and choose the Windows file system that you want to increase storage capacity for\.

1. For **Actions**, choose **Update storage**\. Or, in the **Summary** panel, choose **Update** next to the file system's **Storage capacity**\. 

   The **Update storage capacity** window appears\.  
![\[Console screen shot showing the Update storage capacity pane.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/update-storage-capacity-modal.png)

1. For **Input type**, choose **Percentage** to enter the new storage capacity as a percentage change from the current value, or choose **Absolute** to enter the new value in GiB\.

1. Enter the **Desired storage capacity**\.
**Note**  
The desired capacity value must be at least 10 percent larger than the current value, up to the maximum value of 65,536 GiB\.

1. Choose **Update** to initiate the storage capacity update\.

1. You can monitor the update progress on the **File systems** detail page, in the **Updates** tab\.

### To increase storage capacity for a file system \(CLI\)<a name="increase-storage-console"></a>

To increase the storage capacity for an FSx for Windows File Server file system, use the AWS CLI command [update\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html)\. Set the following parameters:
+ `--file-system-id` to the ID of the file system you are updating\.
+ `--storage-capacity` to a value that is at least 10 percent greater than the current value\.

You can monitor the progress of the update by using the AWS CLI command [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html)\. Look for the `administrative-actions` in the output\. 

For more information, see [AdministrativeAction](https://docs.aws.amazon.com/fsx/latest/APIReference/API_AdministrativeAction.html)\.

## Monitoring storage capacity increases<a name="monitoring-storage-capacity-increase"></a>

You can monitor the progress of a storage capacity increase using the Amazon FSx console, the API, or the AWS CLI\.

### Monitoring increases in the console<a name="monitor-storage-action-console"></a>

In the **Updates** tab in the **File system details** window, you can view the 10 most recent updates for each update type\.

![\[Console screen shot showing recent updates list.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/fs-updates-panel.png)

For storage capacity updates, you can view the following information\.

****Update type****  
Supported types are **Storage capacity**, **Storage optimization**, and **Throughput capacity**\.

****Target value****  
The desired value to update the file system's storage capacity to\.

****Status****  
The current status of the update\. For storage capacity updates, the possible values are as follows:  
+ **Pending** – Amazon FSx has received the update request, but has not started processing it\.
+ **In progress** – Amazon FSx is processing the update request\.
+ **Updated optimizing** – Amazon FSx has increased the file system's storage capacity\. The storage optimization process is now moving the file system data to the new larger disks\.
+ **Completed** – The storage capacity increase completed successfully\.
+ **Failed** – The storage capacity increase failed\. Choose the question mark \(**?**\) to see details on why the storage update failed\.

****Progress %****  
Displays the progress of the storage optimization process as percent complete\.

****Request time****  
The time that Amazon FSx received the update action request\.

### Monitoring increases with the AWS CLI and API<a name="monitor-storage-action-cli-api"></a>

You can view and monitor file system storage capacity increase requests using the [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command and the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API action\. The `AdministrativeActions` array lists the 10 most recent update actions for each administrative action type\. When you increase a file system's storage capacity, two `AdministrativeActions` are generated: a `FILE_SYSTEM_UPDATE` and a `STORAGE_OPTIMIZATION` action\. 

The following example shows an excerpt of the response of a describe\-file\-systems CLI command\. The file system has a storage capacity of 300 GB, and there is a pending administrative action to increase the storage capacity to 1000 GB\.

```
{
    "FileSystems": [
        {
            "OwnerId": "111122223333",
            .
            .
            .
            "StorageCapacity": 300,
            "AdministrativeActions": [
                {
                     "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
                     "RequestTime": 1581694764.757,
                     "Status": "PENDING",
                     "TargetFileSystemValues": {
                         "StorageCapacity": 1000
                     }
                },
                {
                    "AdministrativeActionType": "STORAGE_OPTIMIZATION",
                    "RequestTime": 1581694764.757,
                    "Status": "PENDING",
                }
            ]
```

Amazon FSx processes the `FILE_SYSTEM_UPDATE` action first, adding the new larger storage disks to the file system\. When the new storage is available to the file system, the `FILE_SYSTEM_UPDATE` status changes to `UPDATED_OPTIMIZING`\. The storage capacity shows the new larger value, and Amazon FSx begins processing the `STORAGE_OPTIMIZATION` administrative action\. This is shown in the following excerpt of the response of a describe\-file\-systems CLI command\. 

The `ProgressPercent` property displays the progress of the storage optimization process\. After the storage optimization process completes successfully, the status of the `FILE_SYSTEM_UPDATE` action changes to `COMPLETED`, and the `STORAGE_OPTIMIZATION` action no longer appears\.

```
{
    "FileSystems": [
        {
            "OwnerId": "111122223333",
            .
            .
            .
            "StorageCapacity": 1000,
            "AdministrativeActions": [
                {
                    "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
                    "RequestTime": 1581694764.757,
                    "Status": "UPDATED_OPTIMIZING",
                    "TargetFileSystemValues": {
                        "StorageCapacity": 1000
                }
                },
                {
                    "AdministrativeActionType": "STORAGE_OPTIMIZATION",
                    "RequestTime": 1581694764.757,
                    "Status": "IN_PROGRESS",
                    "ProgressPercent": 50,
                }
            ]
```



 If the storage capacity increase fails, the status of the `FILE_SYSTEM_UPDATE` action changes to `FAILED`\. The `FailureDetails` property provides information about the failure, shown in the following example\.

```
{
    "FileSystems": [ 
        { 
            "OwnerId": "111122223333",
            .
            .
            .
            "StorageCapacity": 300,
            "AdministrativeActions": [ 
                { 
                    "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
                    "FailureDetails": { 
                        "Message": "string"
                    },
                    "RequestTime": 1581694764.757,
                    "Status": "FAILED",
                    "TargetFileSystemValues": 
                        "StorageCapacity": 1000
                }
            ]
```

For information about troubleshooting failed actions, see [Storage or throughput capacity updates fail](admin-actions-ts.md)\.

## Increasing the storage capacity of an FSx for Windows File Server file system dynamically<a name="automate-storage-capacity-increase"></a>

You can use the following solution to dynamically increase the storage capacity of an FSx for Windows File Server file system when the amount of free storage capacity falls below a defined threshold that you specify\. This AWS CloudFormation template automatically deploys all the components that are required to define the free storage capacity threshold, the Amazon CloudWatch alarm based on this threshold, and the AWS Lambda function that increases the file system’s storage capacity\.

The solution automatically deploys all the components needed, and takes in the following parameters:
+ The file system ID
+ The free storage capacity threshold \(numerical value\)
+ Unit of measurement \(percentage \[default\] or GiB\)
+ The percentage by which to increase the storage capacity \(%\)
+ The email address for the SNS subscription
+ Adjust alarm threshold \(Yes/No\)

**Topics**
+ [Architecture overview](#storage-inc-architecture)
+ [AWS CloudFormation template](#storage-capacity-CFN-template)
+ [Automated deployment with AWS CloudFormation](#fsx-dynamic-storage-increase-deployment)

### Architecture overview<a name="storage-inc-architecture"></a>

Deploying this solution builds the following resources in the AWS Cloud\.

![\[Architecture diagram of the solution to automatically increase the storage capacity of an FSx for Windows File Server file system.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/auto-storage-increase-architecture.png)

The diagram illustrates the following steps:

1. The AWS CloudFormation template deploys a CloudWatch alarm, an AWS Lambda function, an Amazon Simple Notification Service \(Amazon SNS\) queue, and all required AWS Identity and Access Management \(IAM\) roles\. The IAM role gives the Lambda function permission to invoke the Amazon FSx API operations\.

1. CloudWatch triggers an alarm when the file system’s free storage capacity goes below the specified threshold, and sends a message to the Amazon SNS queue\.

1. The solution then triggers the Lambda function that is subscribed to this Amazon SNS topic\.

1. The Lambda function calculates the new file system storage capacity based on the specified percent increase value and sets the new file system storage capacity\.

1. The Lambda function can optionally adjust the free storage capacity threshold so that it is equal to a specified percentage of the file system’s new storage capacity\.

1. The original CloudWatch alarm state and results of the Lambda function operations are sent to the Amazon SNS queue\.

To receive notifications about the actions that are performed as a response to the CloudWatch alarm, you must confirm the Amazon SNS topic subscription by following the link provided in the **Subscription Confirmation** email\.

### AWS CloudFormation template<a name="storage-capacity-CFN-template"></a>

This solution uses AWS CloudFormation to automate deploying the components that are used to automatically increase the storage capacity of an FSx for Windows File Server file system\. To use this solution, download the [IncreaseFSxSize](https://s3.amazonaws.com/solution-references/fsx/DynamicScaling/IncreaseFSxSize.yaml) AWS CloudFormation template\.

The template uses the **Parameters** described as follows\. Review the template parameters and their default values, and modify them for the needs of your file system\.



**FileSystemId**  
No default value\. The ID of the file system for which you want to automatically increase the storage capacity\.

**LowFreeDataStorageCapacityThreshold**  
No default value\. Specifies the initial free storage capacity threshold at which to trigger an alarm and automatically increase the file system's storage capacity, specified in GiB or as a percentage \(%\) of the file system's current storage capacity\. When expressed as a percentage, the CloudFormation template re\-calculates to GiB to match the CloudWatch alarm settings\.

**LowFreeDataStorageCapacityThresholdUnit**  
Default is **%**\. Specifies the units for the `LowFreeDataStorageCapacityThreshold`, either in GiB or as a percentage of the current storage capacity\. 

**AlarmModificationNotification**  
Default is **Yes**\. If set to Yes, the initial `LowFreeDataStorageCapacityThreshold`, is increased proportionally to the value of `PercentIncrease` for subsequent alarm thresholds\.  
For example, when `PercentIncrease` is set to 20, and AlarmModificationNotification is set to Yes, the available free space threshold \(`LowFreeDataStorageCapacityThreshold`\) specified in GiB is increased by 20% for subsequent storage capacity increase events\.

**EmailAddress**  
No default value\. Specifies the email address to use for the SNS subscription and will receive the storage capacity threshold alerts\.

**PercentIncrease**  
No default value\. Specifies the amount by which to increase the storage capacity, expressed as a percentage of the current storage capacity\.

### Automated deployment with AWS CloudFormation<a name="fsx-dynamic-storage-increase-deployment"></a>

The following procedure configures and deploys an AWS CloudFormation stack to automatically increase the storage capacity of an FSx for Windows File Server file system\. It takes about 5 minutes to deploy\. 

**Note**  
Implementing this solution incurs billing for the associated AWS services\. For more information, see the pricing details pages for those services\.

Before you start, you must have the ID of the Amazon FSx file system running in an Amazon Virtual Private Cloud \(Amazon VPC\) in your AWS account\. For more information about creating Amazon FSx resources, see [Getting started with Amazon FSx](getting-started.md)\.

**To launch the automatic storage capacity increase solution stack**

1. Download the [IncreaseFSxSize](https://s3.amazonaws.com/solution-references/fsx/DynamicScaling/IncreaseFSxSize.yaml) AWS CloudFormation template\. For more information about creating a CloudFormation stack, see [Creating a stack on the AWS CloudFormation console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in the *AWS CloudFormation User Guide*\.
**Note**  
Amazon FSx is currently only available in specific AWS Regions\. You must launch this solution in an AWS Region where Amazon FSx is available\. For more information, see [Amazon FSx endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/fsxn.html) in the *AWS General Reference*\.

1. In **Specify stack details**, enter the values for your automatic storage capacity increase solution\.  
![\[Screenshot showing the values entered for the Specify stack details page for the CloudFormation template.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/dynamic-storage-capacity-increase-cfn-stack.png)

1. Enter a **Stack name**\.

1. For **Parameters**, review the parameters for the template and modify them for the needs of your file system\. Then choose **Next**\.

1. Enter any **Options** settings that you want for your custom solution, and then choose **Next**\.

1. For **Review**, review and confirm the solution settings\. You must select the check box acknowledging that the template creates IAM resources\.

1. Choose **Create** to deploy the stack\.

You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **CREATE\_COMPLETE** in about 5 minutes\.

#### Updating the stack<a name="automate-storage-capacity-increase-update"></a>

After the stack is created, you can update it by using the same template and providing new values for the parameters\. For more information, see [Updating stacks directly](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-direct.html) in the *AWS CloudFormation User Guide*\.