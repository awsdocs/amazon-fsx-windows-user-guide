# Working with Backups<a name="using-backups"></a>

Creating regular backups for your file system is a best practice that complements the replication that Amazon FSx for Windows File Server performs for your file system\. Amazon FSx backups help support your backup retention and compliance needs\. Working with Amazon FSx backups is easy, whether it's creating backups, restoring a file system from a backup, or deleting a backup\.

With Amazon FSx, backups are file\-system\-consistent, highly durable, and incremental\. To ensure file system consistency, Amazon FSx uses the Volume Shadow Copy Service \(VSS\) in Microsoft Windows\. To ensure high durability, Amazon FSx stores backups in Amazon Simple Storage Service \(Amazon S3\)\. Amazon FSx backups are incremental, which means that only the changes after your most recent backup are saved\. Thus, you can save on backup storage costs by not duplicating data\. Using Amazon FSx, you can create a new file system from a backup, effectively restoring a point\-in\-time snapshot of the file system\.

**Topics**
+ [Working with Automatic Daily Backups](#automatic-backups)
+ [Creating Backups](#creating-backups)
+ [Restoring Backups](#restoring-backups)
+ [Deleting Backups](#delete-backups)
+ [Setting Up a Custom Backup Schedule](custom-backup-schedule.md)

## Working with Automatic Daily Backups<a name="automatic-backups"></a>

Amazon FSx automatically takes backups of your file systems once a day\. These daily backups are taken during the daily backup window that was established when you created the file system\. At some point during the daily backup window, storage I/O might be suspended briefly while the backup process initializes \(typically under a few seconds\)\. When you choose your daily backup window, we recommend that you choose a convenient time of the day\. This time ideally is outside of the normal operating hours for the applications that use the file system\.

Backups are kept for a certain period of time, known as a retention period\. By default, backups are retained for 7 days\. However, you can change the retention period to anywhere in a range of 0–35 days\.

**Note**  
Setting the retention period to 0 days means that your file system is never automatically backed up\. We highly recommend that you use automatic backups for file systems that have any level of critical functionality associated with them\.

You can use the AWS CLI or one of the AWS SDKs to change the backup window, and backup retention period for your file systems with the `UpdateFileSystem` operation\. For more information, see [Walkthrough 3: Update an Existing File System](walkthrough03-update-file-system.md)\.

## Creating Backups<a name="creating-backups"></a>

Amazon FSx enables you to take additional backups of your file systems at any time\. You can do so using the Amazon FSx Management Console, the AWS CLI, or one of the AWS SDKs\. The easiest way to create a backup is through the console\. The following procedure guides you through how to create a user\-initiated backup in the console for a file system that already exists\.

**To create a file system backup**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the console dashboard, choose the name of the file system that you want to back up\.

1. From **Actions**, choose **Create backup**\.

1. In the **Create backup** dialog box that opens, provide a name for your backup\. Backup names can be a maximum of 256 Unicode characters, including letters, white space, numbers, and the special characters \. \+ \- = \_ : /

1. Choose **Create backup**\.

You have now created your file system backup\. You can find a table of all your backups in the Amazon FSx console by choosing **Backups** in the right side navigation\. You can search for the name you gave your backup, and the table filters to only show matching results\.

When you create a user\-initiated backup as this procedure described, it has the type `USER_INITIATED`, and it has the `CREATING` status until it is fully available\.

## Restoring Backups<a name="restoring-backups"></a>

You can use an available backup to create a new file system, effectively restoring a point\-in\-time snapshot of another file system\. You can restore a backup using the console, AWS CLI, or one of the AWS SDKs\. The following procedure guides you through how to restore a backup from the console to create a new file system\. 

**To restore a file system from a backup**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the console dashboard, choose **Backups** from the right side navigation\.

1. Choose the backup that you want to restore from the **Backups** table, and then choose **Restore backup**\. 

   Doing so opens the file system creation wizard\. This wizard is identical to the standard file system creation wizard, except the **Storage capacity** is already set and can't be changed\. However, you can change the throughput capacity, associated VPC, and other settings\.

1. Complete the wizard as you do when you create a new file system\.

1. Choose **Review and create**\.

1. Review the settings you chose for your Amazon FSx file system, and then choose **Create file system**\.

You have restored from a backup, and a new file system is now being created\. When its status changes to `AVAILABLE`, you can use the file system as normal\.

## Deleting Backups<a name="delete-backups"></a>

Deleting a backup is a permanent, unrecoverable action\. Any data in a deleted backup is also deleted\. Do not delete a backup unless you're sure you won't need that backup again in the future\.

**To delete a backup**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the console dashboard, choose **Backups** from the right side navigation\.

1. Choose the backup that you want to delete from the **Backups** table, and then choose **Delete backup**\.

1. In the **Delete backups** dialog box that opens, confirm that the ID of the backup identifies the backup that you want to delete\.

1. Confirm that the check box is checked for the backup that you want to delete\.

1. Choose **Delete backups**\.

Your backup and all included data is now permanently and unrecoverably deleted\.