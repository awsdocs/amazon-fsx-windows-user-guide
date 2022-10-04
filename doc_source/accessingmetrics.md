# Accessing FSx for Windows File Server metrics<a name="accessingmetrics"></a>

You can see Amazon FSx metrics for CloudWatch in the following ways\.
+ The Amazon FSx console\.
+ The CloudWatch console\.
+ The CloudWatch CLI \(command line interface\)\.
+ The CloudWatch API\.

The following procedures describe how to access your file system's metrics using these various tools\.

**To view file system metrics using the Amazon FSx console**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. To display the **File system details** page, choose **File systems** in the navigation pane\.

1. Choose the file system whose metrics you want to view\.

1. To view graphs of the file system's metrics, choose **Monitoring & performance** on the second panel\.  
![\[Screen capture image of the Performance & monitoring panel in the Amazon FSx console.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/FSxW-monitor-perf-panel.png)
   + The **Summary** metrics are displayed by default, showing any active warnings and CloudWatch alarms along with **File system activity** metrics\.
   + Choose **Storage** to view storage capacity and utilization metrics\.
   + Choose **Performance** to view file server and storage performance metrics
   + Choose **CloudWatch alarms** to view graphs of any alarms configured for the file system\.

   For more information, see [How to use FSx for Windows File Server metrics](how_to_use_metrics.md)

**To view metrics in the CloudWatch console**

1. To view a file system metric in the **Metrics** page of the Amazon CloudWatch console, navigate to the metric in the **Monitoring & performance** panel of the Amazon FSx console\.

1. Choose **View in metrics** from the actions menu in the upper right of the metric graph, as shown in the following image\.  
![\[Image of the Total IOPS metric graphic, with the graph actions menu displayed to show the View in metrics action.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/view-metric-in-CW-fsxW.png)

   This opens the **Metrics** page in the CloudWatch console, showing the metric graph, as shown in the following image\.  
![\[Image of the Total IOPS metric graphic shown in the CloudWatch console.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/metric-in-CW-console.png)

**To add metrics to a CloudWatch dashboard**

1. To add a set of FSx for Windows file system metrics to a dashboard in the CloudWatch console, choose the set of metrics \(**Summary**, **Storage**, or **Performance**\) in the **Monitoring & performance** panel of the Amazon FSx console\.

1. Choose **Add to dashboard** in the upper right of the panel, this opens the CloudWatch console\.

1. Select an existing CloudWatch dashboard from the list, or create a new dashboard\. For more information, see [Using Amazon CloudWatch dashboards](https://docs.aws.amazon.com/latest/monitoring/CloudWatch_Dashboards.html) in the *Amazon CloudWatch User Guide*\.

**To access metrics from the AWS CLI**
+ Use the [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command with the `--namespace "AWS/FSx"` namespace\. For more information, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)\.

**Using the CloudWatch API**

**To access metrics from the CloudWatch API**
+ Call `[GetMetricStatistics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetMetricStatistics.html)`\. For more information, see [Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)\. 