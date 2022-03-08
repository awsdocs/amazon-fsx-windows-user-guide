# Troubleshooting data deduplication<a name="data-dedup-ts"></a>

There are a number of potential causes for data deduplication issues, as described in the following section\.

**Topics**
+ [Data deduplication is not working](#data-dedup-not-working)
+ [Deduplication values are unexpectedly set to 0](#data-dedup-stopped)
+ [Space is not freed up on file system after deleting files](#data-dedup-freed-space)

## Data deduplication is not working<a name="data-dedup-not-working"></a>

Using the instructions in our [data deduplication documentation](using-data-dedup.md), run the `Get-FSxDedupStatus` command to view the completion status for the most recent deduplication jobs\. If one or more jobs is failing, you may not see an increase in free storage capacity on your file system\.

The most common reason for deduplication jobs failing is insufficient memory\.
+ Microsoft [recommends](https://docs.microsoft.com/en-us/windows-server/storage/data-deduplication/install-enable#faq) optimally having 1 GB of memory per 1 TB of logical data \(or at a minimum 300 MB \+ 50 MB per 1 TB of logical data\)\. Use the [Amazon FSx performance table](performance.md#performance-table) to determine the memory associated with your file system's throughput capacity and ensure the memory resources are sufficient for the size of your data\.
+ Deduplication jobs are configured with the Windows recommended default of 25% memory allocation, which means that for a file system with 32 GB of memory, 8 GB will be available for deduplication\. The memory allocation is configurable \(using the `Set-FSxDedupSchedule` command with parameter `–Memory`\), but consuming additional memory may impact file system performance\.
+ You can modify the configuration of deduplication jobs to further reduce memory requirements\. For example, you can constrain the optimization to run on specific file types or folders, or set a minimum file size and age for optimization\. We also recommend configuring deduplication jobs to run during idle periods when there is minimal load on your file system\.

You may also see errors if deduplication jobs have insufficient time to complete\. You may need to change the maximum duration of jobs, as described in [Modifying a data deduplication schedule](using-data-dedup.md#set-dedup-sched)\.

If deduplication jobs have been failing for a long period of time, and there have been changes to the data on the file system during this period, subsequent deduplication jobs may require more resources to complete successfully for the first time\.

## Deduplication values are unexpectedly set to 0<a name="data-dedup-stopped"></a>

The values for `SavedSpace` and `OptimizedFilesSavingsRate` are unexpectedly 0 for a file system on which you have configured data deduplication\.

This can occur during the storage optimization process when you increase the file system's storage capacity\. When you increase a file system's storage capacity, Amazon FSx cancels existing data deduplication jobs during the storage optimization process, which migrates data from the old disks to the new, larger disks\. Amazon FSx resumes data deduplication on the file system once the storage optimization job completes\. For more information about increasing storage capacity and storage optimization, see [Managing storage capacity](managing-storage-capacity.md)\.

## Space is not freed up on file system after deleting files<a name="data-dedup-freed-space"></a>

The expected behavior of data deduplication is that if the data that was deleted was something that dedup had saved space on, then the space is not actually freed up on your file system until the garbage collection job runs\.

A practice you may find helpful is to set the schedule to run the garbage collection job right after you delete a large number of files\. After the garbage collection job finishes, you can set the garbage collection schedule back to its original settings\. This ensures you can quickly see the space from your deletions immediately\.

Use the following procedure to set the garbage collection job to run in 5 minutes\.

1. To verify that data deduplication is enabled, use the `Get-FSxDedupStatus` command\. For more information on the command and its expected output, see [Viewing the amount of saved space](using-data-dedup.md#get-dedup-status)\.

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