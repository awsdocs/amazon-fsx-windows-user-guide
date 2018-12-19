# File\- and Folder\-Level Access Control with Windows ACLs and Active Directory<a name="limit-access-file-folder"></a>

Amazon FSx for Windows File Server supports identity\-based authentication over the Server Message Block \(SMB\) protocol through Microsoft Active Directory\. Active Directory is the Microsoft directory service to store information about objects on the network and make this information easy for administrators and users to find and use\. These objects typically include shared resources such as file servers, and the network user and computer accounts\.

Your domain\-joined compute instances can access Amazon FSx file shares using Active Directory credentials\. You use standard Windows access control lists \(ACLs\) for fine\-grained file\- and folder\-level access control\. Amazon FSx file systems automatically verify the credentials of users accessing file system data to enforce these Windows ACLs\. 

When creating your Amazon FSx for Windows File Server file system, you join it to an AWS Directory Service for Microsoft Active Directory \(AD\)\. If you want to use a self\-managed AD, for example an on\-premises directory, with Amazon FSx, establish a one\-way forest trust relationship between that self\-managed AD and an AWS Microsoft AD that you create\.

**Note**  
After it's been created, the joined Active Directory for a file system can't be changed\. However, you can create a new file system from a backup and change the Active Directory integration configuration for that file system, along with other settings like the maintenance window\. For more information, see [Walkthrough 2: Create a File System from a Backup](walkthrough02-create-from-backup.md)\. 

Every Amazon FSx file system comes with a default Windows file share called **share**\. The Windows ACLs for this shared folder are configured to allow read/write access to domain users and allow full control to the **AWS Delegated FSx Administrators** group in your Active Directory\. To change the ACLs, you can map the share as a user that is a member of the **AWS Delegated FSx Administrators** group\.


|  | 
| --- |
|  Amazon FSx requires that the **SYSTEM** user have **Full control** NTFS ACL permissions on all folders within your file system\. Do not change the NTFS ACL permissions for this user on your folders\. Doing so can make your file share inaccessible\.  | 

## Related Links<a name="ad-related-topics"></a>
+ [What Is AWS Directory Service?](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html) in the AWS Directory Service Administration Guide\.
+ [Create Your AWS Managed Microsoft AD directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_create_directory.html) in the *AWS Directory Service Administration Guide*\.
+ [When to Create a Trust Relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html) in the *AWS Directory Service Administration Guide*\.
+ [Walkthrough 1: Prerequisites for Getting Started](walkthrough01-prereqs.md)\.