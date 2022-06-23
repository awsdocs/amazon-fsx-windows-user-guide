# Additional information<a name="additional-info"></a>

This section provides a reference of supported, but deprecated Amazon FSx features\.

**Topics**
+ [Setting up a custom backup schedule](#custom-backup-schedule)
+ [Using Microsoft Distributed File System Replication](#using-dfsr)

## Setting up a custom backup schedule<a name="custom-backup-schedule"></a>

We recommend using AWS Backup to set up a custom backup schedule for your file system\. The information provided here is for reference purposes if you need to schedule backups more frequently than you can when using AWS Backup\.

When enabled, Amazon FSx for Windows File Server automatically takes a backup of your file system once a day during a daily backup window\. Amazon FSx enforces a retention period that you specify for these automatic backups\. It also supports user\-initiated backups, so you can make backups at any point\.

Following, you can find the resources and configuration to deploy custom backup scheduling\. Custom backup scheduling performs user\-initiated backups on an Amazon FSx file system on a custom schedule that you define\. Examples might be once every six hours, once every week, and so on\. This script also configures deleting backups older than your specified retention period\.

The solution automatically deploys all the components needed, and takes in the following parameters:
+ The file system
+ A CRON schedule pattern for performing backups
+ The backup retention period \(in days\)
+ The backup name tags

For more information on CRON schedule patterns, see [Schedule Expressions for Rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html) in the Amazon CloudWatch User Guide\.

### Architecture overview<a name="fsx-custom-backup-overview"></a>

Deploying this solution builds the following resources in the AWS Cloud\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/fsx-custom-backup-architecture.png)

This solution does the following:

1. The AWS CloudFormation template deploys an CloudWatch Event, a Lambda function, an Amazon SNS queue, and an IAM role\. The IAM role gives the Lambda function permission to invoke the Amazon FSx API operations\.

1. The CloudWatch event runs on a schedule you define as a CRON pattern, during the initial deployment\. This event invokes the solution’s backup manager Lambda function that invokes the Amazon FSx `CreateBackup` API operation to initiate a backup\.

1. The backup manager retrieves a list of existing user\-initiated backups for the specified file system using `DescribeBackups`\. It then deletes backups older than the retention period, which you specify during the initial deployment\.

1. The backup manager sends a notification message to the Amazon SNS queue on a successful backup if you choose the option to be notified during the initial deployment\. A notification is always sent in the event of a failure\.

### AWS CloudFormation template<a name="fsx-custom-backup-template"></a>

This solution uses AWS CloudFormation to automate the deployment of the Amazon FSx custom backup scheduling solution\. To use this solution, download the [fsx\-scheduled\-backup\.template](https://s3.amazonaws.com/solution-references/fsx/backup/fsx-scheduled-backup.template?versionId=jS8qpvWUjSn_YH6dZ6gBUFmfq2wQAM_Y) AWS CloudFormation template\.

### Automated deployment<a name="fsx-custom-backup-deployment"></a>

The following procedure configures and deploys this custom backup scheduling solution\. It takes about five minutes to deploy\. Before you start, you must have the ID of an Amazon FSx file system running in an Amazon Virtual Private Cloud \(Amazon VPC\) in your AWS account\. For more information on creating these resources, see [Getting started with Amazon FSx](getting-started.md)\.

**Note**  
Implementing this solution incurs billing for the associated AWS services\. For more information, see the pricing details pages for those services\.

**To launch the custom backup solution stack**

1. Download the [fsx\-scheduled\-backup\.template](https://s3.amazonaws.com/solution-references/fsx/backup/fsx-scheduled-backup.template?versionId=jS8qpvWUjSn_YH6dZ6gBUFmfq2wQAM_Y) AWS CloudFormation template\. For more information on creating an AWS CloudFormation stack, see [Creating a Stack on the AWS CloudFormation Console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in the *AWS CloudFormation User Guide*\.
**Note**  
By default, this template launches in the US East \(N\. Virginia\) AWS Region\. Amazon FSx is currently only available in specific AWS Regions\. You must launch this solution in an AWS Region where Amazon FSx is available\. For more information, see the Amazon FSx section of [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference\. *

1. For **Parameters**, review the parameters for the template and modify them for the needs of your file system\. This solution uses the following default values\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/additional-info.html)

1. Choose **Next**\.

1. For **Options**, choose **Next**\.

1. For **Review**, review and confirm the settings\. You must select the check box acknowledging that the template create IAM resources\.

1. Choose **Create** to deploy the stack\.

You can view the status of the stack in the AWS CloudFormation console in the **Status** column\. You should see a status of **CREATE\_COMPLETE** in about five minutes\.

### Additional options<a name="fsx-custom-backup-supplemental"></a>

You can use the Lambda function created by this solution to perform custom scheduled backups of more than one Amazon FSx file system\. The file system ID is passed to the Amazon FSx function in the input JSON for the CloudWatch event\. The default JSON passed to the Lambda function is as follows, where the values for `FileSystemId` and `SuccessNotification` are passed from the parameters specified when launching the AWS CloudFormation stack\.

```
{
	"start-backup": "true",
	"purge-backups": "true",
	"filesystem-id": "${FileSystemId}",
	"notify_on_success": "${SuccessNotification}"
}
```

To schedule backups for an additional Amazon FSx file system, create another CloudWatch event rule\. You do so using the Schedule event source, with the Lambda function created by this solution as the target\. Choose **Constant \(JSON text\)** under **Configure Input**\. For the JSON input, simply substitute the file system ID of the Amazon FSx file system to back up in place of `${FileSystemId}`\. Also, substitute either `Yes` or `No` in place of `${SuccessNotification}` in the JSON above\.

Any additional CloudWatch Event rules you create manually aren't part of the Amazon FSx custom scheduled backup solution AWS CloudFormation stack\. Thus, they aren't removed if you delete the stack\.

## Using Microsoft Distributed File System Replication<a name="using-dfsr"></a>

**Note**  
To implement high availability for an FSx for Windows File Server, we recommend using Amazon FSx Multi\-AZ\. For more information about Amazon FSx Multi\-AZ, see [Availability and durability: Single\-AZ and Multi\-AZ file systems](high-availability-multiAZ.md)

Amazon FSx supports the use of the Microsoft Distributed File System \(DFS\) for file system deployments across multiple Availability Zones \(AZs\) to get Multi\-AZ availability and durability\. Using DFS Replication, you can automatically replicate data between two file systems\. Using DFS Namespaces, you can configure one file system as your primary and the other as your standby, with automatic failover to the standby if the primary becomes unresponsive\. 

Before using DFS Replication, take the following steps:
+ Set up your security groups as described in [Step 8](getting-started-step1.md#security_group_setup) of Getting Started with Amazon FSx\.
+ Create two Amazon FSx file systems in different AZs within an AWS Region\. For more information on creating your file systems, see [Step 3: Write data to your file share](getting-started-step3.md)\.
+ Ensure that both file systems are in the same AWS Directory Service for Microsoft Active Directory\.
+ After the file systems are created, note their file system IDs for later on\.

In the following topics, you can find a description of how to set up and use DFS Replication and DFS Namespaces failover across AZs with Amazon FSx\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/MultiAZDiagram.png)

### Setting Up DFS Replication<a name="set-up-fsx-dfs"></a>

You can use DFS Replication to automatically replicate data between two Amazon FSx file systems\. This replication is bidirectional, meaning that you can write to either file system and the changes are replicated to the other\.

**Important**  
 You can't use the DFS Management UI in the Microsoft Windows Administrative Tools \(dfsmgmt\.msc\) to configure DFS Replication on your FSx for Windows File Server file system\. 

#### To Set Up DFS Replication \(Scripted\)<a name="win-server-2016-scripted.title"></a>

1. Begin the process of managing DFS by launching your instance and connecting it to the Microsoft Active Directory where you joined your Amazon FSx file systems\. To do this, choose one of the following procedures from the *AWS Directory Service Administration Guide*:
   + [Seamlessly Join a Windows EC2 Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/launching_instance.html)
   + [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html)

1. Connect to your instance as an Active Directory user that is a member of the file system administrators group\. In AWS Managed AD, this group is called AWS Delegated FSx Administrators\. In your self\-managed Microsoft AD, this group is called Domain Admins or the custom name for the administrators group that you provided during creation\. 

   This user must also be a member of a group that has DFS administration permissions delegated to it\. In AWS Managed AD, this group is called AWS Delegated Distributed File System Administrators\. In your self\-managed AD, this user must be a member of Domain Admins or another group to which you delegated DFS administration permissions\. 

   For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Download the [FSx\-DFSr\-Setup\.ps1 PowerShell script](https://s3.amazonaws.com/solution-references/fsx/dfs/FSx-DFSr-Setup.ps1                 )\.

1. Open the **Start** menu and enter **PowerShell**\. From the list, choose **Windows PowerShell**\.

1. Run the PowerShell script with the following specified parameters to establish DFS Replication between your two file systems:
   + The names of the DFS Replication group and folder
   + The local path to the folder that you want to replicate on your file systems \(for example, `D:\share` for the default share that comes included with your Amazon FSx file system\)
   + The DNS names of the primary and standby Amazon FSx file systems you created in the prerequisite steps  
**Example**  

   ```
   FSx-DFSr-Setup.ps1 -group Group -folder Folder -path ContentPath -primary FSxFileSystem1-DNS-Name -standby FSxFileSystem2-DNS-Name
   ```

#### To Set Up DFS Replication \(Step by Step\)<a name="win-server-2016"></a>

1. Begin the process of managing DFS by launching your instance and connecting it to the Microsoft Active Directory where you joined your Amazon FSx file systems\. To do this, choose one of the following procedures from the *AWS Directory Service Administration Guide*:
   + [Seamlessly Join a Windows EC2 Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/launching_instance.html)
   + [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html)

1. Connect to your instance as an Active Directory user that is a member of the file system administrators group\. In AWS Managed AD, this group is called AWS Delegated FSx Administrators\. In your self\-managed Microsoft AD, this group is called Domain Admins or the custom name for the administrators group that you provided during creation\. 

   This user must also be a member of a group that has DFS administration permissions delegated to it\. In AWS Managed AD, this group is called AWS Delegated Distributed File System Administrators\. In your self\-managed AD, this user must be a member of Domain Admins or another group to which you delegated DFS administration permissions\. 

   For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open the **Start** menu and enter **PowerShell**\. From the list, choose **Windows PowerShell**\.

1. If you don't have DFS Management Tools installed already, install them on your instance with the following command\.

   ```
   Install-WindowsFeature RSAT-DFS-Mgmt-Con
   ```

1. From the PowerShell prompt, create a DFS Replication group and folder with the following commands\.

   ```
   $Group = "Name of the DFS Replication group"
   $Folder = "Name of the DFS Replication folder"
   
   New-DfsReplicationGroup –GroupName $Group
   New-DfsReplicatedFolder –GroupName $Group –FolderName $Folder
   ```

1. Determine the Active Directory computer name associated with each file system with the following commands\.

   ```
   $Primary = "DNS name of the primary FSx file system"
   $Standby = "DNS name of the standby FSx file system"
   
   $C1 = (Get-ADObject -Filter "objectClass -eq 'Computer' -and ServicePrincipalName -eq 'HOST/$Primary'").Name
   $C2 = (Get-ADObject -Filter "objectClass -eq 'Computer' -and ServicePrincipalName -eq 'HOST/$Standby'").Name
   ```

1. Add your file systems as members of the DFS Replication group that you created with the following commands\.

   ```
   Add-DfsrMember –GroupName $Group –ComputerName $C1
   Add-DfsrMember –GroupName $Group –ComputerName $C2
   ```

1. Use the following commands to add the local path \(for example, `D:\share`\) for each file system to the DFS Replication group\. In this procedure, `file system 1` serves as the primary member, meaning that its contents initially are synced to the other file system\.

   ```
   $ContentPath1 = "Local path to the folder you want to replicate on file system 1"
   $ContentPath2 = "Local path to the folder you want to replicate on file system 2"
                   
   Set-DfsrMembership –GroupName $Group –FolderName $Folder –ContentPath $ContentPath1 –ComputerName $C1 –PrimaryMember $True
   Set-DfsrMembership –GroupName $Group –FolderName $Folder –ContentPath $ContentPath2 –ComputerName $C2 –PrimaryMember $False
   ```

1. Add a connection between the file systems with the following command\.

   ```
   Add-DfsrConnection –GroupName $Group –SourceComputerName $C1 –DestinationComputerName $C2
   ```

Within minutes, both file systems should begin synchronizing the contents of the `ContentPath` specified preceding\.

### Setting Up DFS Namespaces For Failover<a name="set-up-dfs-namespace"></a>

You can use DFS Namespaces to treat one file system as your primary, and the other as your standby\. By doing this, you can configure automatic failover to the standby if the primary becomes unresponsive\. DFS Namespaces enables you to group shared folders on different servers into a single Namespace, where a single folder path can lead to files stored on multiple servers\. DFS Namespaces are managed by DFS Namespace servers, which direct compute instances mapping a DFS Namespace folder to the appropriate file servers\.

#### To Set Up DFS Namespaces for Failover \(UI\)<a name="gui"></a>

1. If you don't already have DFS Namespace servers running, launch a pair of highly available DFS Namespace servers using the [setup\-DFSN\-servers\.template](https://s3.amazonaws.com/solution-references/fsx/dfs/setup-DFSN-servers.template) AWS CloudFormation template\. For more information on creating an AWS CloudFormation stack, see [Creating a Stack on the AWS CloudFormation Console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in the *AWS CloudFormation User Guide*\.

1. Connect to one of the DFS Namespace servers launched in the previous step as a user in the AWS Delegated Administrators group\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open the DFS Management console\. Open the **Start** menu and run `dfsmgmt.msc`\. Doing this opens the DFS Management GUI tool\.

1. For **Action**, choose **New Namespace**, and enter the computer name of the first DFS Namespace server that you launched for **Server** and choose **Next**\.

1. For **Name**, enter the namespace you're creating \(for example, **corp**\)\.

1. Choose **Edit Settings** and set the appropriate permissions based on your requirements\. Choose **Next**\.

1. Keep the default **Domain\-based namespace** option selected, keep the **Enable Windows Server 2008 mode** option selected, and choose **Next**\.
**Note**  
Windows Server 2008 mode is the latest available option for Namespaces\.

1. Review the namespace settings and choose **Create**\.

1. With the newly created namespace selected under **Namespaces** in the navigation bar, choose **Action**, then **Add Namespace Server**\.

1. For **Namespace server**, enter the computer name of the second DFS Namespace server that you launched\. 

1. Choose **Edit Settings**, set the appropriate permissions based on your requirements, and choose **OK**\.

1. Choose **Add**, enter the UNC name of the file share on the primary Amazon FSx file system \(for example \\\\*fs\-0123456789abcdef0*\.example\.com\\*share*\) for Path to folder target, and choose **OK**\.

1. Choose **Add**, enter the UNC name of the file share on the standby Amazon FSx file system \(for example, \\\\*fs\-fedbca9876543210f*\.example\.com\\*share*\) for Path to folder target, and choose **OK\.**

1. From the **New Folder** window, choose **OK**\. The new folder is created with the two folder targets under your namespace\.

1. Repeat the last three steps for each file share that you want to add to your namespace\.

#### To Set Up DFS Namespaces for Failover \(PowerShell\)<a name="powershell"></a>

1. If you don't already have DFS Namespace servers running, launch a pair of highly available DFS Namespace servers using the [setup\-DFSN\-servers\.template](https://s3.amazonaws.com/solution-references/fsx/dfs/setup-DFSN-servers.template) AWS CloudFormation template\. For more information on creating an AWS CloudFormation stack, see [Creating a Stack on the AWS CloudFormation Console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in the *AWS CloudFormation User Guide*\.

1. Connect to one of the DFS Namespace servers launched in the previous step as a user in the **AWS Delegated Administrators** group\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open the **Start** menu and enter **PowerShell**\. **Windows PowerShell** appears in the list of matches\.

1. Open the context \(right\-click\) menu for **Windows PowerShell** and choose **Run as Administrator**\.

1. If you don't have DFS Management Tools installed already, install it on your instance with the following command\.

   ```
   Install-WindowsFeature RSAT-DFS-Mgmt-Con
   ```

1. If you don't already have an existing DFS Namespace, you can create one using the following PowerShell commands\.

   ```
   $NSS1 = computer name of the 1st DFS Namespace server
   $NSS2 = computer name of the 2nd DFS Namespace server
   
   $DNSRoot = fully qualified Active Directory domain name (e.g. mydomain.com)
   $Namespace = Namespace name you want to use
   $Folder = Folder path you want to use within the Namespace
   $FS1FolderTarget = Share path to Folder Target on File System 1
   $FS2FolderTarget = Share path to Folder Target on File System 2
   
   $NSS1,$NSS2 | ForEach-Object { Invoke-Command –ComputerName $_ –ScriptBlock { mkdir “C:\DFS\${using:Namespace}”;
   New-SmbShare –Name ${using:Namespace} –Path “C:\DFS\${using:Namespace}” } }
   
   New-DfsnRoot -Path "\\${DNSRoot}\${Namespace}" -TargetPath "\\${NSS1}.${DNSRoot}\${Namespace}" -Type DomainV2
   New-DfsnRootTarget -Path "\\${DNSRoot}\${Namespace}" -TargetPath "\\${NSS2}.${DNSRoot}\${Namespace}"
   ```

1. To create a folder within your DFS Namespace, you can use the following PowerShell command\. Doing this creates a folder that directs compute instances accessing the folder to your primary Amazon FSx file system by default\.

   ```
   $FS1 = DNS name of primary FSx file system
   New-DfsnFolder –Path “\\${DNSRoot}\${Namespace}\${Folder}" -TargetPath “\\${FS1}\${FS1FolderTarget}” –EnableTargetFailback $True –ReferralPriorityClass GlobalHigh
   ```

1. Add your standby Amazon FSx file system to the same DFS Namespace folder\. Compute instances accessing the folder fall back to this file system if they can't connect to the primary Amazon FSx file system\.

   ```
   $FS2 = DNS name of secondary FSx file system
   New-DfsnFolderTarget –Path “\\${DNSRoot}\${Namespace}\${Folder}" -TargetPath “\\${FS2}\${FS2FolderTarget}”
   ```

You can now access your data from compute instances using the DFS Namespace folder's remote path specified preceding\. Doing this directs the compute instances to the primary Amazon FSx file system \(and to the standby file system, if the primary is unresponsive\)\.

For example, open the **Start** menu and enter `PowerShell`\. From the list, choose `Windows PowerShell` and run the following command\.

```
net use Z: \\${DNSRoot}\${Namespace}\${Folder} /persistent:yes
```

### Working with Maintenance Windows and FSx Multi\-AZ<a name="best-practices"></a>

To help ensure high availability of your Multi\-AZ file system deployment, we recommend that you pick nonoverlapping maintenance windows for the two Amazon FSx file systems in your Multi\-AZ deployment\. Doing this helps ensure that your file data continues to be available to your applications and users during system maintenance windows\. 

**Note**  
To allow DFS Replication traffic to and from the file systems, make sure that you add VPC security group inbound and outbound rules as described in [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups)\.