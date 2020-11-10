# You can't access your file system<a name="unable-to-access"></a>

There are a number of potential causes for being unable to access your file system, each with their own resolution, as follows\.

## The file system elastic network interface was modified or deleted<a name="eni-deleted"></a>

You must not modify or delete the file system's elastic network interface\. Modifying or deleting the network interface can cause a permanent loss of connection between your VPC and your file system\. Create a new file system, and do not modify or delete the Amazon FSx elastic network interface\. For more information, see [ File System Access Control with Amazon VPC ](limit-access-security-groups.md)\. 

## The Elastic IP address attached to the file system elastic network interface was deleted<a name="eni-epi-removed"></a>

Amazon FSx doesn't support accessing file systems from the public internet\. Amazon FSx automatically detaches any Elastic IP address, which is a public IP address reachable from the internet, that gets attached to a file system's elastic network interface\. For more information, see [Supported Clients, Access Methods, and Environments for Amazon FSx for Windows File ServerSupported Clients, Access Methods, and Environments](supported-fsx-clients.md)\.

## The file system security group lacks the required inbound or outbound rules\.<a name="sg-lacks-inbound-rules"></a>

Review the inbound rules specified in [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups), and make sure that the security group associated with your file system has the corresponding inbound rules\. 

## The compute instance's security group lacks the required outbound rules<a name="compute-instance-lacks-inbound-rules"></a>

Review the outbound rules specified in [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups), and make sure that the security group associated with your compute instance has the corresponding outbound rules\.

## Compute instance not joined to an Active Directory<a name="fs-not-joined-to-ad"></a>

Your compute instances might not be correctly joined to one of two types of Active Directory:
+ The AWS Managed Microsoft AD directory to which your file system is joined\.
+ A Microsoft Active Directory directory that has a one\-way forest trust relationship established with the AWS Managed Microsoft AD directory\.

Make sure that your compute instances are joined to one of two types of directory\. One type is the AWS Managed Microsoft AD directory to which your file system is joined\. The other type is a Microsoft AD directory that has a one\-way forest trust relationship established with the AWS Managed Microsoft AD directory\. For more information, see [Using Amazon FSx with AWS Directory Service for Microsoft Active Directory](fsx-aws-managed-ad.md)\.

## The file share doesn't exist<a name="file-share-doesnt-exist"></a>

The Microsoft Windows file share that you're attempting to access doesn't exist\.

If you're using an existing file share, make sure that the file system DNS name and the share name are correctly specified\. To manage your file shares, see [File Shares](managing-file-shares.md)\.

## Active Directory user lacks required permissions<a name="ad-user-lacks-permission"></a>

The Active Directory user that you're accessing the file share as lacks the necessary access permissions\.

Make sure that the access permissions for the file share and Windows access control lists \(ACLs\) for the shared folder allow access to the Active Directory users that need to access it\.

## Allow Full control NTFS ACL permissions removed<a name="removed-allow-full-control"></a>

If you remove **Allow Full control** NTFS ACL permissions for the SYSTEM user on a folder that you shared, that share can become inaccessible\.

You will need to re\-create the affected file share\. For more information, see [File Shares](managing-file-shares.md)\. After you recreate the folder or share, you can map and use the Windows file shares from your compute instances\.

## Can't access a file system using an on\-premises client<a name="non-private-ips-onprem"></a>

You're using your Amazon FSx file system from on\-premises using AWS Direct Connect or VPN, and you're using a non\-private IP address range for the on\-premises client\.

Use a private IP address range for the on\-premises client\. For more information, see [Accessing Amazon FSx for Windows File Server File Systems from On\-Premises](supported-fsx-clients.md#on-premise-access)\.

## New file system is not registered in DNS<a name="fs-dns-not-registered"></a>

For file systems joined to a self\-managed Active Directory, Amazon FSx did not register the file system DNS when it was created because the customer network does not use Microsoft DNS\.

Amazon FSx does not register file systems in DNS if your network uses a third\-party DNS service instead of Microsoft DNS\. You must manually set up DNS A entries for your Amazon FSx file systems\. For Single\-AZ 1 file systems, you will need to add one DNS A entry; for Single\-AZ 2 and Multi\-AZ file systems, you will need to add two DNS A entries\. Use the following procedure to obtain the file system IP address or addresses to use when manually adding the DNS A entries\. 

1. In the [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/), choose the file system that you want to obtain the IP address of to display the file system details page\.

1. In the **Network & security** tab do one of the following:
   + For a Single\-AZ 1 file system:
     + In the **Subnet** panel, choose the elastic network interface shown under **Network interface** to open the **Network Interfaces** page in the Amazon EC2 console\.
     + The IP address for the Single\-AZ 1 file system to use is shown in the **Primary private IPv4 IP** column\.
   + For a Single\-AZ 2 or Multi\-AZ file system:
     + In the **Preferred subnet** panel, choose the elastic network interface shown under **Network interface** to open the **Network Interfaces** page in the Amazon EC2 console\.
     + The IP address for the preferred subnet to use is shown in the **Secondary private IPv4 IP** column\.
     + In the Amazon FSx **Standby subnet** panel, choose the elastic network interface shown under **Network interface** to open the **Network Interfaces** page in the Amazon EC2 console\.
     + The IP address for the standby subnet to use is shown in the **Secondary private IPv4 IP** column\.

## Can't access the file system using a DNS alias<a name="cant-connect-using-dns-alias"></a>

If you're unable to access a file system using a DNS alias, use the following procedure to troubleshoot the issue\.

1. Verify that the alias is associated with the file system by doing either of the following steps:

   1. **Using the Amazon FSx console** – Choose the file system that you're trying to access\. On the **File system details** page, the **DNS aliases** are shown on the **Network & security** tab\.

   1. **Using the CLI or API** – Use the `describe-file-system-aliases` CLI command, or the [DescribeFileSystemAliases](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystemAliases.html) API operation to retrieve the aliases currently associated with the file system\.

1. If the DNS alias is not listed, you must associate it with the file system\. For more information, see [Managing DNS aliases on existing file systems](managing-dns-aliases.md#manage-aliases-existing-fs)\.

1. If the DNS alias is associated with the file system, verify that you've also configured the following required items:
   + Created service principal names \(SPNs\) corresponding to the DNS alias on your Amazon FSx file system's Active Directory computer object\.

     For more information, see [Step 2: Configure service principal names \(SPNs\) for Kerberos](walkthrough05-file-system-custom-CNAME.md#step2-configure-spn-kerberos)\.
   + Created a DNS CNAME record for the DNS alias that resolves to the default DNS name of the Amazon FSx file system\.

     For more information, see [Step 3: Update or create a DNS CNAME record for the file system](walkthrough05-file-system-custom-CNAME.md#step4-configure-dns-cname)\.

1. If you created valid SPNs and a DNS CNAME record, verify that the client's DNS has the DNS CNAME record that resolves to the correct file system\.

   1. Run `nslookup` to confirm that the record exists and that it resolves to the file system's default DNS name\.

   1. If the DNS CNAME resolves to another file system, wait for the client's DNS cache to refresh, and then check the CNAME record again\. You can accelerate the process by flushing the client's DNS cache using the following command\.

      ```
      ipconfig /flushdns
      ```

1. If the DNS CNAME record resolves to the Amazon FSx file system's default DNS, and the client is still unable to access the file system, see [You can't access your file system ](#unable-to-access) for additional troubleshooting steps\.