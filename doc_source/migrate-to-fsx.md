# Migrating Existing Files to Amazon FSx<a name="migrate-to-fsx"></a>

Built on Microsoft Windows Server, Amazon FSx for Windows File Server enables you to migrate your existing file datasets fully into your Amazon FSx file systems\. You can migrate not just the data for each file, but also all the relevant file metadata including attributes, time stamps, access control lists \(ACLs\), owner information, and auditing information\. With this total migration support, Amazon FSx enables moving your Windows\-based workloads and applications relying on these file datasets to the AWS Cloud\.

Use the following topic as a guide through the process for copying existing file data, while preserving all file metadata, from your on\-premises data centers or from your self\-managed file servers on Amazon EC2\.

## Prerequisites<a name="fsx-migrate-prereqs"></a>

Before you begin, make sure that you do the following:
+ Deploy an AWS Directory Service for Microsoft Active Directory \(AWS Managed Microsoft AD\) directory in your Amazon VPC\.
+ Establish a one\-way forest\-level trust relationship between the AWS Managed Microsoft AD directory and your on\-premises Microsoft AD directory, where the AWS Managed Microsoft AD directory forest trusts your on\-premises Microsoft AD forest\.
+ Create an Amazon FSx file system in the same Amazon VPC, joined to your AWS Managed Microsoft AD directory\.
+ Note the location \(for example, `\\Source\Share`\) of the file share \(either on\-premises or in AWS\) that contains the existing files you want to transfer over to Amazon FSx\.
+ Note the location \(for example, `\\Target\Share`\) of the file share on your Amazon FSx file system to which you want to transfer over your existing files\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/fsx-migrate-existing.png)

## How to Migrate Existing Files to Amazon FSx<a name="fsx-migrate-procedure"></a>

You can migrate existing files to Amazon FSx by using the following procedure\.

**To migrate existing files to Amazon FSx**

1. Launch a Windows Server 2016 Amazon EC2 instance in the same Amazon VPC as that of your Amazon FSx file system\.

1. Log on to the Amazon EC2 instance as a member of the **AWS Delegated FSx Administrators** group\. This is to ensure that you’re able to transfer sensitive information like owner information and auditing information that need administrative privileges\.

1. Open the **Command Prompt** and Map the source file share on your existing file server \(on\-premises or in AWS\) to a drive letter \(for example, Y:\) as follows\. You will have to provide credentials for a user in your on\-premises AD that has Read and Execute permissions on the source shared folder\.

   ```
   C:\>net use Y: \\fileserver1.mydata.com\localdata
   Enter the user name for ‘fileserver1.mydata.com’: mydata.com\Administrator
   Enter the password for ‘fileserver1.mydata.com’: _
   
   Drive Y: is now connected to \\fileserver1.mydata.com\localdata.
   
   The command completed successfully.
   ```

1. Map the target file share on your Amazon FSx file system to a different drive letter \(for example, *Z*:\) on your Amazon EC2 instance as follows:

   ```
   C:\>net use Z: \\fs-0123456789abcdef0.my-aws-ad.com\share
   Drive Z: is now connected to \\fs-0123456789abcdef0.my-aws-ad.com\share.
   
   The command completed successfully.
   ```

1. From the **Command Prompt** or **Windows PowerShell** as Administrator \(using the **Run as Administrator** option from the context menu\), execute the following RoboCopy command to copy the files from the source share to the target share\. 

   The `ROBOCOPY` command is a flexible file transfer utility with multiple options to control the data transfer process\. As a result of this `ROBOCOPY` command execution, all the files and directories from the source share are copied to the Amazon FSx target share\. The copy preserves file and folder NTFS ACLs, attributes, time stamps, owner information, and auditing information\.

   ```
   robocopy Y:\ Z:\ /copy:DATSOU /secfix /e /z /MT:8
   ```

   The example command preceding uses the following elements and options:
   + Y – Refers to the source share located in the on\-premises Active Directory forest mydata\.com\.
   + Z – Refers to the target share \\\\fs\-0c322f485eb48aec1\\share on Amazon FSx\.
   + /copy – Specifies the following file properties to be copied: 
     + D – data
     + A – attributes
     + T – time stamps
     + S – NTFS ACLs
     + O – owner information
     + U – auditing information\.
   + /secfix – Fixes file security on all files, even skipped ones\.
   + /e – Copies subdirectories, including empty ones\.
   + /z – Copies files in restartable mode\.
   + /MT:8 – Specifies how many threads to use for performing multithreaded copies\.