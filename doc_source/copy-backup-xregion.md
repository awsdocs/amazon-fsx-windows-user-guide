# Walkthrough 7: Copying a backup to another AWS Region<a name="copy-backup-xregion"></a>

With Amazon FSx, you can copy an existing backup within the same AWS account to another AWS Region \(a cross\-Region backup copy\) or to the same AWS Region \(an in\-Region backup copy\)\.

The following procedure guides you through the process of creating a copy of a backup within the same AWS account\. Before you can create this backup copy, you must have an existing backup\. For more information, see [Working with backups](using-backups.md)\.

**To copy an existing backup within the same AWS account \(cross\-Region or in\-Region\)**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the navigation pane, choose **Backups**\.

1. In the **Backups** table, choose the backup that you want to copy\.

1. Choose **Copy backup**\. Doing so opens the **Copy backup** wizard\.

1. In the **Destination Region** list, choose a destination AWS Region to copy the backup to\. The destination can be in another AWS Region or within the same AWS Region\.

1. \(Optional\) Select **Copy Tags** to copy tags from the source backup to the destination backup\. If you select **Copy Tags** and also add tags at step 8, all the tags are merged\.

1. For **Encryption**, choose the AWS KMS encryption key to encrypt the copied backup\.

1. For **Tags \- optional**, enter a key and value to add tags for your copied backup\. If you add tags here and also selected **Copy Tags** at step 6, all the tags are merged\.

1. Choose **Copy backup**\.

You've now successfully copied a backup within the same AWS account to another AWS Region or within the same AWS Region\.