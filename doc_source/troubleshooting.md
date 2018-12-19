# Troubleshooting<a name="troubleshooting"></a>

## You Can't Access Your File System<a name="unable-to-access"></a>

There are a number of potential causes for being unable to access your file system, each with their own resolution, as follows:

**Potential Cause**  
The Amazon FSx file system your compute instances are connecting to aren't in the same VPC\.

**Resolution**  
Launch your compute instances and create your file system in the same VPC\.

**Potential Cause**  
The security group you've specified for your Amazon FSx file system lacks the required inbound rules\.

**Resolution**  
Make sure that your security group has the inbound rules specified in [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups)\. 

**Potential Cause**  
The security group you've associated with your compute instances lacks the required outbound rules\.

**Resolution**  
Review the inbound rules specified in [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups), and make sure the security group associated with your compute instances has the corresponding outbound rules\.

**Potential Cause**  
Your compute instances are not joined to your AWS Managed Microsoft AD directory to which your file system is joined\. Alternatively, they're joined to an Active Directory that has a one\-way forest trust relationship established with the AWS Managed Microsoft AD directory\.

**Resolution**  
Make sure that your compute instances are joined to one of two types of directory: They should be joined to the AWS Managed Microsoft AD directory to which your file system is joined\. Or they should be joined to an Active Directory that has a one\-way forest trust relationship established with the AWS Managed Microsoft AD directory\.

**Potential Cause**  
The Microsoft Windows file share that you're attempting to access doesn't exist\.

**Resolution**  
If you're using an existing file share, make sure you have the file system DNS name and the share name correctly specified\. To manage your file shares, see [Managing File Shares](using-file-shares.md#managing-file-shares)\.

**Potential Cause**  
The Active Directory user you're accessing the file share as lacks the necessary access permissions\.

**Resolution**  
Make sure the access permissions for the file share and the Windows access control lists \(ACLs\) for the shared folder allow access to the Active Directory users that need to access it\.

**Potential Cause**  
If you've removed the **Allow Full control** NTFS ACL permissions for the **SYSTEM** user on a folder that you shared, that share might have become inaccessible\.

**Resolution**  
Re\-create your file share or shares\. For more information, see [Managing File Shares](using-file-shares.md#managing-file-shares)\. After they're recreated, you can map and use the Windows file shares from your compute instance or instances\.

## Amazon FSx for Windows File Server Forums<a name="fsx-forums-troubleshoot"></a>

If you encounter other issues while using Amazon FSx try the [forums](https://forums.aws.amazon.com/forum.jspa?forumID=308)\.