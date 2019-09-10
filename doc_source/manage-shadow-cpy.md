# Managing Shadow Copies<a name="manage-shadow-cpy"></a>

 Using the set of custom PowerShell commands defined by Amazon FSx, you can manage all aspects of shadow copies on your Amazon FSx for Windows File Server file systems\. 

**Topics**
+ [Using the Amazon FSx Remote PowerShell](#remote-pwrshell)
+ [Setting Shadow Copy Storage](#shadow-copy-storage)
+ [Deleting Shadow Copy Storage, Schedule, and All Shadow Copies](#remove-fsxshadowstorage)
+ [Creating a Custom Shadow Copy Schedule](#shadow-schedules)
+ [Viewing Your Shadow Copy Schedule](#get-fsxshadowcopy-sched)
+ [Deleting a Shadow Copy Schedule](#remove-fsxshadowcopy-sched)
+ [Creating a Shadow Copy](#new-fsxshadow-copy)
+ [Viewing Existing Shadow Copies](#get-fsxshadow-copies)
+ [Deleting Shadow Copies](#remove-fsxshadow-copies)

## Using the Amazon FSx Remote PowerShell<a name="remote-pwrshell"></a>

The custom remote PowerShell that is provided with Amazon FSx enables limited administrative privileges for a file system for users in the file system administrators group\. To start a remote PowerShell session on your Amazon FSx for Windows File Server file system, you need the following: 
+ You can connect to a Windows compute instance that has network connectivity with your file system\. 
+ You are logged into the Windows compute instance as a member of the file system administrators group\. In AWS Managed Microsoft AD, that group is **AWS Delegated FSx Administrators**\. In your self\-managed Microsoft AD, that group is **Domain Admins** or the custom group that you specified for administration when you created your file system\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\. 
+  The file system's security group inbound rules must allow traffic on port 5985\. 

**To initiate a remote PowerShell session on your file system**

1. Connect to a compute instance that has network connectivity with your file system as a user that is a member of the file system administrators group\. 

1.  Open a Windows PowerShell window on the compute instance\. 

1.  Use the following command to open a remote PowerShell session on your Amazon FSx file system\. Replace `FSxFileSystem-DNS-Name` with the DNS name of file system where you want to configure shadow copies\. 

   ```
   PS C:\Users\delegateadmin> enter-pssession -ComputerName FSxFileSystem-DNS-Name -ConfigurationName FsxRemoteAdmin
   [fs-1234567890abcef12]: PS>
   ```

 You can run Amazon FSx PowerShell commands on your file system using the `Invoke-Command` cmdlet, as described following\. 

 The following example illustrates the syntax required when using `Invoke-Command` cmdlet to run PowerShell commands on an Amazon FSx for Windows File Server file system\. 

```
PS C:\Users\delegateadmin> Invoke-Command -ComputerName FSxFileSystem-DNS-Name -ConfigurationName FSxRemoteAdmin -scriptblock {fsx-command}
```

**To run Amazon FSx PowerShell commands using the Invoke\-Command cmdlet**
+ Use the following command to view all shadow copies on your file system\.

  ```
  PS C:\Users\delegateadmin> Invoke-Command -ComputerName FSxFileSystem-DNS-Name -ConfigurationName FSxRemoteAdmin -scriptblock {Get-FsxShadowCopies}
  FSx Shadow Copies: 5 total
  
  
  Shadow Copy ID : {6E26C5AD-CB8C-491E-86EA-5114BD748DA9}
  Creation Time  : 7/25/2019 7:00:45 AM
  PSComputerName : fs-0123456789abcdef
  RunspaceId     : a2920fe7-628f-4f9c-91c0-a24bf8281808
  
  Shadow Copy ID : {60E87BE9-172A-408B-B9B1-6FAB8DB35D69}
  Creation Time  : 7/25/2019 2:03:17 PM
  PSComputerName : fs-0123456789abcdef
  RunspaceId     : a2920fe7-628f-4f9c-91c0-a24bf8281808
  
  Shadow Copy ID : {454A9F43-136B-4B89-9378-EB519AE25E2B}
  Creation Time  : 7/26/2019 7:00:03 AM
  PSComputerName : fs-0123456789abcdef
  RunspaceId     : a2920fe7-628f-4f9c-91c0-a24bf8281808
  
  Shadow Copy ID : {DC7A8386-23DD-4550-963F-274A88186E4E}
  Creation Time  : 7/26/2019 2:00:02 PM
  PSComputerName : fs-0123456789abcdef
  RunspaceId     : a2920fe7-628f-4f9c-91c0-a24bf8281808
  
  Shadow Copy ID : {F93B97C6-CA25-4AC1-9C19-9C77A61C5024}
  Creation Time  : 7/29/2019 6:00:02 AM
  PSComputerName : fs-0123456789abcdef
  RunspaceId     : a2920fe7-628f-4f9c-91c0-a24bf8281808
  ```

## Setting Shadow Copy Storage<a name="shadow-copy-storage"></a>

 Shadow copies consume storage space on the same file system of which the shadow copies are taken\. When you configure shadow copy storage, you define the maximum amount of storage that shadow copies can consume on the file system using the `Set-FsxShadowStorage` custom PowerShell command\. You specify the maximum size that shadow copies can grow to using the `-Maxsize` or the `-Default` command options\. 

Using `-Maxsize`, you can define shadow copy storage as follows: 
+ In bytes: `Set-FsxShadowStorage -Maxsize 2500000000` 
+ In kilobytes, megabytes, gigabytes, or other units: `Set-FsxShadowStorage -Maxsize (2500MB)` or `Set-FsxShadowStorage -Maxsize (2.5GB)` 
+ As a percentage of the overall storage: `Set-FsxShadowStorage -Maxsize "20%"` 
+ As undefined: `Set-FsxShadowStorage -Maxsize "UNBOUNDED"` 

 Use `-Default` to set shadow storage to use up to 10 percent of the file system: `Set-FsxShadowStorage -Default`\. To learn more about using the default option, see [Setting Up Shadow Copies Using Default Settings](shadow-copies-fsxW.md#setting-up-fsx-shadow-copies)\. 

**To set the amount of shadow copy storage on an Amazon FSx for Windows File Server file system**

1. Connect to a compute instance that has network connectivity with your file system as a user that is a member of the file system administrators group\. In AWS Managed Microsoft AD, that group is **AWS Delegated FSx Administrators**\. In your self\-managed Microsoft AD, that group is **Domain Admins** or the custom group that you specified for administration when you created your file system\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\. 

1.  Open a Windows PowerShell window on the compute instance\. 

1.  Use the following command to open a remote PowerShell session on your Amazon FSx file system\. Replace `FSxFileSystem-DNS-Name` with the DNS name of the file system where you want to configure shadow copies\. 

   ```
   PS C:\Users\delegateadmin> enter-pssession -computername FSxFileSystem-DNS-Name -configurationname fsxremoteadmin
   ```

1. Verify that shadow copy storage is not already configured on the file system using the following command\.

   ```
   [fs-1234567890abcef12]: PS>Get-FsxShadowStorage
   No Fsx Shadow Storage Configured
   ```

1.  Set the amount of shadow storage to 10 percent of the volume using the `-Default` option\. 

   ```
   [fs-1234567890abcef12]: PS>Set-FsxShadowStorage -Default
   FSx Shadow Storage Configuration 
   
   AllocatedSpace UsedSpace     MaxSpace
   -------------- ---------     --------
                0         0  32530536858
   ```

    The output shows the shadow storage configuration, as follows: 
   +  `AllocatedSpace` – the amount of storage on the file system in bytes currently allocated to shadow copies\. Initially, this value is 0\. 
   +  `UsedSpace` – the amount of storage currently used by shadow copies\. Initially, this value is 0\. 
   +  `MaxSpace` `-` the maximum amount of storage space to which shadow storage can grow\. This is the value that you just set\. 

## Deleting Shadow Copy Storage, Schedule, and All Shadow Copies<a name="remove-fsxshadowstorage"></a>

 You can delete your shadow copy configuration, including all existing shadow copies, along with the shadow copy schedule\. At the same time, you can release the shadow copy storage on the file system\. 

To do this, enter the `Remove-FsxShadowStorage` command in a remote PowerShell session on your file system\. For instructions on launching a remote PowerShell session on your file system, see [Using the Amazon FSx Remote PowerShell](#remote-pwrshell)\. 

```
[fs-0123456789abcdef1]PS>Remove-FsxShadowStorage

Confirm
Are you sure you want to perform this action?
Performing the operation "Remove-FsxShadowStorage" on target "Removing all Shadow Copies, Shadow Copy Schedule, and Shadow Storage".
[Y] Yes [A] Yes to All [N] No [L] No to All [?] Help (Default is "Y": Y
FSx Shadow Storage Configuration
Removing Shadow Copy Schedule
Removing Shadow Copies
All shadow copies removed.
Removing Shadow Storage
Shadow Storage removed successfully.
```

## Creating a Custom Shadow Copy Schedule<a name="shadow-schedules"></a>

Shadow copy schedules use scheduled task triggers in Microsoft Windows to specify when shadow copies are automatically taken\. A shadow copy schedule can have multiple triggers, providing you with a lot of scheduling flexibility\. Only one shadow copy schedule can exist at a time\. 

When you run the `Set-FsxShadowCopySchedule` command on a file system, you overwrite any existing shadow copy schedule\. Optionally, you can specify the time zone for a trigger using Windows time zones and the `-TimezoneId` option\. For a list of Windows time zones, see Microsoft's [Default Timezone](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/default-time-zones) documentation or run the following at a Windows command prompt: `tzutil /l`\. To learn more about Windows task triggers, see [Task Triggers](https://docs.microsoft.com/en-us/windows/win32/taskschd/task-triggers) in Microsoft Windows Developer Center documentation\. 

You can also use the `-Default` option to quickly set up a default shadow copy schedule\. To learn more, see [Setting Up Shadow Copies Using Default Settings](shadow-copies-fsxW.md#setting-up-fsx-shadow-copies)\. 

**To create a custom shadow copy schedule**

1. Create a set of Windows scheduled task triggers to define when shadow copies are taken in the shadow copy schedule\. Use the `new-scheduledTaskTrigger` command in a PowerShell on your local machine to set multiple triggers\. 

   This following example creates a custom shadow copy schedule that takes shadow copies every Monday–Friday, at 6:00 AM and at 6:00 PM UTC\. By default, times are in UTC, unless you specify a time zone in the Windows scheduled task triggers you create\. 

   ```
   PS C:\Users\delegateadmin> $trigger1 = new-scheduledTaskTrigger -weekly -DaysOfWeek Monday,Tuesday,Wednesday,Thursday,Friday -at 06:00
   PS C:\Users\delegateadmin> $trigger2 = new-scheduledTaskTrigger -weekly -DaysOfWeek Monday,Tuesday,Wednesday,Thursday,Friday -at 18:00
   ```

1.  Use `invoke-command` to run the `scriptblock` command\. Doing so writes a script that sets the shadow copy schedule with the `new-scheduledTaskTrigger` value that you just created\. Replace *`file-system-Id`* with the ID of the file system on which you're setting the shadow copy schedule\. 

   ```
   PS C:\Users\delegateadmin> invoke-command -ComputerName file-system-Id -ConfigurationName FSxRemoteAdmin -scriptblock {
   ```

1.  Enter the following line at the `>>` prompt to set your shadow copy schedule using the `set-fsxshadowcopyschedule` command\.

   ```
   >> set-fsxshadowcopyschedule -scheduledtasktriggers $Using:trigger1,$Using:trigger2 -Confirm:$false }
   ```

    The response displays the shadow copy schedule that you configured on the file system\. 

   ```
   FSx Shadow Copy Schedule
        
   
   Start Time:    : 2019-07-16T06:00:00+00:00
   Days of Week   : Monday,Tuesday,Wednesday,Thursday,Friday
   WeeksInterval  : 1
   PSComputerName : fs-0123456789abcdef1
   RunspaceId     : 12345678-90ab-cdef-1234-567890abcde1
   
   Start Time:    : 2019-07-16T18:00:00+00:00
   Days of Week   : Monday,Tuesday,Wednesday,Thursday,Friday
   WeeksInterval  : 1
   PSComputerName : fs-0123456789abcdef1
   RunspaceId     : 12345678-90ab-cdef-1234-567890abcdef
   ```

## Viewing Your Shadow Copy Schedule<a name="get-fsxshadowcopy-sched"></a>

To view the existing shadow copy schedule on your file system, enter the following command in a remote PowerShell session on your file system\. For instructions on launching a remote PowerShell session on your file system, see [Using the Amazon FSx Remote PowerShell](#remote-pwrshell)\.

```
[fs-0123456789abcdef1]PS> Get-FsxShadowCopySchedule
FSx Shadow Copy Schedule

Start Time                Days of week                             WeeksInterval
----------                ------------                             -------------
2019-07-16T07:00:00+00:00 Monday,Tuesday,Wednesday,Thursday,Friday             1
2019-07-16T12:00:00+00:00 Monday,Tuesday,Wednesday,Thursday,Friday             1
```

## Deleting a Shadow Copy Schedule<a name="remove-fsxshadowcopy-sched"></a>

To delete the existing shadow copy schedule on your file system, enter the following command in a remote PowerShell session on your file system\. For instructions on launching a remote PowerShell session on your file system, see [Using the Amazon FSx Remote PowerShell](#remote-pwrshell)\.

```
[fs-0123456789abcdef1]PS>Remove-FsxShadowCopySchedule
 
Confirm
Are you sure you want to perform this action?
Performing the operation "Remove-FsxShadowCopySchedule" on target "Removing FSx Shadow Copy Schedule". 
[Y] Yes [A] Yes to All [N] No [L] No to All [?] Help (Default is "Y"): Y
[fs-0123456789abcdef1]PS>
```

## Creating a Shadow Copy<a name="new-fsxshadow-copy"></a>

To manually create a shadow copy, enter the following command in a remote PowerShell session on your file system\. For instructions on launching a remote PowerShell session on your file system, see [Using the Amazon FSx Remote PowerShell](#remote-pwrshell)\.

```
[fs-0123456789abcdef1]PS>New-FsxShadowCopy

Shadow Copy {ABCDEF12-3456-7890-ABCD-EF1234567890} taken successfully
```

## Viewing Existing Shadow Copies<a name="get-fsxshadow-copies"></a>

To view the set of existing shadow copies on your file system, enter the following command in a remote PowerShell session on your file system\. For instructions on launching a remote PowerShell session on your file system, see [Using the Amazon FSx Remote PowerShell](#remote-pwrshell)\. 

```
[fs-0123456789abcdef1]PS>Get-FsxShadowCopies
FSx Shadow Copies: 2 total 

Shadow Copy ID                        Creation Time
--------------                        -----------------
{ABCDEF12-3456-7890-ABCD-EF1234567890} 6/17/2019 7:11:09 AM
{FEDCBA21-6543-0987-0987-EF3214567892} 6/19/2019 11:24:19 AM
```

## Deleting Shadow Copies<a name="remove-fsxshadow-copies"></a>

 You can delete one or more existing shadow copies on your file system using the `Remove-FsxShadowCopies` command in a remote PowerShell session on your file system\. For instructions on launching a remote PowerShell session on your file system, see [Using the Amazon FSx Remote PowerShell](#remote-pwrshell)\. 

Specify which shadow copies to delete by using one of the following required options: 
+  `-Oldest` deletes the oldest shadow copy 
+  `-All` deletes all existing shadow copies 
+  `-ShadowCopyId` deletes a specific shadow copy by ID\. 

 You can use only one option with the command\. An error occurs if you don't specify which shadow copy to delete, if you specify multiple shadow copy IDs, or if you specify an invalid shadow copy ID\. 

To delete the oldest shadow copy on your file system, enter the following command in a remote PowerShell session on your file system\.

```
[fs-0123456789abcdef1]PS>Remove-FsxShadowCopies -Oldest
Confirm
Are you sure you want to perform this action?
Performing the operation "Remove-FSxShadowCopies" on target "Removing oldest shadow copy".
[Y] Yes [A] Yes to All [N] No [L] No to All [?] Help (Default is "Y": Y
Shadow Copy {ABCDEF12-3456-7890-ABCD-EF1234567890} deleted
```

 To delete a specific shadow copy on your file system, enter the following command in a remote PowerShell session on your file system\. 

```
[fs-0123456789abcdef1]PS>Remove-FsxShadowCopies -ShadowCopyId "{ABCDEF12-3456-7890-ABCD-EF1234567890}"
Are you sure you want to perform this action?
Performing the operation "Remove-FSxShadowCopies" on target "Removing shadow copy {ABCDEF12-3456-7890-ABCD-EF1234567890}".
[Y] Yes [A] Yes to All [N] No [L] No to All [?] Help (Default is "Y":>Y
Shadow Copy \\AMZNFSXABCDE123\root\cimv2:Wind32_ShadowCopy.ID{ABCDEF12-3456-7890-ABCD-EF1234567890}".ID deleted.
```