# Managing DNS aliases<a name="managing-dns-aliases"></a>

Amazon FSx for Windows File Server provides a default Domain Name System \(DNS\) name for every file system that you can use to access the data on your file system\. You can also access your file systems using a DNS alias of your choosing\. With DNS aliases, you can continue using existing DNS names to access data stored on Amazon FSx when migrating file system storage from on\-premises to Amazon FSx, without needing to update any tools or applications\. For more information, see [Migrating existing file storage to Amazon FSx](migrate-to-fsx.md)\. 

**Note**  
Support for DNS aliases is available on Amazon FSx for Windows File Server file systems created after 12:00 pm ET on November 9, 2020\. To use DNS aliases on a file system created before 12:00 pm ET on November 9, 2020, do the following:  
Take a backup of the existing file system\. For more information, see [Working with user\-initiated backups](using-backups.md#user-initiated-backups)\.
Restore the backup to a new file system\. For more information, see [Restoring Backups](using-backups.md#restoring-backups)\.
Once the new file system is available, you will be able to use DNS aliases to access it, using the information provided in this section\.

You can associate DNS aliases with existing Amazon FSx for Windows File Server file systems, when you create new file systems, and when you create a new file system from a backup\. You can associate up to 50 DNS aliases with a file system at any one time\. 

In addition to associating DNS aliases with your file system, for clients to connect to the file system using the DNS aliases, you also must do the following:
+ Configure service principal names \(SPNs\) for Kerberos authentication and encryption\.
+ Configure a DNS CNAME record for the DNS alias that resolves to the default DNS name for your Amazon FSx file system\.

For more information, see [Walkthrough 5: Using DNS aliases to access your file system](walkthrough05-file-system-custom-CNAME.md)\.

A DNS alias name must meet the following requirements:
+ Must be formatted as a fully qualified domain name \(FQDN\)\.
+ Can contain alphanumeric characters and hyphens \(‐\)\.
+ Cannot start or end with a hyphen\.
+ Can start with a numeric\.

For DNS alias names, Amazon FSx stores alphabetic characters as lowercase letters \(a\-z\), regardless of how you specify them: as uppercase letters, lowercase letters, or the corresponding letters in escape codes\. 

If you try to associate an alias that is already associated with the file system, it has no effect\. If you try to disassociate an alias from a file system that is not associated with the file system, Amazon FSx responds with a bad request error\.

**Note**  
When Amazon FSx adds or removes aliases on a file system, connected clients are temporarily disconnected and will automatically reconnect to the file system\. Any files that were open by clients mapping a non\-Continuously\-Available \(non\-CA\) share at the time of disconnection must be reopened by the client\.

**Topics**
+ [Using DNS aliases with Kerberos authentication](#aliases-and-kerberos)
+ [Viewing DNS aliases associated with file systems and backups](#view-aliases)
+ [DNS alias status](#alias-status)
+ [Associating DNS aliases when creating a new file system](#add-alias-new-filesystem)
+ [Managing DNS aliases on existing file systems](#manage-aliases-existing-fs)

## Using DNS aliases with Kerberos authentication<a name="aliases-and-kerberos"></a>

We recommend that you use Kerberos\-based authentication and encryption in transit with Amazon FSx\. Kerberos provides the most secure authentication for clients that access your file system\. To enable Kerberos authentication for clients that access your Amazon FSx file system using a DNS alias, you must configure service principal names \(SPNs\) that correspond to the DNS alias on your Amazon FSx file system’s Active Directory computer object\.

If you have SPNs conﬁgured for the DNS alias that you've assigned to another ﬁle system on a computer object in your Active Directory, you must ﬁrst remove those SPNs before adding SPNs to your ﬁle system’s computer object\. For more information, see [Walkthrough 5: Using DNS aliases to access your file system](walkthrough05-file-system-custom-CNAME.md)\. 

## Viewing DNS aliases associated with file systems and backups<a name="view-aliases"></a>

You can see the DNS aliases that are currently associated with file systems and backups using the Amazon FSx console, the AWS CLI, and the Amazon FSx API and SDKs\.

**To view DNS aliases associated with file systems:**
+ Using the console — Choose a file system to view the **File systems** detail page\. Choose the **Network & security** tab to view the **DNS aliases**\.
+ Using the CLI or API — Use the `describe-file-system-aliases` CLI command or the [DescribeFileSystemAliases](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystemAliases.html) API operation\.

**To view DNS aliases associated with backups:**
+ Using the console — In the navigation pane, choose **Backups**, and then choose the backup that you want to view\. In the **Summary** pane, view the **DNS aliases** field\.
+ Using the CLI or API — Use the `describe-backups` CLI command or the [DescribeBackups](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeBackups.html) API operation\.

## DNS alias status<a name="alias-status"></a>

DNS aliases can have one of the following values:
+ Available – The DNS alias is associated with an Amazon FSx file system\.
+ Creating – Amazon FSx is creating the DNS alias and associating it with the file system\.
+ Deleting – Amazon FSx is disassociating the DNS alias from the file system and deleting it\.
+ Failed to create – Amazon FSx was unable to associate the DNS alias with the file system\.
+ Failed to delete – Amazon FSx was unable to disassociate the DNS alias from the file system\.

## Associating DNS aliases when creating a new file system<a name="add-alias-new-filesystem"></a>

You can associate DNS aliases when creating a new file system from scratch, or when creating a file system from a backup\.

### To associate DNS aliases when creating a new Amazon FSx file system \(console\)<a name="w157aac27b9c33b5b1"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Follow the procedure for creating a new file system described in [Step 1: Create Your File System](getting-started-step1.md) in the Getting Started section\. 

1. In the **Access \- optional** section of the **Create file system** wizard, enter the DNS aliases that you want to associate with your file system\.  
![\[\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/FSxW-create-fs-Access-aliases.png)

1. When the file system is **Available**, you can access it using the DNS alias by configuring service principal names \(SPNs\) and updating or creating a DNS CNAME record for the alias\. For more information, see [Walkthrough 5: Using DNS aliases to access your file system](walkthrough05-file-system-custom-CNAME.md)\.

### To associate DNS aliases when creating a new Amazon FSx file system \(CLI\)<a name="w157aac27b9c33b5b3"></a>

1. When creating a new file system, use the [Alias](https://docs.aws.amazon.com/fsx/latest/APIReference/API_Alias.html) property with the [CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html) API operation to associate DNS aliases with the new file system\.

   ```
   aws fsx create-file-system \
     --file-system-type WINDOWS \
     --storage-capacity 2000 \
     --storage-type SSD \
     --subnet-ids subnet-123456 \
     --windows-configuration Aliases=[financials.corp.example.com,accts-rcv.corp.example.com]
   ```

1. When the file system is **Available**, you can access it using the DNS alias by configuring service principal names \(SPNs\) and updating or creating a DNS CNAME record for the alias\. For more information, see [Walkthrough 5: Using DNS aliases to access your file system](walkthrough05-file-system-custom-CNAME.md)\.

### To associate or disassociate DNS aliases when creating a new Amazon FSx file system from a backup \(CLI\)<a name="w157aac27b9c33b5b5"></a>

1. When creating a new file system from a backup of an existing file system, you can use the [Aliases](https://docs.aws.amazon.com/fsx/latest/APIReference/API_Aliases.html) property with the [CreateFileSystemFromBackup](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystemFromBackup.html) API operation as follows:
   + Any aliases associated with the backup are associated with the new file system by default\.
   + To create a file system without preserving any aliases from the backup, use the `Aliases` property with an empty set\. 

     To associate additional DNS aliases, use the `Aliases` property and include both the original aliases associated with the backup and the new aliases you want to associate\.

   The following CLI command associates two aliases with the file system Amazon FSx is creating from a backup\.

   ```
   aws fsx create-file-system-from-backup \
     --backup-id backup-0123456789abcdef0
     --storage-capacity 2000 \
     --storage-type HDD \
     --subnet-ids subnet-123456 \
     --windows-configuration Aliases=[transactions.corp.example.com,accts-rcv.corp.example.com]
   ```

1. When the file system is **Available**, you can access it using the DNS alias by configuring service principal names \(SPNs\) and updating or creating a DNS CNAME record for the alias\. For more information, see [Walkthrough 5: Using DNS aliases to access your file system](walkthrough05-file-system-custom-CNAME.md)\.

## Managing DNS aliases on existing file systems<a name="manage-aliases-existing-fs"></a>

You can add and remove aliases on existing file systems\.

### To manage DNS aliases on an existing file system \(console\)<a name="w157aac27b9c35b5b1"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems**, and choose the Windows file system that you want to manage DNS aliases for\.

1. On the **Network & security** tab, choose **Manage** for **DNS aliases** to display the **Manage DNS aliases** dialog box\.  
![\[FSx console Manage DNS aliases window, use this window for associating and disassociating DNS aliases with an Amazon FSx for Windows File Server file system.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/FSxW-manage-aliases.png)
   + To associate DNS aliases – In the **Associate new aliases** box, enter the DNS aliases that you want to associate\. Choose **Associate**\. 
   + To disassociate DNS aliases – In the **Current aliases** list, choose the aliases to disassociate from\. Choose **Disassociate**\.

   You can monitor the status of the aliases you have managed in the **Current aliases** list\. Refresh the list to update the status\. It takes up to 2\.5 minutes for an alias to be associated or disassociated with a file system\.

1. When the alias is **Available**, you can access your file system using the DNS alias by configuring service principal names \(SPNs\) and updating or creating a DNS CNAME record for the alias\. For more information, see [Walkthrough 5: Using DNS aliases to access your file system](walkthrough05-file-system-custom-CNAME.md)\.

### To associate DNS aliases with an existing file system \(CLI\)<a name="w157aac27b9c35b5b3"></a>

1. Use the `associate-file-system-aliases` CLI command or the [AssociateFileSystemAliases](https://docs.aws.amazon.com/fsx/latest/APIReference/API_AssociateFileSystemAliases.html) API operation to associate DNS aliases with an existing file system\. 

   The following CLI request associates two aliases with the specified file system\.

   ```
   aws fsx associate-file-system-aliases \
     --file-system-id fs-0123456789abcdef0 \
     --aliases financials.corp.example.com transfers.corp.example.com
   ```

   The response shows the status of the aliases that Amazon FSx is associating with the file system\.

   ```
   {
       "Aliases": [
           {
               "Name": "financials.corp.example.com",
               "Lifecycle": CREATING
           },
           {
               "Name": "transfers.corp.example.com",
               "Lifecycle": CREATING
           }
       ]
   }
   ```

1. Use the `describe-file-system-aliases` CLI command \([DescribeFileSystemAliases](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystemAliases.html) is the equivalent API operation\) to monitor the status of the aliases that you are associating\.

1. When the `Lifecycle` has a value of AVAILABLE \(a process that takes up to 2\.5 minutes\), you can access your file system using the DNS alias by configuring service principal names \(SPNs\) and updating or creating a DNS CNAME record for the alias\. For more information, see [Walkthrough 5: Using DNS aliases to access your file system](walkthrough05-file-system-custom-CNAME.md)\.

### To disassociate DNS aliases from a file system \(CLI\)<a name="w157aac27b9c35b5b5"></a>
+ Use the `disassociate-file-system-aliases` CLI command or the [DisassociateFileSystemAliases](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DisassociateFileSystemAliases.html) API operation to disassociate DNS aliases from an existing file system\. 

  The following command disassociates one alias from a file system\.

  ```
  aws fsx disassociate-file-system-aliases \
    --file-system-id fs-0123456789abcdef0 \
    --aliases financials.corp.example.com
  ```

  The response shows the status of the aliases that Amazon FSx is disassociating from the file system\.

  ```
  {
      "Aliases": [
          {
              "Name": "financials.corp.example.com",
              "Lifecycle": DELETING
          }
      ]
  }
  ```

  Use the `describe-file-system-aliases` CLI command \([DescribeFileSystemAliases](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystemAliases.html) is the equivalent API operation\) to monitor the status of the aliases\. It takes up to 2\.5 minutes for the alias to be deleted\.