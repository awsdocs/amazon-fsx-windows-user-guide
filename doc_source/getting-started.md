# Getting Started with Amazon FSx<a name="getting-started"></a>

Following, you can learn how to get started using Amazon FSx\. This getting started exercise includes the following steps\.

**Topics**
+ [Step 1: Create Your File System](#getting-started-step1)
+ [Step 2: Map Your File Share to an EC2 Instance Running Windows Server](#getting-started-step2)
+ [Step 3: Write Data to Your File Share](#getting-started-step3)
+ [Step 4: Back Up Your File System](#getting-started-step4)
+ [Step 5: Clean Up Resources](#getting-started-step5)
+ [Amazon FSx File System Status](file-system-lifecycle-states.md)

## Step 1: Create Your File System<a name="getting-started-step1"></a>

To create your Amazon FSx file system, you must create your Amazon Elastic Compute Cloud \(Amazon EC2\) instance and the AWS Directory Service directory\. If you don't have that set up already, see [Walkthrough 1: Prerequisites for Getting Started](walkthrough01-prereqs.md)\. 

**To create your first file system**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the console dashboard, choose **Create file system** to start the file system creation wizard\.

1. Provide a name for your file system\. You can use a maximum of 256 Unicode letters, white space, and numbers, plus the special characters \+ \- = \. \_ : /

1. Provide the **storage capacity** for your file system, in GiB\. This value can be any whole number in the range of 300 to 65,536\.

1. Choose your throughput capacity\. The **Recommended throughput capacity** is based off of your chosen storage capacity\. You can change this level to more MB/s as needed at creation by manually choosing a throughput capacity from the box\.

1. Choose the Amazon VPC that you want to associate with your file system\. For the purposes of this getting started exercise, choose the same Amazon VPC that you chose for your AWS Directory Service directory and your Amazon EC2 instance\.

1. Choose any value for **Availability Zones** and **Subnet**\.

1. <a name="security_group_setup"></a>For **VPC security groups**, the default security group for your default Amazon VPC is already added to your file system in the console\. If you're not using the default security group, make sure that you add the following rules to the security group you're using for this getting started exercise:

   1. Inbound and outbound rules to allow the following ports:
      + TCP/UDP 445 \(SMB\)
      + TCP 135 \(RPC\)
      + TCP/UDP 1024\-65535 \(Ephemeral ports for RPC\)

      From and to IP addresses or security group IDs associated with the following source and destination resources:
      + Client compute instances from which you want to access the file system\.
      + Other file servers that you expect this file system to participate with in DFS Replication groups\.

   1. Outbound rules to allow all traffic to the security group ID associated with the AWS Managed Microsoft AD directory to which you're joining your file system\.
**Note**  
In some cases, you might have modified the rules of your AWS Managed Microsoft AD's security group from the default settings\. If so, make sure that this security group has the required inbound rules to allow traffic from your Amazon FSx file system\. To learn more about the required inbound rules, see [AWS Managed Microsoft AD Prerequisites](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_prereqs.html) in the *AWS Directory Service Administration Guide*\.

1. For **Windows authentication**, choose **AWS Managed Microsoft Active Directory** and enter the fully qualified domain name for your AWS Directory Service directory from the list\. 

1. Keep **Encryption** set to off\.

1. Choose **Next**\. 

1.  Review the file system configuration on the **Create file system** page, and choose **Create file system**\. 

1. After the file system has been created, choose the file system ID in the **File Systems** dashboard, choose **Attach**, and note the fully qualified domain name for your file system\. You need it in a later step\.

## Step 2: Map Your File Share to an EC2 Instance Running Windows Server<a name="getting-started-step2"></a>

You can now mount your Amazon FSx file system to your Microsoft Windows–based Amazon EC2 instance joined to your AWS Directory Service directory\. The name of your file share is not the same as name of your file system\.

**To map a file share on an Amazon EC2 Windows instance using the GUI**

1. Before you can mount a file share on a Windows instance, you must launch the EC2 instance and connect it to an AWS Directory Service for Microsoft Active Directory\. To perform this action, choose one of the following procedures from the *AWS Directory Service Administration Guide*:
   + [Seamlessly Join a Windows EC2 Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/launching_instance.html)
   + [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html)

1. Connect to your instance\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Once connected, open File Explorer\.

1. From the navigation pane, open the context \(right\-click\) menu for **Network** and choose **Map Network Drive**\.

1. Choose a drive letter of your choice for **Drive**\.

1. Enter the fully qualified domain name \(FQDN\) name for your file share\. You construct this name from the FQDN of your file system and the name of your Windows file share\. An example is `\\fs-012345678901234567.ad-domain.com\share` for **Folder**\.

1. Choose whether the file share should **Reconnect at sign\-in** and then choose **Finish**\.

## Step 3: Write Data to Your File Share<a name="getting-started-step3"></a>

Now that you've mapped your file share to your instance, you can use your file share like any other directory in your Windows environment\.

**To write data to your file share**

1. Open the Notepad text editor\.

1. Write some content in the text editor\. For example: *Hello, World\!*

1. Save the file to your file share's drive letter\.

1. Using File Explorer, navigate to your file share and find the text file that you just saved\.

## Step 4: Back Up Your File System<a name="getting-started-step4"></a>

Now that you've had a chance to use your Amazon FSx file system and its file shares, you can back it up\. By default, daily backups are created automatically during your file system's 30\-minute backup window\. However you can create a user\-initiated backup at any time\. Backups have additional costs associated with them\. For more information on backup pricing, see [Pricing](https://aws.amazon.com/fsx/windows/pricing)\.

**To create a backup of your file system from the console**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the console dashboard, choose the name of the file system you created for this exercise\.

1. From the **Overview** tab for your file system, choose **Create backup**\.

1. In the **Create backup** dialog box that opens, provide a name for your backup\. This name can contain a maximum of 256 Unicode letters and include white space, numbers, and the following special characters: \+ \- = \. \_ : / 

1. Choose **Create backup**\.

1. To view all your backups in a list, so you can restore your file system or delete the backup, choose **Backups**\.

When you create a new backup, its status is set to **CREATING** while it is being created\. This can take a few minutes\. When the backup is available for use, its status changes to **AVAILABLE**\.

## Step 5: Clean Up Resources<a name="getting-started-step5"></a>

After you have finished this exercise, you should follow these steps to clean up your resources and protect your AWS account\.

**To clean up resources**

1. On the Amazon EC2 console, terminate your instance\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Windows Instances\.*

1. On the Amazon FSx console, delete your file system\. All automatic backups are deleted automatically\. However, you still need to delete the manually created backups\. The following steps outline this process:

   1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

   1. From the console dashboard, choose the name of the file system you created for this exercise\.

   1. For **Actions**, choose **Delete file system**\.

   1. In the **Delete file system** dialog box that opens, decide whether you want to create a final backup\. If you do, provide a name for the final backup\. Any automatically created backups are also deleted\.
**Important**  
New file systems can be created from backups\. We recommend that you create a final backup as a best practice\. If you find you don't need it after a certain period of time, you can delete this and other manually created backups\.

   1. Enter the ID of the file system that you want to delete in the **File system ID** box\.

   1. Choose **Delete file system**\.

   1. The file system is now being deleted, and its status in the dashboard changes to **DELETING**\. When the file system has been deleted, it no longer appears in the dashboard\.

   1. Now you can delete any manually created backups for your file system\. From the right\-side navigation, choose **Backups**\.

   1. From the dashboard, choose any backups that have the same **File system ID** as the file system that you deleted, and choose **Delete backup**\.

   1. The **Delete backups** dialog box opens\. Leave the check box checked for the ID of the backup you selected, and choose **Delete backups**\.

   Your Amazon FSx file system and related automatic backups are now deleted\.

1. If you created an AWS Directory Service directory for this exercise in [Walkthrough 1: Prerequisites for Getting Started](walkthrough01-prereqs.md), you can delete it now\. For more information, see [Delete Your Directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/simple_ad_delete.html) in the *AWS Directory Service Administration Guide\.*