# Working with backups<a name="using-backups"></a>

With Amazon FSx, backups are file\-system\-consistent, highly durable, and incremental\. To ensure file system consistency, Amazon FSx uses the Volume Shadow Copy Service \(VSS\) in Microsoft Windows\. To ensure high durability, Amazon FSx stores backups in Amazon Simple Storage Service \(Amazon S3\)\. 

Amazon FSx backups are incremental, whether they are generated using the automatic daily backup or the user\-initiated backup feature\. This means that only the data on the file system that has changed after your most recent backup is saved\. This minimizes the time required to create the backup and saves on storage costs by not duplicating data\. \(Note that if the data on your file system changes frequently, your total backup usage can be greater than the used storage capacity of your file system\.\) When you delete a backup, only the data unique to that backup is removed\.

Each FSx for Windows File Server backup contains all of the information that is needed to create a new file system from the backup, effectively restoring a point\-in\-time snapshot of the file system\.

Creating regular backups for your file system is a best practice that complements the replication that Amazon FSx for Windows File Server performs for your file system\. Amazon FSx backups help support your backup retention and compliance needs\. Working with Amazon FSx backups is easy, whether it's creating backups, copying a backup, restoring a file system from a backup, or deleting a backup\.

**Topics**
+ [Working with automatic daily backups](#automatic-backups)
+ [Working with user\-initiated backups](#user-initiated-backups)
+ [Using AWS Backup with Amazon FSx](#aws-backup-and-fsx)
+ [Copying backups](#copy-backups)
+ [Restoring backups](#restoring-backups)
+ [Deleting backups](#delete-backups)

## Working with automatic daily backups<a name="automatic-backups"></a>

By default, Amazon FSx takes an automatic daily backup of your file system\. These automatic daily backups occur during the daily backup window that was established when you created the file system\. At some point during the daily backup window, storage I/O might be suspended briefly while the backup process initializes \(typically for less than a few seconds\)\. When you choose your daily backup window, we recommend that you choose a convenient time of the day\. This time ideally is outside of the normal operating hours for the applications that use the file system\.

Automatic daily backups are kept for a certain period of time, known as a retention period\. The default retention period for automatic daily backups is 7 days\. You can set the retention period to be between 0–90 days\. Setting the retention period to 0 \(zero\) days turns off automatic daily backups\. Automatic daily backups are deleted when the file system is deleted\.

**Note**  
Setting the retention period to 0 days means that your file system is never automatically backed up\. We highly recommend that you use automatic daily backups for file systems that have any level of critical functionality associated with them\.

You can use the AWS CLI or one of the AWS SDKs to change the backup window and backup retention period for your file systems\. Use the [https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) API operation or the [https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html) CLI command\. For more information, see [Walkthrough 3: Update an existing file system](walkthrough03-update-file-system.md)\.

## Working with user\-initiated backups<a name="user-initiated-backups"></a>

With Amazon FSx, you can manually take backups of your file systems at any time\. You can do so using the Amazon FSx console, API, or the AWS Command Line Interface \(AWS CLI\)\. Your user\-initiated backups of Amazon FSx file systems never expire, and they are available for as long as you want to keep them\. User\-initiated backups are retained even after you delete the file system that was backed up\. You can delete user\-initiated backups only by using the Amazon FSx console, API, or CLI\. They are never automatically deleted by Amazon FSx\. For more information, see [Deleting backups](#delete-backups)\.

If a backup is initiated while the file system is being modified \(such as during an update to throughput capacity, or during file system maintenance\), the backup request is queued and will resume when the activity is complete\.

### Creating user\-initiated backups<a name="creating-backups"></a>

The following procedure guides you through how to create a user\-initiated backup in the Amazon FSx console for an existing file system\.

**To create a user\-initiated file system backup**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the console dashboard, choose the name of the file system that you want to back up\.

1. From **Actions**, choose **Create backup**\.

1. In the **Create backup** dialog box that opens, provide a name for your backup\. Backup names can be a maximum of 256 Unicode characters, including letters, white space, numbers, and the special characters \. \+ \- = \_ : /

1. Choose **Create backup**\.

You have now created your file system backup\. You can find a table of all your backups in the Amazon FSx console by choosing **Backups** in the right side navigation\. You can search for the name you gave your backup, and the table filters to only show matching results\.

When you create a user\-initiated backup as this procedure described, it has the type `USER_INITIATED`, and it has the `CREATING` status until it is fully available\.

## Using AWS Backup with Amazon FSx<a name="aws-backup-and-fsx"></a>

AWS Backup is a simple and cost\-effective way to protect your data by backing up your Amazon FSx file systems\. AWS Backup is a unified backup service designed to simplify the creation, copying, restoration, and deletion of backups, while providing improved reporting and auditing\. AWS Backup makes it easier to develop a centralized backup strategy for legal, regulatory, and professional compliance\. AWS Backup also makes protecting your AWS storage volumes, databases, and file systems simpler by providing a central place where you can do the following:
+ Configure and audit the AWS resources that you want to back up\.
+ Automate backup scheduling\.
+ Set retention policies\.
+ Copy backups across AWS Regions and across AWS accounts\.
+ Monitor all recent backup, copy, and restore activity\.

AWS Backup uses the built\-in backup functionality of Amazon FSx\. Backups taken from the AWS Backup console have the same level of file system consistency and performance, and the same restore options as backups taken through the Amazon FSx console\. If you use AWS Backup to manage these backups, you gain additional functionality, such as unlimited retention options and the ability to create scheduled backups as frequently as every hour\. In addition, AWS Backup retains your immutable backups even after the source file system is deleted\. This protects against accidental or malicious deletion\.

Backups taken by AWS Backup are considered user\-initiated backups, and they count toward the user\-initiated backup quota for Amazon FSx\. You can see and restore backups taken by AWS Backup in the Amazon FSx console, CLI, and API\. However, you can't delete backups taken by AWS Backup in the Amazon FSx console, CLI, or API\. For more information about how to use AWS Backup to back up your Amazon FSx file systems, see [Working with Amazon FSx File Systems](https://docs.aws.amazon.com/aws-backup/latest/devguide/working-with-other-services.html#working-with-fsx) in the *AWS Backup Developer Guide*\.

## Copying backups<a name="copy-backups"></a>

You can use Amazon FSx to manually copy backups within the same AWS account to another AWS Region \(cross\-Region copies\) or within the same AWS Region \(in\-Region copies\)\. You can make cross\-Region copies only within the same AWS partition\. You can create user\-initiated backup copies using the Amazon FSx console, AWS CLI, or API\. When you create a user\-initiated backup copy, it has the type `USER_INITIATED`\.

You can also use AWS Backup to copy backups across AWS Regions and across AWS accounts\. AWS Backup is a fully managed backup management service that provides a central interface for policy\-based backup plans\. With its cross\-account management, you can automatically use backup policies to apply backup plans across the accounts within your organization\.

*Cross\-Region backup copies* are particularly valuable for cross\-Region disaster recovery\. You take backups and copy them to another AWS Region so that in the event of a disaster in the primary AWS Region, you can restore from backup and recover availability quickly in the other AWS Region\. You can also use backup copies to clone your file dataset to another AWS Region or within the same AWS Region\. You make backup copies within the same AWS account \(cross\-Region or in\-Region\) by using the Amazon FSx console, AWS CLI, or Amazon FSx API\. You can also use [AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/cross-region-backup.html) to perform backup copies, either on\-demand or policy\-based\.

*Cross\-account backup copies* are valuable for meeting your regulatory compliance requirements to copy backups to an isolated account\. They also provide an additional layer of data protection to help prevent accidental or malicious deletion of backups, loss of credentials, or compromise of AWS KMS keys\. Cross\-account backups support *fan\-in* \(copy backups from multiple primary accounts to one isolated backup copy account\) and *fan\-out* \(copy backups from one primary account to multiple isolated backup copy accounts\)\. 

You can make cross\-account backup copies by using AWS Backup with AWS Organizations support\. Account boundaries for cross\-account copies are defined by AWS Organizations policies\. For more information about using AWS Backup to make cross\-account backup copies, see [Creating backup copies across AWS accounts](https://docs.aws.amazon.com/aws-backup/latest/devguide/create-cross-account-backup.html) in the *AWS Backup Developer Guide*\.

### Backup copy limitations<a name="copy-limitations"></a>

The following are some limitations when you copy backups:
+ Cross\-Region backup copies are supported only between any two commercial AWS Regions, between the China \(Beijing\) and China \(Ningxia\) Regions, and between the AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\) Regions, but not across those sets of Regions\.
+ Cross\-Region backup copies are not supported in opt\-in Regions\.
+ You can make in\-Region backup copies within any AWS Region\.
+ The source backup must have a status of `AVAILABLE` before you can copy it\.
+ You cannot delete a source backup if it is being copied\. There might be a short delay between when the destination backup becomes available and when you are allowed to delete the source backup\. You should keep this delay in mind if you retry deleting a source backup\.
+ You can have up to five backup copy requests in progress to a single destination AWS Region per account\.

### Permissions for cross\-Region backup copies<a name="copy-permissions"></a>

You use an IAM policy statement to grant permissions to perform a backup copy operation\. To communicate with the source AWS Region to request a cross\-Region backup copy, the requester \(IAM role or IAM user\) must have access to the source backup and the source AWS Region\.

You use the policy to grant permissions to the `CopyBackup` action for the backup copy operation\. You specify the action in the policy's `Action` field, and you specify the resource value in the policy's `Resource` field, as in the following example\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "fsx:CopyBackup",
            "Resource": "arn:aws:fsx:*:111111111111:backup/*"
        }
    ]
}
```

For more information on IAM policies, see [Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) in the *IAM User Guide*\.

### Full and incremental copies<a name="copy-incrementals"></a>

When you copy a backup to a different AWS Region from the source backup, the first copy is a full backup copy\. After the first backup copy, all subsequent backup copies to the same destination Region within the same AWS account are incremental, provided that you haven't deleted all previously\-copied backups in that Region and have been using the same AWS KMS key\. If both conditions aren't met, the copy operation results in a full \(not incremental\) backup copy\.

### To copy a backup within the same account \(cross\-Region or in\-Region\) using the console<a name="collapsible-section-1"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the navigation pane, choose **Backups**\.

1. In the **Backups** table, choose the backup that you want to copy, and then choose **Copy backup**\.

1. In the **Settings** section, do the following:
   + In the **Destination Region** list, choose a destination AWS Region to copy the backup to\. The destination can be in another AWS Region \(cross\-Region copy\) or within the same AWS Region \(in\-Region copy\)\.
   + \(Optional\) Select **Copy Tags** to copy tags from the source backup to the destination backup\. If you select **Copy Tags** and also add tags at step 6, all the tags are merged\.

1. For **Encryption**, choose the AWS KMS encryption key to encrypt the copied backup\.

1. For **Tags \- optional**, enter a key and value to add tags for your copied backup\. If you add tags here and also selected **Copy Tags** at step 4, all the tags are merged\.

1. Choose **Copy backup**\.

Your backup is copied within the same AWS account to the selected AWS Region\.

### To copy a backup within the same account \(cross\-Region or in\-Region\) using the CLI<a name="collapsible-section-2"></a>
+ Use the `copy-backup` CLI command or the [CopyBackup](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CopyBackup.html) API operation to copy a backup within the same AWS account, either across an AWS Region or within an AWS Region\.

  The following command copies a backup with an ID of `backup-0abc123456789cba7` from the `us-east-1` Region\.

  ```
  aws fsx copy-backup \
    --source-backup-id backup-0abc123456789cba7 \
    --source-region us-east-1
  ```

  The response shows the description of the copied backup\.

   You can view your backups on the Amazon FSx console or programmatically using the `describe-backups` CLI command or the [DescribeBackups](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeBackups.html) API operation\.

## Restoring backups<a name="restoring-backups"></a>

You can use an available backup to create a new file system, effectively restoring a point\-in\-time snapshot of another file system\. You can restore a backup using the console, AWS CLI, or one of the AWS SDKs\. Restoring a backup to a new file system takes the same amount of time as creating a new file system\. The data restored from the backup is lazy\-loaded onto the file system, during which time you will experience slightly higher latency\.

To ensure that users can continue to access the restored file system, make sure that the Active Directory domain associated with the restored file system is the same as that of the original file system, or is trusted by the AD domain of the original file system\. For more information about Active Directory, see [Working with Microsoft Active Directory in FSx for Windows File Server](aws-ad-integration-fsxW.md)\.

The following procedure guides you through how to restore a backup using the console to create a new file system\.

**Note**  
You can only restore your backup to a file system of the same deployment type and storage capacity as the original\. You can increase your restored file system's storage capacity after it becomes available\. For more information, see [Managing storage capacity](managing-storage-capacity.md)\.

**To restore a file system from a backup**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the console dashboard, choose **Backups** from the left side navigation\.

1. Choose the backup that you want to restore from the **Backups** table, and then choose **Restore backup**\. 

   Doing so opens the file system creation wizard\. This wizard is identical to the standard file system creation wizard, except the **Deployment type** and **Storage capacity** are already set and can't be changed\. However, you can change the throughput capacity, associated VPC, and other settings, and storage type\. The storage type is set to **SSD** by default, but you can change it to **HDD** under the following conditions:
   + The file system deployment type is **Multi\-AZ** or **Single\-AZ 2**\.
   + The storage capacity is at least 2,000 GiB\.

1. Complete the wizard as you do when you create a new file system\.

1. Choose **Review and create**\.

1. Review the settings you chose for your Amazon FSx file system, and then choose **Create file system**\.

You have restored from a backup, and a new file system is now being created\. When its status changes to `AVAILABLE`, you can use the file system as normal\.

## Deleting backups<a name="delete-backups"></a>

Deleting a backup is a permanent, unrecoverable action\. Any data in a deleted backup is also deleted\. Do not delete a backup unless you're sure you won't need that backup again in the future\. You can't delete backups taken by AWS Backup, which have type **AWS Backup**, in the Amazon FSx console, CLI, or API\.

**To delete a backup**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the console dashboard, choose **Backups** from the right side navigation\.

1. Choose the backup that you want to delete from the **Backups** table, and then choose **Delete backup**\.

1. In the **Delete backups** dialog box that opens, confirm that the ID of the backup identifies the backup that you want to delete\.

1. Confirm that the check box is checked for the backup that you want to delete\.

1. Choose **Delete backups**\.

Your backup and all included data are now permanently and unrecoverably deleted\.