# Working with Amazon FSx maintenance windows<a name="maintenance-windows"></a>

Amazon FSx for Windows File Server performs routine software patching for the Microsoft Windows Server software it manages\. The maintenance window is your opportunity to control what day and time of the week this software patching occurs\.

Patching occurs infrequently, typically once every several weeks\. Patching should require only a fraction of your 30\-minute maintenance window\. During these few minutes of time, you should expect that your Single\-AZ file system will be unavailable, and your Multi\-AZ file systems will automatically fail over and fail back\.

You choose the maintenance window during file system creation\. If you have no time preference, then a 30\-minute default window is assigned\.

**Note**  
To ensure data integrity during maintenance activity, Amazon FSx for Windows File Server completes any pending write operations to the underlying storage volumes hosting your file system before maintenance begins\.

You can use the Amazon FSx Management Console, AWS CLI, AWS API, or one of the AWS SDKs to change the maintenance window for your file systems\. 

**To change the weekly maintenance window \(console\)**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Choose **File systems** in the left hand navigation column\.

1. Choose the file system that you want to change the weekly maintenance window for\. The file system details page displays\.

1. Choose **Administration** to display the file system administration **Settings** panel\.

1. Choose **Update** to display the **Change maintenance window** window\.

1.  Enter the new day and time that you want the weekly maintenance window to start\.

1. Choose **Save** to save your changes\. The new maintenance start time is displayed in the **Administration Settings** panel\.

To change the weekly maintenance window using the CLI or API using the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) operation, see [Walkthrough 3: Update an existing file system](walkthrough03-update-file-system.md)\.