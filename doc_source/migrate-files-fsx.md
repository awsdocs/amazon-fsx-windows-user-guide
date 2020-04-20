# Migrating Existing File Storage to Amazon FSx for Windows File Server<a name="migrate-files-fsx"></a>

Two options are available to migrate your existing files to Amazon FSx for Windows File Server file systems:
+ AWS DataSync service
+ Robust file copy \(robocopy\)

AWS DataSync is an online data transfer service designed to simplify, automate, and accelerate copying large amounts of data to and from AWS storage services\. DataSync copies data over the internet or AWS Direct Connect\. As a fully managed service, DataSync removes much of the need to modify applications, develop scripts, or manage infrastructure\. For more information, see [Migrating Existing Files to Amazon FSx for Windows File Server Using AWS DataSync](migrate-files-to-fsx-datasync.md)\. 

**Note**  
DataSync does not currently support copying NTFS system access control lists \(SACLs\), which are used by administrators to log attempts to access a secured object\. If you need to copy SACLs into your Amazon FSx file system, we recommend that you use Robocopy\. To learn more, see [How DataSync Handles Metadata and Special Files](https://docs.aws.amazon.com/datasync/latest/userguide/special-files.html) in the *AWS DataSync User Guide*\.

Robust File Copy, or Robocopy, is a command\-line directory and file replication command set for Microsoft Windows\. For detailed procedures on how to use Robocopy with Amazon FSx for Windows File Server, see [Migrating Existing Files to Amazon FSx for Windows File Server Using Robocopy](migrate-files-to-fsx.md)\.

## Best Practices for Migrating Existing File Storage to Amazon FSx for Windows File Server<a name="migrate-best-practices"></a>

To migrate large amounts of data to Amazon FSx for Windows File Server as quickly as possible, use Amazon FSx file systems configured with solid state drive \(SSD\) storage\. After the migration is complete, you can move the data to Amazon FSx file systems using hard disk drive \(HDD\) storage if that is the best solution for your application\. To move data from an Amazon FSx file system using SDD storage to HDD storage you do the following:
+ Take a backup of your SSD file system\. For more information, see [Creating Manual Backups](using-backups.md#creating-backups)\.
+ Restore the backup to a file system using HDD storage\. For more information, see [Restoring Backups](using-backups.md#restoring-backups)\.