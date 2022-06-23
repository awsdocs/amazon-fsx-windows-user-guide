# Using FSx for Windows File Server with Microsoft SQL Server<a name="sql-server"></a>

High availability \(HA\) Microsoft SQL Server is typically deployed across multiple database nodes in a Windows Server Failover Cluster \(WSFC\), with each node having access to shared file storage\. You can use FSx for Windows File Server as shared storage for High Availability \(HA\) Microsoft SQL Server deployments in two ways: as storage for active data files and as an SMB file share witness\.

SSD storage is recommended for SQL Server\. SSD storage is designed for the highest\-performance and most latency\-sensitive workloads, including databases\.

For information about using Amazon FSx to reduce complexity and costs for your SQL Server high availability deployments, see the following posts on the *AWS Storage Blog*:
+ [Simplify your Microsoft SQL Server high availability deployments using Amazon FSx for Windows File Server](http://aws.amazon.com/blogs/storage/simplify-your-microsoft-sql-server-high-availability-deployments-using-amazon-fsx-for-windows-file-server/)
+ [Optimizing cost for your high availability SQL Server deployments on AWS](http://aws.amazon.com/blogs/storage/optimizing-cost-for-your-high-availability-sql-server-deployments-on-aws/)
+ [Simplify SQL Server Always On deployments with AWS Launch Wizard and Amazon FSx](http://aws.amazon.com/blogs/storage/simplify-sql-server-always-on-deployments-with-the-aws-launch-wizard-and-amazon-fsx/)

## Using Amazon FSx for Active SQL Server Data Files<a name="active-data-files"></a>

Microsoft SQL Server can be deployed with an SMB file share as the storage option for active data files\. Amazon FSx is optimized to provide shared storage for SQL Server databases by supporting continuously available \(CA\) file shares\. These file shares are designed for applications like SQL Server that require uninterrupted access to shared file data\. While you can create CA shares on Single\-AZ 2 file systems, it is required that you use CA shares on Multi\-AZ file systems for all SQL Server deployments, whether HA or not\. 

### Create a Continuously Available Share<a name="ca-share"></a>

You can create CA shares using the Amazon FSx CLI for Remote Management on PowerShell\. To specify that the share is a continuously available share, use the `New-FSxSmbShare` with the `-ContinuouslyAvailable` option set to `$True`\. To learn more about creating a new CA share, see [Creating a continuously available share](managing-file-shares.md#create-ca-share)\.

### Configure SMB timeout settings<a name="smb-timeout-settings"></a>

As described in [Failover process for FSx for Windows File Server](high-availability-multiAZ.md#MulitAZ-Failover), failover and failback for Multi\-AZ can result in I/O pauses that typically complete in less than 30 seconds\. Your SQL Server application may have different sensitivity to timeout settings depending on how it is configured\.

You can tune the SMB client configuration session timeout to make sure your application is resilient to Multi\-AZ file system failovers\. You can test the behavior of your application during failovers by updating your file system’s throughput capacity, which initiates an automatic failover and failback\.

## Using Amazon FSx as an SMB File Share Witness<a name="smb-share-witness"></a>

Windows Server Failover cluster deployments commonly deploy an SMB file share witness to maintain quorum of the cluster’s resources\. Witness file shares require only a small amount of storage for quorum information\. Amazon FSx file systems can be used as an SMB file share witness for Windows Server Failover Cluster deployments\.