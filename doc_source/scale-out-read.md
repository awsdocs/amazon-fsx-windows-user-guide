# Scaling Out Read Performance with Read Replicas<a name="scale-out-read"></a>

Amazon FSx for Windows File Server provides throughput capacity of up to 2 GB/s per file system\. By using Microsoft’s Distributed File System \(DFS\) Replication, you can replicate your primary Amazon FSx file system data to multiple other Amazon FSx file systems that act as read replicas to achieve scale\-out read performance\.

This solution is targeted at scale\-out read\-heavy workloads that need access to the same file dataset, including data analytics, video transcoding, machine learning, artificial intelligence, and others\. If your workload requires uniformly distributed read/write access to your file data \(for example, if each subset of compute instances accesses a different portion of your file data\), see [Scaling Out Performance with Shards](scale-out-performance.md)\.

## Setting Up DFS Replication to Create Read Replicas<a name="fsx-scaleout-read"></a>

You can use DFS Replication to replicate a file share’s data to multiple other file shares hosted on different Amazon FSx file systems\. With this deployment, you can achieve higher read performance than what’s available from a single file system\. You configure your applications to write to a single file share, ensuring consistency while generating or ingesting data\. This data is replicated to multiple other Amazon FSx file systems using DFS Replication\. Instances running these read\-heavy applications point to different file systems, spreading the read activity across multiple file systems in parallel\. This design allows read performance to scale out as more file systems are added\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/FSx-scale-out-read.png)

**To set up a DFS Replication group**

1. Before you can manage DFS, you must launch the instance and connect it to the AWS Directory Service for Microsoft Active Directory to which you've joined your Amazon FSx file systems\. To perform this action, choose one of the following procedures from the *AWS Directory Service Administration Guide*:
   + [Seamlessly Join a Windows EC2 Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/launching_instance.html)
   + [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html)

1. Connect to your instance as a user in the **AWS Delegated FSx Administrators** group\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open the **Start** menu and type **PowerShell**\. From the list of matches, choose **Windows PowerShell**\.

1. If you don't have DFS Management Tools installed on your instance already, install it with the following command:

   ```
   Install-WindowsFeature RSAT-DFS-Mgmt-Con
   ```

1. From the PowerShell prompt, create a DFS Replication group and folder with the following commands:

   ```
   $Group = "Name of the DFS Replication group"
   $Folder = "Name of the DFS Replication folder"
   
   New-DfsReplicationGroup –GroupName $Group
   Grant-DfsrDelegation –GroupName $Group –AccountName "FSxAdmins" –Force
   New-DfsReplicatedFolder –GroupName $Group –FolderName $Folder
   ```

**To set up the initial DFS Replication connection between the read and write file shares**

1. Determine the Active Directory computer name associated with the write file system and the first read file system with the following commands:

   ```
   $WriteFSDnsName = "fs-abcdef0123456789.example.com"
   $ReadFSDnsName = "fs-0123456789abcdef.example.com"
   $WriteFSComputerName = (Resolve-DnsName ${WriteFSDnsName} -Type CNAME).NameHost
   $ReadFSComputerName = (Resolve-DnsName ${ReadFSDnsName} -Type CNAME).NameHost
   ```

1. Add your first read and write file systems as members of the DFS Replication group with the following commands:

   ```
   Add-DfsrMember -GroupName ${Group} -ComputerName ${WriteFSComputerName}
   Add-DfsrMember -GroupName ${Group} -ComputerName ${ReadFSComputerName}
   ```

1. Add the local path of the share you want to replicate \(for example, D:\\Share\) for each file system to the DFS Replication group with the following commands\. In this solution, the primary file system will serve as the primary member, meaning that its contents will initially be synced to the other file system\.

   ```
   $WriteFSReplicationPath = "D:\Share"
   $ReadFSReplicationPath = "D:\Share"
   
   Set-DfsrMembership –GroupName ${Group} –FolderName ${Folder} –ContentPath ${WriteFSReplicationPath} –ComputerName ${WriteFSComputerName} –PrimaryMember $True -Force
   Set-DfsrMembership –GroupName ${Group} –FolderName ${Folder} –ContentPath ${ReadFSReplicationPath} –ComputerName ${ReadFSComputerName} –PrimaryMember $False -Force
   ```

1. Add a connection between the file share with the following command:

   ```
   Add-DfsrConnection -GroupName ${Group} -SourceComputerName ${WriteFSComputerName} -DestinationComputerName ${ReadFSComputerName}
   ```

Finally, you can configure the DFS Replication connections for additional read file shares with the following commands\. Repeat these commands for each additional file share for read operations that you want to create\.

```
$Group = "Group"
$Folder = "Folder"

$WriteFSDnsName = "fs-abcdef0123456789.example.com"
$ReadFSDnsName = "fs-0123456789abcdef.example.com"
$WriteFSComputerName = (Resolve-DnsName ${WriteFSDnsName} -Type CNAME).NameHost
$ReadFSComputerName = (Resolve-DnsName ${ReadFSDnsName} -Type CNAME).NameHost

Add-DfsrMember -GroupName ${Group} -ComputerName ${ReadFSComputerName}

$WriteFSReplicationPath = "D:\Share"
$ReadFSReplicationPath = "D:\Share"

Set-DfsrMembership –GroupName ${Group} –FolderName ${Folder} –ContentPath ${ReadFSReplicationPath} –ComputerName ${ReadFSComputerName} –PrimaryMember $False -Force

Add-DfsrConnection -GroupName ${Group} -SourceComputerName ${WriteFSComputerName} -DestinationComputerName ${ReadFSComputerName}
```