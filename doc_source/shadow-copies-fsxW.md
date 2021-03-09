# Working with Shadow Copies<a name="shadow-copies-fsxW"></a>

A Microsoft Windows *shadow copy* is a snapshot of a Windows file system at a point in time\. With shadow copies enabled, your users can easily view and restore individual files or folders from an earlier snapshot in Windows File Explorer\. Doing this enables users to easily undo changes and compare file versions\. Storage administrators using Amazon FSx can easily schedule shadow copies to be taken periodically using Windows PowerShell commands\.

Shadow copies are stored alongside your ﬁle system's data, and therefore consume the ﬁle system's storage capacity\. However, shadow copies consume storage capacity only for the changed portions of ﬁles\. All shadow copies stored in your ﬁle system are included in backups of your ﬁle system\.

**Note**  
Shadow copies are *not* enabled on Amazon FSx for Windows File Server by default\. To have shadow copies running on your file system, you must enable shadow copies and set up a shadow copy schedule on your file system\. For more information, see [Setting Up Shadow Copies Using Default Settings](#setting-up-fsx-shadow-copies)\.

**Note**  
Shadow copies are not a substitute for backups\. If you enable shadow copies, make sure that you continue performing regular backups\.

**Topics**
+ [Shadow Copies Configuration Overview](#shadow-cpy-config-ovrvw)
+ [Setting Up Shadow Copies Using Default Settings](#setting-up-fsx-shadow-copies)
+ [Restoring Individual Files and Folders](#end-user-experience)

## Shadow Copies Configuration Overview<a name="shadow-cpy-config-ovrvw"></a>

You enable and schedule periodic shadow copies on your file system using Windows PowerShell commands defined by Amazon FSx\. Shadow copy configuration contains two settings:
+ The maximum amount of storage that shadow copies can consume on your file system
+ \(Optional\) A schedule to take shadow copies at defined times and intervals, such as daily, weekly, and monthly

You can store up to 512 shadow copies per file system at any point in time\. When you reach this limit, the next shadow copy that you take replaces the oldest shadow copy\. Similarly, when the maximum shadow copy storage amount is reached, one or more of the oldest shadow copies are deleted to make sufficient storage space for the next shadow copy\.

For information about how to quickly enable and schedule periodic shadow copies by using default Amazon FSx settings, see [Setting Up Shadow Copies Using Default Settings](#setting-up-fsx-shadow-copies)\. For information about how to customize your shadow copy configuration, see [Shadow Copies](manage-shadow-cpy.md)\.

### Considerations for Allocating Shadow Copy Storage<a name="shadow-cpy-storage-config"></a>

A shadow copy is a block\-level copy of file changes that were made since the last shadow copy\. The entire file is not copied, only the changes\. Therefore, previous versions of files typically don't take up as much storage space as the current file\. The amount of volume space used for changes can vary according to your workload\. When a file is modified, the storage space used by shadow copies depends on your workload\. When you determine how much storage space to allocate for shadow copies, you should account for your workload's file system usage patterns\.

When you enable shadow copies, you can specify the maximum amount of storage that shadow copies can consume on the file system\. The default limit is 10 percent of your file system\. We recommend that you increase the limit if your users frequently add or modify files\. Setting the limit too small can result in the oldest shadow copies being deleted more often than users might expect\.

You can set the shadow copy storage as unbounded \(`Set-FsxShadowStorage -Maxsize "UNBOUNDED"`\)\. However, an unbounded configuration can result in a large number of shadow copies consuming your file system storage\. This could result in not having enough storage capacity for your workloads\. If you set an unbounded storage, be sure to scale your storage capacity as the shadow copy limits are reached\. For information about configuring your shadow copy storage to a specific size or as unbounded, see [Setting Shadow Copy Storage](manage-shadow-cpy.md#shadow-copy-storage)\.

After you enable shadow copies, you can monitor the amount of storage space consumed by the shadow copies\. For more information, see [Viewing Your Shadow Copy Storage](manage-shadow-cpy.md#get-fsxshadowstorage)\.

### File System Recommendations for Shadow Copies<a name="shadow-cpy-config-recommend"></a>

Following are file system recommendations for using shadow copies\.
+ Make sure you provision sufficient performance capacity for your workload needs on your file system\. Amazon FSx delivers the Shadow Copies feature as provided by Microsoft Windows Server\. By design, Microsoft Windows uses a copy\-on\-write method for recording the changes since the most recent shadow copy point, and this copy\-on\-write activity can result in up to three I/O operations for every file write operation\. If Windows is unable to keep up with the incoming rate of I/O operations per second, it can cause all shadow copies to be deleted because it can no longer maintain the shadow copies via copy\-on\-write\. Therefore, it is important that you provision sufficient I/O performance capacity for your workload needs on your file system \(both the throughput capacity dimension that determines the file server I/O performance, and the storage type and capacity that determine the storage I/O performance\)\.
+ We generally recommend that you use file systems configured with SSD storage rather than HDD storage when you enable shadow copies, given that Windows consumes a higher I/O performance to maintain shadow copies, and given that HDD storage provides lower performance capacity for I/O operations\.
+ Your file system should have at least 320 MB of free space, in addition to the maximum shadow copy storage amount configured \(`MaxSpace`\)\. For example, if you allocated 5 GB `MaxSpace` to shadow copies, your file system should always have at least 320 MB free space in addition to the 5 GB `MaxSpace`\.
+ When configuring your shadow copy schedule, make sure that you don't schedule shadow copies when migrating data or when data deduplication jobs are scheduled to run\. You should schedule shadow copies when you expect your file system to be idle\. For information about configuring a custom shadow copy schedule, see [Creating a Custom Shadow Copy Schedule](manage-shadow-cpy.md#shadow-schedules)\.

## Setting Up Shadow Copies Using Default Settings<a name="setting-up-fsx-shadow-copies"></a>

You can quickly set up shadow copies on your file system by using the default settings available for shadow copy storage and schedule\. The default shadow copy storage setting lets shadow copies consume a maximum of 10 percent of your file system\. If you increase your file system's storage capacity \(either as a percentage or an absolute value\), the amount of the currently allocated shadow copy storage is not similarly increased\.

The default schedule automatically takes shadow copies every Monday, Tuesday, Wednesday, Thursday, and Friday, at 7:00 AM and 12:00 PM UTC\.

**To set up the default level of shadow copy storage**

1. Connect to a Windows compute instance that has network connectivity with your file system\. 

1. Log in to the Windows compute instance as a member of the file system administrators group\. In AWS Managed Microsoft AD, that group is **AWS Delegated FSx Administrators**\. In your self\-managed Microsoft AD, that group is **Domain Admins** or the custom group that you specified for administration when you created your file system\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\. 

1.  Set the default amount of shadow storage using the following command\. Replace `FSxFileSystem-Remote-PowerShell-Endpoint` with the Windows Remote PowerShell endpoint of file system that you want to administer\. You can find the Windows Remote PowerShell endpoint in the Amazon FSx console, in the **Network & Security** section of the file system details screen, or in the response of the `DescribeFileSystem` API operation\. 

   ```
   PS C:\Users\delegateadmin> Invoke-Command -ComputerName FSxFileSystem-Remote-PowerShell-Endpoint -ConfigurationName FSxRemoteAdmin -scriptblock {Set-FsxShadowStorage -Default}
   ```

   The response looks like the following\.

   ```
   FSx Shadow Storage Configuration 
   
   AllocatedSpace UsedSpace     MaxSpace
   -------------- ---------     --------
                0         0  32530536858
   ```

**To create the default shadow copy schedule**
+  Set the default shadow copy schedule by entering the following command\. 

  ```
  PS C:\Users\delegateadmin> Invoke-Command -ComputerName FSxFileSystem-Remote-PowerShell-Endpoint -ConfigurationName FSxRemoteAdmin -scriptblock {Set-FsxShadowCopySchedule -Default}
  ```

   The response displays the default schedule that is now set\. 

  ```
  FSx Shadow Copy Schedule
  
  Start Time                Days of week                             WeeksInterval
  ----------                ------------                             -------------
  2019-07-16T07:00:00+00:00 Monday,Tuesday,Wednesday,Thursday,Friday             1
  2019-07-16T12:00:00+00:00 Monday,Tuesday,Wednesday,Thursday,Friday             1
  ```

 To learn about additional options and creating a custom shadow copy schedule, see [Creating a Custom Shadow Copy Schedule](manage-shadow-cpy.md#shadow-schedules)\. 

## Restoring Individual Files and Folders<a name="end-user-experience"></a>

After you configure shadow copies on your Amazon FSx file system, your users can quickly restore previous versions of individual files or folders\. Doing this enables them to recover deleted or changed files stored on the shared file system\. They do this in a self\-service manner directly on their desktop without administrator assistance\. This self\-service approach increases productivity and reduces administrative workload\. 

Users restore files to previous versions using the familiar Windows File Explorer interface\. To restore a file, you choose the file to restore, then choose **Restore previous versions** from the context \(right\-click\) menu\.

![\[Restore previous versions in Windows File Explore\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/restore-prev-vers.png)

 Users can then view and restore a previous version from the **Previous Versions** list\. 

![\[Restore previous versions in Windows File Explorer\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/rest-list.png)

To learn about the complete set of custom PowerShell commands available for managing shadow copies on your Amazon FSx for Windows File Server shares, see [Shadow Copies](manage-shadow-cpy.md)\.