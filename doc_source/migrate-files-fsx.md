# Migrating existing file storage to FSx for Windows File Server<a name="migrate-files-fsx"></a>

To migrate your existing files to FSx for Windows File Server file systems, we recommend using AWS DataSync, an online data transfer service designed to simplify, automate, and accelerate copying large amounts of data to and from AWS storage services\. DataSync copies data over the internet or AWS Direct Connect\. As a fully managed service, DataSync removes much of the need to modify applications, develop scripts, or manage infrastructure\. For more information, see [Migrating existing files to FSx for Windows File Server using AWS DataSync](migrate-files-to-fsx-datasync.md)\.

As an alternative solution, you can use Robust File Copy, or Robocopy, which is a command line directory and file replication command set for Microsoft Windows\. For detailed procedures on how to use Robocopy to migrate file storage to FSx for Windows File Server, see [Migrating existing files to FSx for Windows File Server using Robocopy](migrate-files-to-fsx.md)\.

## Best practices for migrating existing file storage to FSx for Windows File Server<a name="migrate-best-practices"></a>

To migrate large amounts of data to FSx for Windows File Server as quickly as possible, use Amazon FSx file systems configured with solid state drive \(SSD\) storage\. After the migration is complete, you can move the data to Amazon FSx file systems using hard disk drive \(HDD\) storage if that is the best solution for your application\. To move data from an Amazon FSx file system using SDD storage to HDD storage you do the following:
+ Take a backup of your SSD file system\. For more information, see [Creating user\-initiated backups](using-backups.md#creating-backups)\.
+ Restore the backup to a file system using HDD storage\. For more information, see [Restoring backups](using-backups.md#restoring-backups)\.