# Troubleshooting Problems for Amazon FSx<a name="troubleshooting"></a>

Use the following sections to help troubleshoot problems you have with Amazon FSx\. 

If you encounter problems not listed following while using Amazon FSx, try asking a question in the [Amazon FSx forum](https://forums.aws.amazon.com/forum.jspa?forumID=308)\.

**Topics**
+ [You Can't Access Your File System](unable-to-access.md)
+ [Trying to Create an Amazon FSx File System Fails](unable-to-create-fs.md)
+ [File System Is In a Misconfigured State](misconfigured-ad-config.md)
+ [You can't Access Your File System Using Remote PowerShell](remote-pwr-shell.md)
+ [You can't configure Microsoft Distributed File System Replication \(DFS\-R\) on a Multi\-AZ File System](#dfs-r)

## You can't configure Microsoft Distributed File System Replication \(DFS\-R\) on a Multi\-AZ File System<a name="dfs-r"></a>

DFS\-R is not supported on Multi\-AZ file systems\.

**Potential Cause**  
Multi\-AZ file systems are configured for redundancy across multiple access zones natively, and do not support DFS\-R\.

**Resolution**  
Use the Multi\-AZ deployment type for high availability across multiple AZs\. To learn more, see [Availability and Durability: Single\-AZ and Multi\-AZ File Systems](high-availability-multiAZ.md)\.