# Troubleshooting data deduplication<a name="data-dedup-ts"></a>

There are a number of potential causes for data deduplication issues, as described in the following section\.

**Topics**
+ [Data deduplication not working](#data-dedup-stopped)
+ [Space is not freed up on file system after deleting files](#data-dedup-freed-space)

## Data deduplication not working<a name="data-dedup-stopped"></a>

The values for `SavedSpace` and `OptimizedFilesSavingsRate` are unexpectedly 0 for a file system on which you have configured data deduplication\.

This can occur during the storage optimization process when you increase the file system's storage capacity\. When you increase a file system's storage capacity, Amazon FSx cancels existing data deduplication jobs during the storage optimization process, which migrates data from the old disks to the new, larger disks\. Amazon FSx resumes data deduplication on the file system once the storage optimization job completes\. For more information about increasing storage capacity and storage optimization, see [Managing storage capacity](managing-storage-capacity.md)\.

## Space is not freed up on file system after deleting files<a name="data-dedup-freed-space"></a>

The expected behavior of data deduplication is that if the data that was deleted was something that dedup had saved space on, then the space is not actually freed up on your file system until the garabage collection job runs\.

A practice you may find helpful is to set the schedule to run the garbage collection job right after you delete a large number of files\. After the garbage collection job finishes, you can set the garbage collection schedule back to its original settings\. This ensures you can quickly see the space from your deletions immediately\.

Use the following procedure to set the garbage collection job to run in 5 minutes\.

1. To verify that data deduplication is enabled, use the `Get-FSxDedupStatus` command\. For more information on the command and its expected output, see [Viewing the Amount of Saved Space](using-data-dedup.md#get-dedup-status)\.

1. Use the following to set the schedule to run the garbage collection job 5 minutes from now\.

   ```
   $date=get-date
   $DayOfWeek = $date.DayOfWeek
   $Hour = $date.Hour
   $Minute = $date.Minute + 5
   $Time = "${Hour}:${Minute}"
   Invoke-Command -ComputerName ${RPS_ENDPOINT} -ConfigurationName FSxRemoteAdmin -ScriptBlock {   
       Set-FSxDedupSchedule -Name "WeeklyGarbageCollection" -Days $Using:DayOfWeek -Start $Using:Time -DurationHours 9
   }
   ```

1. After the garbage collection job has run and the space has been freed up, set the schedule back to its original settings\.