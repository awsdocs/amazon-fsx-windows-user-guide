# Using Amazon FSx for Windows File Server with Microsoft SQL Server<a name="sql-server"></a>

High availability \(HA\) Microsoft SQL Server is typically deployed across multiple database nodes in a Windows Server Failover Cluster \(WSFC\), with each node having access to shared file storage\. You can use Amazon FSx for Windows File Server as shared storage for High Availability \(HA\) Microsoft SQL Server deployments in two ways: as storage for active data files and as an SMB file share witness\.

## Using Amazon FSx for Active SQL Server Data Files<a name="active-data-files"></a>

Microsoft SQL Server can be deployed with an SMB file share as the storage option for active data files\. Amazon FSx is optimized to provide shared storage for SQL Server databases by supporting Continuously Available \(CA\) file shares, which are designed for applications such as SQL Server that require uninterrupted access to shared file data\.

### Create a Continuously Available Share<a name="ca-share"></a>

You can create continuously available \(CA\) shares using the Amazon FSx CLI for Remote Management on PowerShell\. Use the `New-FSxSmbShare` with the `-ContinuouslyAvailable` option set to $True to specify that the share is a continuously available share\. To learn more about creating a new CA share, see [Creating a Continuously Available Share](managing-file-shares.md#create-ca-share)\.

## Using Amazon FSx as an SMB File Share Witness<a name="smb-share-witness"></a>

Windows Server Failover Cluster deployments commonly deploy an SMB file share witness to maintain quorum of the cluster’s resources\. Witness file shares require only a small amount of storage for quorum information\. Amazon FSx file systems can be used as an SMB file share witness for Windows Server Failover Cluster deployments\.