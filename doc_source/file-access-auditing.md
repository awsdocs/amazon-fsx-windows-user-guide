# File access auditing<a name="file-access-auditing"></a>

Amazon FSx for Windows File Server supports auditing of end\-user accesses on files, folders, and file shares\. You can choose to send the audit event logs to a rich set of other AWS services enabling querying, processing, storing and archiving logs, issuing notifications, and triggering actions to further advance your security and compliance goals\.

**Topics**
+ [File access auditing overview](#faa-overview)
+ [Audit event log destinations](#faa-log-destinations)
+ [Auditing access to files and folders](#faa-audit-controls)
+ [Managing file access auditing](#manage-faa)
+ [Migrating your audit controls](#migrate-faa)
+ [Viewing event logs](#view-faa-logs)

## File access auditing overview<a name="faa-overview"></a>

File access auditing enables you to record end\-user accesses of individual files, folders, and file shares based on your defined audit controls\. Audit controls are also known as NTFS system access control lists \(SACLs\)\. If you already have audit controls set up on your existing file data, you can take advantage of file access auditing by creating a new Amazon FSx for Windows File Server file system and migrating your data\.

Amazon FSx supports the following audit events provided by Windows for file, folder, and file share accesses:
+ For file accesses, it supports: All, Traverse folder / Execute file, List folder / Read data, Read attributes, Create files / Write data, Create folders / Append data, Write attributes, Delete subfolders and files, Delete, Read permissions, Change permissions, and Take ownership\.
+ For file share accesses, it supports: Connect to a file share\.

Across file, folder, and file share accesses, Amazon FSx supports logging of successful attempts \(such as a user with sufficient permissions successfully accessing a file or file share\), failed attempts, or both\.

You can configure whether you want access auditing only on files and folders, only on file shares, or both\. You can also configure which types of accesses should be logged \(successful attempts only, failed attempts only, or both\)\. You can also turn off file access auditing at any time\.

The maximum rate of access audit events supported is 5,000 events per second\. Access audit events are not generated for each file read and write operation, but generated once per file metadata operation, such as when a user creates, opens, or deletes a file\.

## Audit event log destinations<a name="faa-log-destinations"></a>

When enabled, the file access auditing feature must have a configured AWS service to which Amazon FSx sends the audit event logs\. This audit event log destination must be either an Amazon CloudWatch Logs log stream in a CloudWatch Logs log group or an Amazon Kinesis Data Firehose delivery stream\. You can choose the audit event logs destination when you create your Amazon FSx for Windows File Server file system or afterwards by updating it\. For more information, see [Managing file access auditing](#manage-faa)\.

Following are some recommendations that may help you decide which audit event logs destination to choose: 
+ Choose CloudWatch Logs if you want to store, view, and search audit event logs in the Amazon CloudWatch console, run queries on the logs using CloudWatch Logs Insights, and trigger CloudWatch alarms or Lambda functions\.
+ Choose Kinesis Data Firehose if you want to continuously stream events to storage in Amazon S3, to a database in Amazon Redshift, to Amazon OpenSearch Service, or to AWS Partner solutions \(such as Splunk or Datadog\) for further analysis\.

By default, Amazon FSx will create and use a default CloudWatch Logs log group in your account as the audit event log destination\. If you want to use a custom CloudWatch Logs log group or use Kinesis Data Firehose as the audit event log destination, here are the requirements for the names and locations of the audit event log destination:
+ The name of the CloudWatch Logs log group must begin with the `/aws/fsx/` prefix\. If you don't have an existing CloudWatch Logs log group when you create or update a file system on the console, Amazon FSx can create and use a default log stream in the CloudWatch Logs `/aws/fsx/windows` log group\. If you don't want to use the default log group, the configuration UI lets you create a CloudWatch Logs log group when you create or update your file system on the console\.
+ The name of the Kinesis Data Firehose delivery stream must begin with the `aws-fsx-` prefix\. If you don't have an existing Kinesis Data Firehose delivery stream, you can create one when you create or update your file system at the console\.
+ The Kinesis Data Firehose delivery stream must be configured to use `Direct PUT` as its source\. You cannot use an existing Kinesis data stream as a data source for your delivery stream\.
+ The destination \(either CloudWatch Logs log group or Kinesis Data Firehose delivery stream\) must be in the same AWS partition, AWS Region, and AWS account as your Amazon FSx file system\.

You can change the audit event log destination at any time \(for example, from CloudWatch Logs to Kinesis Data Firehose\)\. When you do so, new audit event logs are sent only to the new destination\.

### Best effort audit event log delivery<a name="faa-log-delivery"></a>

Typically, audit event log records are delivered in minutes, but can sometimes take longer\. On very rare occasions, audit event log records might be missed\. If your use case requires particular semantics \(for example, ensuring that no audit events are missed\), we recommend that you account for missed events when designing your workflows\. You can audit for missed events by scanning the file and folder structure on your file system\.

## Auditing access to files and folders<a name="faa-audit-controls"></a>

You need to set audit controls on the files and folders that you want audited for user access attempts\. Audit controls are also known as NTFS system access control lists \(SACLs\)\.

You configure audit controls using the Windows\-native GUI interface or programmatically using Windows PowerShell commands\. If inheritance is enabled, you typically need to set audit controls only on the top\-level folders you want to log accesses for\.

### Using the Windows GUI to set auditing access<a name="faa-gui-interface"></a>

To use a GUI for setting audit controls on your files and folders, use Windows File Explorer\. On a given file or folder, open Windows File Explorer and select the **Properties > Security > Advanced > Auditing** tab\.

The following audit control example audits successful events for a folder\. A Windows event log entry will be emitted whenever that handle is opened for read successfully by the Admin user\. 

![\[\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/faa-audit-control-gui.png)



The **Type** field indicates what actions you want to audit\. Set this field to **Success** to audit successful attempts, **Fail** to audit failed attempts, or **All** to audit both successful and failed attempts\.

For more information on the auditing entry fields, see [ Apply a basic audit policy on a file or folder](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) in the Microsoft documentation\.

### Using PowerShell commands to set auditing access<a name="faa-powershell-commands"></a>

You can use the Microsoft Windows `Set-Acl` command to set the auditing SACL on any file or folder\. For information about this command, see the Microsoft [Set\-Acl](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7.1) documentation\.

Following is an example of using a series of PowerShell commands and variables to set auditing access for successful attempts\. You can adapt these example commands to fit the needs on your file system\.

```
$path = "C:\Users\TestUser\Desktop\DemoTest\"

$ACL = Get-Acl $path

$ACL | Format-List

$AuditUser = "TESTDOMAIN\TestUser"

$AuditRules = "FullControl"

$InheritType = "ContainerInherit,ObjectInherit"

$AuditType = "Success"

$AccessRule = New-Object System.Security.AccessControl.FileSystemAuditRule($AuditUser,$AuditRules,$InheritType,"None",$AuditType)

$ACL.SetAuditRule($AccessRule)

$ACL | Set-Acl $path

Get-Acl $path -Audit | Format-List
```

## Managing file access auditing<a name="manage-faa"></a>

You can enable file access auditing when creating a new Amazon FSx for Windows File Server file system\. File access auditing is turned off by default when you create a file system from the Amazon FSx console\.

On existing file systems that have file access auditing enabled, you can change the file access auditing settings, including changing the access attempt types for file and file share accesses, and the audit event log destination\. You can perform these tasks using the Amazon FSx console, AWS CLI, or API\.

**Note**  
File access auditing is supported only on Amazon FSx for Windows File Server file systems with a throughput capacity of 32 MB/s or greater\. You cannot create or update a file system with a throughput capacity of less than 32 MB/s if file access auditing is enabled\. You can modify the throughput capacity at any time after you create the file system\. For more information, see [Managing throughput capacity](managing-throughput-capacity.md)\.

### To enable file access auditing when creating a file system \(console\)<a name="faa-create-modify-config"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Follow the procedure for creating a new file system described in [Step 1: Create your file system](getting-started-step1.md) in the Getting Started section\. 

1. Open the **Auditing \- optional** section\. File access auditing is disabled by default\.  
![\[\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/faa-create-wizard.png)

1. To enable and configure file access auditing, do the following\.
   + For **Log access to files and folders**, select the logging of successful and/or failed attempts\. Logging is disabled for files and folders if you don't make a selection\.
   + For **Log access to file shares**, select the logging of successful and/or failed attempts\. Logging is disabled for file shares if you don't make a selection\.
   + For **Choose an audit event log destination**, choose **CloudWatch Logs** or **Kinesis Data Firehose**\. Then choose an existing log or delivery stream or create a new one\. For CloudWatch Logs, Amazon FSx can create and use a default log stream in the CloudWatch Logs `/aws/fsx/windows` log group\.

   Following is an example of a file access auditing configuration that will audit successful and failed access attempts of end users for files, folders, and file shares\. The audit event logs will be sent to the default CloudWatch Logs `/aws/fsx/windows` log group destination\.  
![\[\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/faa-create-advanced.png)

1. Continue with the next section of the file system creation wizard\.

When the file system is **Available**, the file access auditing feature is enabled\.

### To enable file access auditing when creating a file system \(CLI\)<a name="w213aac27c15c13b9b3"></a>

1. When creating a new file system, use the `AuditLogConfiguration` property with the [CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html) API operation to enable file access auditing for the new file system\.

   ```
   aws fsx create-file-system \
     --file-system-type WINDOWS \
     --storage-capacity 300 \
     --subnet-ids subnet-123456 \
     --windows-configuration AuditLogConfiguration='{FileAccessAuditLogLevel="SUCCESS_AND_FAILURE", \
       FileShareAccessAuditLogLevel="SUCCESS_AND_FAILURE", \
       AuditLogDestination="arn:aws:logs:us-east-1:123456789012:log-group:/aws/fsx/my-customer-log-group"}'
   ```

1. When the file system is **Available**, the file access auditing feature is enabled\.

### To change the file access auditing configuration \(console\)<a name="w213aac27c15c13b9b5"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems**, and choose the Windows file system that you want to manage file access auditing for\.

1. Choose the **Administration** tab\.

1. On the **File Access Auditing** panel, choose **Manage**\.  
![\[FSx console File access auditing panel, which shows the file access auditing configuration.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/faa-admin-panel.png)

1. On the **Manage file access auditing settings** dialog, change the desired settings\.  
![\[FSx console File access auditing panel, use this panel for modifying the file access auditing configurations.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/faa-update-config.png)
   + For **Log access to files and folders**, select the logging of successful and/or failed attempts\. Logging is disabled for files and folders if you don't make a selection\.
   + For **Log access to file shares**, select the logging of successful and/or failed attempts\. Logging is disabled for file shares if you don't make a selection\.
   + For **Choose an audit event log destination**, choose **CloudWatch Logs** or **Kinesis Data Firehose**\. Then choose an existing log or delivery stream or create a new one\.

1. Choose **Save**\.

### To change the file access auditing configuration \(CLI\)<a name="w213aac27c15c13b9b7"></a>
+ Use the [https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html) CLI command or the equivalent [https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) API operation\.

  ```
  aws fsx update-file-system \
    --file-system-id fs-0123456789abcdef0 \
    --windows-configuration AuditLogConfiguration='{FileAccessAuditLogLevel="SUCCESS_ONLY", \
      FileShareAccessAuditLogLevel="FAILURE_ONLY", \
      AuditLogDestination="arn:aws:logs:us-east-1:123456789012:log-group:/aws/fsx/my-customer-log-group"}'
  ```

## Migrating your audit controls<a name="migrate-faa"></a>

If you have audit controls \(SACLs\) already set up on your existing file data, you can create an Amazon FSx file system and migrate your data to your new file system\. We recommend using AWS DataSync to transfer data and the associated SACLs to your Amazon FSx file system\. As an alternative solution, you can use Robocopy \(Robust File Copy\)\. For more information, see [Migrating existing file storage to Amazon FSx](migrate-to-fsx.md)\.

## Viewing event logs<a name="view-faa-logs"></a>

You can view the audit event logs after Amazon FSx has started emitting them\. Where and how you view the logs depends on the audit event log destination: 
+ You can view CloudWatch Logs logs by going to the CloudWatch console and choosing the log group and log stream to which your audit event logs are sent\. For more information, see [ View log data sent to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html) in the *Amazon CloudWatch Logs User Guide*\. 

  You can use CloudWatch Logs Insights to interactively search and analyze your log data\. For more information, see [ Analyzing Log Data with CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html), in the *Amazon CloudWatch Logs User Guide*\.

  You can also export the audit event logs to Amazon S3\. For more information, see [ Exporting Log Data to Amazon S3](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/S3Export.html), also in the *Amazon CloudWatch Logs User Guide*\.
+ You can't view the audit event logs on Kinesis Data Firehose\. However, you can configure Kinesis Data Firehose to forward the logs to a destination that you can read from\. The destinations include Amazon S3, Amazon Redshift, Amazon OpenSearch Service, and partner solutions such as Splunk and Datadog, For more information, see [Choose destination ](https://docs.aws.amazon.com/firehose/latest/dev/create-destination.html) in the *Amazon Kinesis Data Firehose Developer Guide*\.

### Audit event fields<a name="faa-event-data"></a>

This section provides descriptions of the information in audit event logs and examples of audit events\.

Following are descriptions of the salient fields in a Windows audit event\.
+ **EventID** refers to the Microsoft\-defined Windows event log event ID\. See Microsoft documentation for information on [file system events](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/audit-file-system) and [file share events](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/audit-file-share)\.
+ **SubjectUserName** refers to the user performing the access\.
+ **ObjectName** refers to the target file, folder, or file share that was accessed\.
+ **ShareName** is available for events that are generated for file share access\. For example, `EventID 5140` is generated when a network share object was accessed\.
+ **IpAddress** refers to the client that initiated the event for file share events\.
+ **Keywords**, when available, refer to whether the file access was successful or a failure\. For successful accesses, the value is `0x8020000000000000`\. For failed accesses, the value is `0x8010000000000000`\.
+ **TimeCreated SystemTime** refers to the time the event was generated in the system and shown in <YYYY\-MM\-DDThh:mm:ss\.s>Z format\.
+ **Computer** refers to the DNS name of the file system Windows Remote PowerShell Endpoint and can be used to identify the file system\.
+ **AccessMask**, when available, refers to the type of file access performed \(for example, ReadData, WriteData\)\.
+ **AccessList** refers to requested or granted access to an Object\. For details, see the table below and Microsoft documentation \(such as in [Event 4556](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4656)\)\.


| Access Type | Access Mask | Value | 
| --- | --- | --- | 
|  Read Data or List Directory  |  0x1  |  %%4416  | 
|  Write Data or Add File  |  0x2  |  %%4417  | 
|  Append Data or Add Subdirectory  |  0x4  |  %%4418  | 
|  Read Extended Attributes  |  0x8  |  %%4419  | 
|  Write Extended Attributes  |  0x10  |  %%4420  | 
|  Execute/Traverse  |  0x20  |  %%4421  | 
|  Delete Child  |  0x40  |  %%4422  | 
|  Read Attributes  |  0x80  |  %%4423  | 
|  Write Attributes  |  0x100  |  %%4424  | 
|  Delete  |  0x10000  |  %%1537  | 
|  Read ACL  |  0x20000  |  %%1538  | 
|  Write ACL  |  0x40000  |  %%1539  | 
|  Write Owner  |  0x80000  |  %%1540  | 
|  Synchronize  |  0x100000  |  %%1541  | 
|  Access Security ACL  |  0x1000000  |  %%1542  | 

Following are some key events with examples\. Note that the XML is formatted for readability\.

**Event ID 4660** is logged when an object is deleted\.

```
<Event xmlns='http://schemas.microsoft.com/win/2004/08/events/event'><System>
<Provider Name='Microsoft-Windows-Security-Auditing' Guid='{54849625-5478-4994-A5BA-3E3B0328C30D}'/>
<EventID>4660</EventID><Version>0</Version><Level>0</Level>
<Task>12800</Task><Opcode>0</Opcode>
<Keywords>0x8020000000000000</Keywords><TimeCreated SystemTime='2021-05-18T04:51:56.916563800Z'/>
<EventRecordID>315452</EventRecordID><Correlation/>
<Execution ProcessID='4' ThreadID='5636'/><Channel>Security</Channel>
<Computer>amznfsxgyzohmw8.example.com</Computer><Security/></System><EventData>
<Data Name='SubjectUserSid'>S-1-5-21-658495921-4185342820-3824891517-1113</Data>
<Data Name='SubjectUserName'>Admin</Data><Data Name='SubjectDomainName'>example</Data>
<Data Name='SubjectLogonId'>0x50932f71</Data><Data Name='ObjectServer'>Security</Data>
<Data Name='HandleId'>0x12e0</Data><Data Name='ProcessId'>0x4</Data><Data Name='ProcessName'></Data>
<Data Name='TransactionId'>{00000000-0000-0000-0000-000000000000}</Data></EventData></Event>
```

**Event ID 4659** is logged on a request to delete a file\.

```
<Event xmlns='http://schemas.microsoft.com/win/2004/08/events/event'><System>
<Provider Name='Microsoft-Windows-Security-Auditing' Guid='{54849625-5478-4994-A5BA-3E3B0328C30D}'/>
<EventID>4659</EventID><Version>0</Version><Level>0</Level><Task>12800</Task><Opcode>0</Opcode>
<Keywords>0x8020000000000000</Keywords><TimeCreated SystemTime='2021-0603T19:18:09.951551200Z'/>
<EventRecordID>308888</EventRecordID><Correlation/><Execution ProcessID='4' ThreadID='5540'/>
<Channel>Security</Channel><Computer>amznfsxgyzohmw8.example.com</Computer><Security/></System>
<EventData><Data Name='SubjectUserSid'>S-1-5-21-658495921-4185342820-3824891517-1113</Data>
<Data Name='SubjectUserName'>Admin</Data><Data Name='SubjectDomainName'>example</Data>
<Data Name='SubjectLogonId'>0x2a9a603f</Data><Data Name='ObjectServer'>Security</Data>
<Data Name='ObjectType'>File</Data><Data Name='ObjectName'>\Device\HarddiskVolume8\shar\event.txt</Data>
<Data Name='HandleId'>0x0</Data><Data Name='TransactionId'>{00000000-0000-0000-0000-000000000000}</Data>
<Data Name='AccessList'>%%1537
				%%4423
				</Data><Data Name='AccessMask'>0x10080</Data><Data Name='PrivilegeList'>-</Data>
<Data Name='ProcessId'>0x4</Data></EventData></Event>
```

**Event ID 4663** is logged when a specific operation was performed on the object\. The following example shows reading data from a file, which can be interpreted from `AccessList %%4416`\.

```
<Event xmlns='http://schemas.microsoft.com/win/2004/08/events/event'><System>
<Provider Name='Microsoft-Windows-Security-Auditing' Guid='{54849625-5478-4994-A5BA-3E3B0328C30D}'/>
<EventID>4663< /EventID><Version>1</Version><Level>0</Level><Task>12800</Task><Opcode>0</Opcode>
<Keywords>0x8020000000000000</Keywords><TimeCreated SystemTime='2021-06-03T19:10:13.887145400Z'/>
<EventRecordID>308831</EventRecordID><Correlation/><Execution ProcessID='4' ThreadID='6916'/>
<Channel>Security</Channel><Computer>amznfsxgyzohmw8.example.com</Computer><Security/></System>
<EventData>< Data Name='SubjectUserSid'>S-1-5-21-658495921-4185342820-3824891517-1113< /Data>
<Data Name='SubjectUserName'>Admin</Data><Data Name='SubjectDomainName'>example</Data>
<Data Name='SubjectLogonId'>0x2a9a603f</Data><Data Name='ObjectServer'>Security</Data>
<Data Name='ObjectType'>File</Data><Data Name='ObjectName'>\Device\HarddiskVolume8\share\event.txt</Data>
<Data Name='HandleId'>0x101c</Data><Data Name='AccessList'>%%4416
				</Data>
<Data Name='AccessMask'>0x1</Data><Data Name='ProcessId'>0x4</Data>
<Data Name='ProcessName'></Data><Data Name='ResourceAttributes'>S:AI</Data>
</EventData></Event>
```

The following example shows write/append data from a file, which can be interpreted from `AccessList %%4417`\.

```
<Event xmlns='http://schemas.microsoft.com/win/2004/08/events/event'><System>
<Provider Name='Microsoft-Windows-Security-Auditing' Guid='{54849625-5478-4994-A5BA-3E3B0328C30D}'/>
<EventID>4663</EventID><Version>1</Version><Level>0</Level><Task>12800</Task><Opcode>0</Opcode>
<Keywords>0x8020000000000000</Keywords><TimeCreated SystemTime='2021-06-03T19:12:16.813827100Z'/>
<EventRecordID>308838</EventRecordID><Correlation/><Execution ProcessID='4' ThreadID='5828'/>
<Channel>Security</Channel><Computer>amznfsxgyzohmw8.example.com</Computer><Security/></System>
<EventData><Data Name='SubjectUserSid'>S-1-5-21-658495921-4185342820-3824891517-1113</Data>
<Data Name='SubjectUserName'>Admin</Data><Data Name='SubjectDomainName'>example</Data>
<Data Name='SubjectLogonId'>0x2a9a603f</Data><Data Name='ObjectServer'>Security</Data>
<Data Name='ObjectType'>File</Data><Data Name='ObjectName'>\Device\HarddiskVolume8\share\event.txt</Data>
<Data Name='HandleId'>0xa38</Data><Data Name='AccessList'>%%4417
				</Data><Data Name='AccessMask'>0x2</Data><Data Name='ProcessId'>0x4</Data>
<Data Name='ProcessName'></Data><Data Name='ResourceAttributes'>S:AI</Data></EventData></Event>
```

**Event ID 4656** indicates that a specific access was requested for an object\. In the following example, the Read request was initiated to ObjectName "permtest" and was a failed attempt, as seen in the Keywords value of `0x8010000000000000`\.

```
<Event xmlns='http://schemas.microsoft.com/win/2004/08/events/event'><System>
<Provider Name='Microsoft-Windows-Security-Auditing' Guid='{54849625-5478-4994-A5BA-3E3B0328C30D}'/>
<EventID>4656</EventID><Version>1</Version><Level>0</Level><Task>12800</Task><Opcode>0</Opcode>
<Keywords>0x8010000000000000</Keywords><TimeCreated SystemTime='2021-06-03T19:22:55.113783500Z'/>
<EventRecordID>308919</EventRecordID><Correlation/><Execution ProcessID='4' ThreadID='4924'/>
<Channel>Security</Channel><Computer>amznfsxgyzohmw8.example.com</Computer><Security/></System>
<EventData><Data Name='SubjectUserSid'>S-1-5-21-658495921-4185342820-3824891517-1113</Data>
<Data Name='SubjectUserName'>Admin</Data><Data Name='SubjectDomainName'>example</Data>
<Data Name='SubjectLogonId'>0x2a9a603f</Data><Data Name='ObjectServer'>Security</Data>
<Data Name='ObjectType'>File</Data><Data Name='ObjectName'>\Device\HarddiskVolume8\share\permtest</Data>
<Data Name='HandleId'>0x0</Data><Data Name='TransactionId'>{00000000-0000-0000-0000-000000000000}</Data>
<Data Name='AccessList'>%%1541
				%%4416
				%%4423
				</Data><Data Name='AccessReason'>%%1541:	%%1805
				%%4416:	%%1805
				%%4423:	%%1811	D:(A;OICI;0x1301bf;;;AU)
				</Data><Data Name='AccessMask'>0x100081</Data><Data Name='PrivilegeList'>-</Data>
<Data Name='RestrictedSidCount'>0</Data><Data Name='ProcessId'>0x4</Data><Data Name='ProcessName'></Data>
<Data Name='ResourceAttributes'>-</Data></EventData></Event>
```

**Event ID 4670** is logged when permissions for an object are changed\. The following example shows that user "admin" modified the permission on ObjectName "permtest" to add permissions to SID "S\-1\-5\-21\-658495921\-4185342820\-3824891517\-1113"\. Refer to Microsoft documentation for more information on how to interpret the permissions\.

```
<Event xmlns='http://schemas.microsoft.com/win/2004/08/events/event'><System>
<Provider Name='Microsoft-Windows-Security-Auditing' Guid='{54849625-5478-4994-A5BA-3E3B0328C30D}'/>
<EventID>4670</EventID><Version>0</Version><Level>0</Level>
<Task>13570</Task><Opcode>0</Opcode><Keywords>0x8020000000000000</Keywords>
<TimeCreated SystemTime='2021-06-03T19:39:47.537129500Z'/><EventRecordID>308992</EventRecordID>
<Correlation/><Execution ProcessID='4' ThreadID='2776'/><Channel>Security</Channel>
<Computer>amznfsxgyzohmw8.example.com</Computer><Security/></System><EventData>
<Data Name='SubjectUserSid'>S-1-5-21-658495921-4185342820-3824891517-1113</Data>
<Data Name='SubjectUserName'>Admin</Data><Data Name='SubjectDomainName'>example</Data>
<Data Name='SubjectLogonId'>0x2a9a603f</Data><Data Name='ObjectServer'>Security</Data>
<Data Name='ObjectType'>File</Data><Data Name='ObjectName'>\Device\HarddiskVolume8\share\permtest</Data>
<Data Name='HandleId'>0xcc8</Data>
<Data Name='OldSd'>D:PAI(A;OICI;FA;;;SY)(A;OICI;FA;;;S-1-5-21-658495921-4185342820-3824891517-2622)</Data>
<Data Name='NewSd'>D:PARAI(A;OICI;FA;;;S-1-5-21-658495921-4185342820-3824891517-1113)(A;OICI;FA;;;SY)(A;OICI;FA;;;
S-1-5-21-658495921-4185342820-3824891517-2622)</Data><Data Name='ProcessId'>0x4</Data>
<Data Name='ProcessName'></Data></EventData></Event>
```

**Event ID 5140** is logged every time a file share is accessed\.

```
<Event xmlns='http://schemas.microsoft.com/win/2004/08/events/event'><System>
<Provider Name='Microsoft-Windows-Security-Auditing' Guid='{54849625-5478-4994-A5BA-3E3B0328C30D}'/>
<EventID>5140</EventID><Version>1</Version><Level>0</Level><Task>12808</Task><Opcode>0</Opcode>
<Keywords>0x8020000000000000</Keywords><TimeCreated SystemTime='2021-06-03T19:32:07.535208200Z'/>
<EventRecordID>308947</EventRecordID><Correlation/><Execution ProcessID='4' ThreadID='3120'/>
<Channel>Security</Channel><Computer>amznfsxgyzohmw8.example.com</Computer><Security/></System>
<EventData><Data Name='SubjectUserSid'>S-1-5-21-658495921-4185342820-3824891517-2620</Data>
<Data Name='SubjectUserName'>EC2AMAZ-1GP4HMN$</Data><Data Name='SubjectDomainName'>example</Data>
<Data Name='SubjectLogonId'>0x2d4ca529</Data><Data Name='ObjectType'>File</Data><Data Name='IpAddress'>172.45.6.789</Data>
<Data Name='IpPort'>49730</Data><Data Name='ShareName'>\\AMZNFSXCYDKLDZZ\share</Data>
<Data Name='ShareLocalPath'>\??\D:\share</Data><Data Name='AccessMask'>0x1</Data><Data Name='AccessList'>%%4416
				</Data></EventData></Event>
```

**Event ID 5145** is logged when access is denied at the file share level\. The following example shows access to ShareName "demoshare01" was denied\.

```
<Event xmlns='http://schemas.microsoft.com/win/2004/08/events/event'><System>
<Provider Name='Microsoft-Windows-Security-Auditing' Guid='{54849625-5478-4994-A5BA-3E3B0328C30D}'/>
<EventID>5145</EventID><Version>0</Version><Level>0</Level>
<Task>12811</Task><Opcode>0</Opcode><Keywords>0x8010000000000000</Keywords>
<TimeCreated SystemTime='2021-05-19T22:30:40.485188700Z'/><EventRecordID>282939</EventRecordID>
<Correlation/><Execution ProcessID='4' ThreadID='344'/><Channel>Security</Channel>
<Computer>amznfsxtmn9autz.example.com</Computer><Security/></System><EventData>
<Data Name='SubjectUserSid'>S-1-5-21-658495921-4185342820-3824891517-
1113</Data><Data Name='SubjectUserName'>Admin</Data><Data Name='SubjectDomainName'>example</Data>
<Data Name='SubjectLogonId'>0x95b3fb7</Data><Data Name='ObjectType'>File</Data>
<Data Name='IpAddress'>172.31.7.112</Data><Data Name='IpPort'>59979</Data>
<Data Name='ShareName'>\\AMZNFSXDPNTE0DC\demoshare01</Data><Data Name='ShareLocalPath'>\??\D:\demoshare01</Data>
<Data Name='RelativeTargetName'>Desktop.ini</Data><Data Name='AccessMask'>0x120089</Data>
<Data Name='AccessList'>%%1538 %%1541 %%4416 %%4419 %%4423 </Data><Data Name='AccessReason'>%%1538:
%%1804 %%1541: %%1805 %%4416: %%1805 %%4419: %%1805 %%4423: %%1805 </Data></EventData></Event>
```

If you use CloudWatch Logs Insights to search your log data, you can run queries on the event fields, as shown by the following examples:
+ To query for a specific event ID:

  ```
  fields @message
     | filter @message like /4660/
  ```
+ To query all events matching a particular file name:

  ```
  fields @message
     | filter @message like /event.txt/
  ```

 For more information on the CloudWatch Logs Insights query language, see [ Analyzing Log Data with CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html), in the *Amazon CloudWatch Logs User Guide*\.