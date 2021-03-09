# Cutting over to Amazon FSx<a name="cutover-to-fsx"></a>

To cut over to your Amazon FSx for Windows File Server file system, you perform the following steps:
+ Prepare for the cut over\.
  + Temporarily disconnect SMB clients from the original file system\.
  + Perform a final file and file share configuration sync\.
+ Configure service principal names \(SPNs\) for your Amazon FSx file system\.
+ Update DNS CNAME records to point to your Amazon FSx file system\.

The procedures to perform each of these steps are provided in the following sections\.

**Topics**
+ [Preparing for the cutover to Amazon FSx](#final-sync-and-disconnect)
+ [Configure SPNs for Kerberos authentication](#configure-spns)
+ [Update the DNS CNAME records for the Amazon FSx file system](#update-dns-cname)

## Preparing for the cutover to Amazon FSx<a name="final-sync-and-disconnect"></a>

To prepare for the cutover to your Amazon FSx file system, you must do the following:
+ Disconnect all clients that write to the original file system\.
+ Perform a final file sync using AWS DataSync or Robocopy\. For more information, see [Migrating existing file storage to Amazon FSx for Windows File Server](migrate-files-fsx.md)\. 
+ Perform a final file share configuration sync\. For more information, see [Migrating file share configurations to Amazon FSx](migrate-file-share-config-to-fsx.md)\.

## Configure SPNs for Kerberos authentication<a name="configure-spns"></a>

We recommend that you use Kerberos\-based authentication and encryption in transit with Amazon FSx\. Kerberos provides the most secure authentication for clients that access your file system\. To enable Kerberos authentication for clients accessing Amazon FSx using a DNS alias, you must add service principal names \(SPNs\) that correspond to the DNS alias on your Amazon FSx file system’s Active Directory computer object\. 

There are two required SPNs for Kerberos authentication\.

```
HOST/alias
HOST/alias.domain
```

As an example, if the alias is `finance.domain.com`, the two required SPNs are as follows\.

```
HOST/finance
HOST/finance.domain.com
```

An SPN can only be associated with a single Active Directory computer object at a time\. If there are existing SPNs for the DNS name configured for your original file system's Active Directory computer object, you must delete them before creating SPNs for your Amazon FSx file system\.

The following procedures describe how to find any existing SPNs, delete them, and create new SPNs for your Amazon FSx file system's Active Directory computer object\.

**To install the required PowerShell Active Directory module**

1. Log on to a Windows instance joined to the Active Directory that your Amazon FSx file system is joined to\.

1. Open PowerShell as administrator\.

1. Install the PowerShell Active Directory module using the following command\.

   ```
   Install-WindowsFeature RSAT-AD-PowerShell
   ```<a name="finddelete-existing-spn"></a>

**To find and delete existing DNS alias SPNs on the original file system's Active Directory computer object**

1.  Find any existing SPNs by using the following commands\. Replace `alias_fqdn` with the DNS alias that you associated with the file system in [Migrating DNS configuration to use Amazon FSx](migrate-dns-config.md)\.

   ```
   ## Find SPNs for original file system's AD computer object
   $ALIAS = "alias_fqdn"
   SetSPN /Q ("HOST/" + $ALIAS)
   SetSPN /Q ("HOST/" + $ALIAS.Split(".")[0])
   ```

1. Delete the existing HOST SPNs returned in the previous step by using the following example script\.
   + Replace `alias_fqdn` with the full DNS alias that you associated with the file system in [Migrating DNS configuration to use Amazon FSx](migrate-dns-config.md)\.
   + Replace `file_system_DNS_name` with the original file system's DNS name \.

   ```
   ## Delete SPNs for original file system's AD computer object
   $Alias = "alias_fqdn"
   $FileSystemDnsName = "file_system_dns_name"
   $FileSystemHost = (Resolve-DnsName ${FileSystemDnsName} | Where Type -eq 'A')[0].Name.Split(".")[0]
   $FSxAdComputer = (Get-AdComputer -Identity ${FileSystemHost})
   
   SetSPN /D ("HOST/" + ${Alias}) ${FSxAdComputer}.Name
   SetSPN /D ("HOST/" + ${Alias}.Split(".")[0]) ${FSxAdComputer}.Name
   ```

1. Repeat these steps for each DNS alias that you associated with the file system in [Migrating DNS configuration to use Amazon FSx](migrate-dns-config.md)\.

**To set SPNs on your Amazon FSx file system’s Active Directory computer object**

1. Set new SPNs for your Amazon FSx file system by running the following commands\.
   + Replace `file_system_DNS_name` with the DNS name that Amazon FSx assigned to the file system\. 

     To find your file system's DNS name on the Amazon FSx console, choose **File systems**, and choose your file system\. Choose the **Network & security** pane of the file system details page\. You can also get the DNS name in the response of the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\.
   + Replace `alias_fqdn` with the full DNS alias that you associated with the file system in [Migrating DNS configuration to use Amazon FSx](migrate-dns-config.md)\.

   ```
   ## Set SPNs for FSx file system AD computer object
   $FSxDnsName = "file_system_DNS_name"
   $Alias = "alias_fqdn"
   $FileSystemHost = (Resolve-DnsName $FSxDnsName | Where Type -eq 'A')[0].Name.Split(".")[0]
   $FSxAdComputer = (Get-AdComputer -Identity $FileSystemHost)
   
   Set-AdComputer -Identity $FSxAdComputer -Add @{"msDS-AdditionalDnsHostname"="$Alias"}
   SetSpn /S ("HOST/" + $Alias.Split('.')[0]) $FSxAdComputer.Name
   SetSpn /S ("HOST/" + $Alias) $FSxAdComputer.Name
   ```
**Note**  
Setting an SPN for your Amazon FSx file system will fail if an SPN for the DNS alias exists in the AD for the original file system's computer object\. For information about finding and deleting existing SPNs, see [To find and delete existing DNS alias SPNs on the original file system's Active Directory computer object](#finddelete-existing-spn)\.

1. Verify that the new SPNs are configured for the DNS alias using the following example script\. Ensure that the response includes two HOST SPNs, `HOST/alias` and `HOST/alias_fqdn`\.

   Replace `file_system_DNS_name` with the DNS name that Amazon FSx assigned to your file system\. To find your file system's DNS name on the Amazon FSx console, choose **File systems**, choose your file system, and then choose the **Network & security** pane on the file system details page\. 

   You can also get the DNS name in the response of the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\.

   ```
   ## Verify SPNs on FSx file system AD computer object
   $FileSystemDnsName = "file_system_dns_name"
   $FileSystemHost = (Resolve-DnsName ${FSxDnsName} | Where Type -eq 'A')[0].Name.Split(".")[0]
   $FSxAdComputer = (Get-AdComputer -Identity ${FileSystemHost})
   SetSpn /L ${FSxAdComputer}.Name
   ```

1. Repeat the previous steps for each DNS alias that you've associated with the file system in [Migrating DNS configuration to use Amazon FSx](migrate-dns-config.md)\.

**Note**  
You can enforce Kerberos authentication and encryption in transit with clients connecting to your file system using DNS aliases by setting the following Group Policy Objects \(GPOs\) in your Active Directory:  
Restrict NTLM: Outgoing NTLM traffic to remote servers
Restrict NTLM: Add remote server exceptions for NTLM authentication
For more information, see [Enforcing Kerberos authentication using GPOs](walkthrough05-file-system-custom-CNAME.md#enforce-kerberos) in *Walkthrough 5: Using DNS aliases to access your file system*\.

## Update the DNS CNAME records for the Amazon FSx file system<a name="update-dns-cname"></a>

After you properly configure SPNs for your file system, you can cut over to Amazon FSx by replacing each DNS record that resolved to the original file system with a DNS record that resolves to the default DNS name of the Amazon FSx file system\.

**To install the required PowerShell cmdlets**

1. Log on to a Windows instance joined to the Active Directory that your Amazon FSx file system is joined to as a user that is a member of a group that has DNS administration permissions \(**AWS Delegated Domain Name System Administrators** inAWS Managed Microsoft Active Directory, and **Domain Admins** or another group to which you've delegated DNS administration permissions in your self\-managed Active Directory\) 

   For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open PowerShell as administrator\.

1. The PowerShell DNS server module is required to perform the instructions in this procedure\. Install it using the following command\.

   ```
   Install-WindowsFeature RSAT-DNS-Server
   ```

**To update an existing a DNS CNAME record**

1. The following script updates any existing DNS CNAME records for the `alias_fqdn` to your Amazon FSx file system's computer object\. If none is found, it creates a new DNS CNAME record for the DNS alias `alias_fqdn` that resolves to the default DNS name for your Amazon FSx file system\. 

   To run the script:
   + Replace `alias_fqdn` with the DNS alias that you associated with the file system\.
   + Replace `file_system_DNS_name` with the default DNS name Amazon FSx has assigned to the file system\.

   ```
   $Alias="alias_fqdn"
   $FSxDnsName="file_system_dns_name"
   $AliasHost=$Alias.Split('.')[0]
   $ZoneName=((Get-WmiObject Win32_ComputerSystem).Domain)
   $DnsServerComputerName = (Resolve-DnsName $ZoneName -Type NS | Where Type -eq 'A' | Select -ExpandProperty Name)[0]
   
   Add-DnsServerResourceRecordCName -Name $AliasHost -ComputerName $DnsServerComputerName -HostNameAlias $FSxDnsName -ZoneName $ZoneName
   ```

1. Repeat the previous step for each DNS alias that you associated with the file system in [Migrating DNS configuration to use Amazon FSx](migrate-dns-config.md)\.