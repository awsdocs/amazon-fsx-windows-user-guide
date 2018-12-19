# Amazon FSx Maintenance Windows<a name="maintenance-windows"></a>

Amazon FSx for Windows File Server performs routine software patching for the Microsoft Windows Server software it manages\. The maintenance window is your opportunity to control what day and time of the week this software patching occurs\.

Patching occurs infrequently, typically once every several weeks\. Patching should require only a fraction of your 30\-minute maintenance window\. During these few minutes of time, your file system is temporarily unavailable\. 

You choose the maintenance window during file system creation\. If you have no time preference, then a 30\-minute default window is assigned\.

**Note**  
To ensure data integrity during maintenance activity, Amazon FSx for Windows File Server completes any pending write operations to the underlying storage volumes hosting your file system before maintenance begins\.

## Changing the Maintenance Window of an Existing File System<a name="change-maintenance-windows"></a>

You can use the AWS CLI or one of the AWS SDKs \(but not the console\) to change the maintenance window for your file systems with the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) operation\. For more information, see [Walkthrough 3: Update an Existing File System](walkthrough03-update-file-system.md)\.