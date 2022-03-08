# Switching storage type to HDD while restoring a backup fails<a name="create-fs-from-backup-fails"></a>

Creating a file system from a backup fails with the following error message:

`Switching storage type to HDD while creating a file system from backup backup_id is not supported because a storage scaling activity was still under way on the source file system to increase storage capacity from less than 2000 GiB when the backup backup_id was taken, and the minimum storage capacity for HDD storage is 2000 GiB.`

This issue occurs when restoring a backup and you have changed the storage type from SSD to HDD\. The restore from backup fails because the backup that you are restoring was taken while a storage capacity increase was still in progress on the original file system\. The file system's SSD storage capacity before the increase request was less than 2000 GiB, which is the minimum storage capacity required to create an HDD file system\. 

Use the following procedure to resolve this issue\.

1. Wait for the storage capacity increase request to complete and the file system has at least 2000 GiB of SSD storage capacity\. For more information, see [Monitoring storage capacity increases](managing-storage-capacity.md#monitoring-storage-capacity-increase)\.

1. Take a user\-initiated backup of the file system\. For more information, see [Working with user\-initiated backups](using-backups.md#user-initiated-backups)\.

1. Restore the user\-initiated backup to a new file system using HDD storage\. For more information, see [Restoring backups](using-backups.md#restoring-backups)\.