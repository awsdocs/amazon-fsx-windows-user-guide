# Walkthrough 5: Using DNS aliases to access your file system<a name="walkthrough05-file-system-custom-CNAME"></a>

FSx for Windows File Server provides a default Domain Name System \(DNS\) name for every file system that you can use to access the data on your file system\. You can also access your file systems using a DNS alias of your choosing\. With DNS aliases, you can continue using existing DNS names to access data stored on Amazon FSx when migrating file system storage from on\-premises to Amazon FSx, without needing to update any tools or applications\. You can associate up to 50 DNS aliases with a file system at any one time\. 

To access your Amazon FSx file systems using DNS aliases, you must perform the following three steps:

1. Associate DNS aliases with your Amazon FSx file system\.

1. Configure service principal names \(SPNs\) for your file system’s computer object\. \(This is required to get Kerberos authentication when accessing your file system using DNS aliases\.\)

1. Update or create a DNS CNAME record for the file system and the DNS alias\.

**Topics**
+ [Step 1: Associate DNS aliases with your Amazon FSx file system](#step1-assign-dns-alias)
+ [Step 2: Configure service principal names \(SPNs\) for Kerberos](#step2-configure-spn-kerberos)
+ [Step 3: Update or create a DNS CNAME record for the file system](#step4-configure-dns-cname)
+ [Enforcing Kerberos authentication using GPOs](#enforce-kerberos)

## Step 1: Associate DNS aliases with your Amazon FSx file system<a name="step1-assign-dns-alias"></a>

You can associate DNS aliases with existing FSx for Windows File Server file systems, when you create new file systems, and when you create a new file system from a backup using the Amazon FSx console, CLI, and API\. If you are creating an alias with a different domain name input the full name, including parent domain, to associate an alias\.

This procedure describes how to associate DNS aliases when creating a new file system using the Amazon FSx console\. For information about associating DNS aliases with existing file systems, and details about using the CLI and API, see [Managing DNS aliases](managing-dns-aliases.md)\.

**To associate DNS aliases when creating a new file system**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Follow the procedure for creating a new file system as described in [Step 1: Create your file system](getting-started-step1.md) of the Getting Started section\.

1. In the **Access \- optional** section of the **Create file system** wizard, enter the DNS aliases that you want to associate with your file system\.   
![\[\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/FSxW-create-fs-Access-aliases.png)

   Use the following guidelines when specifying DNS aliases:
   + Must be formatted as a fully qualified domain name \(FQDN\) `hostname.domain`, for example, `accounting.example.com`\.
   + Can contain alphanumeric characters and hyphens \(‐\)\.
   + Cannot start or end with a hyphen\.
   + Can start with a numeric\.

   For DNS alias names, Amazon FSx stores alphabetic characters as lowercase letters \(a\-z\), regardless of how you specify them: as uppercase letters, lowercase letters, or the corresponding letters in escape codes\.

1. For **Maintenance preferences**, make any changes that you want\.

1. In the **Tags \- optional** section, add any tags that you need, and then choose **Next**\.

1. Review the file system configuration shown on the **Create file system** page\. Choose **Create file system** to create the file system\. 

   When your new file system becomes available, continue with step 2\.

## Step 2: Configure service principal names \(SPNs\) for Kerberos<a name="step2-configure-spn-kerberos"></a>

We recommend that you use Kerberos\-based authentication and encryption in transit with Amazon FSx\. Kerberos provides the most secure authentication for clients that access your file system\.

To enable Kerberos authentication for clients that access Amazon FSx using a DNS alias, you must add service principal names \(SPNs\) that correspond to the DNS alias on your Amazon FSx file system’s Active Directory computer object\. An SPN can only be associated with a single Active Directory computer object at a time\. If you have existing SPNs for the DNS name configured for your original file system's Active Directory computer object, you must delete them first\. 

There are two required SPNs for Kerberos authentication:

```
HOST/alias
HOST/alias.domain
```

If the alias is `finance.domain.com`, the following are the two required SPNs:

```
HOST/finance
HOST/finance.domain.com
```

**Note**  
You will need to delete any existing HOST SPNs that correspond to the DNS alias on the Active Directory computer object before you create new HOST SPNs for your Amazon FSx file system's Active Directory \(AD\) computer object\. Attempts to set SPNs for your Amazon FSx file system will fail if an SPN for the DNS alias exists in the AD\.

The following procedures describes how to do the following:
+ Find any existing DNS alias SPNs on the original file system's Active Directory computer object\.
+ Delete the existing SPNs found, if any\.
+ Create new DNS alias SPNs for your Amazon FSx file system's Active Directory computer object\.

**To install the required PowerShell Active Directory module**

1. Log on to a Windows instance joined to the Active Directory to which your Amazon FSx file system is joined\.

1. Open PowerShell as administrator\.

1. Install the PowerShell Active Directory module using the following command\.

   ```
   Install-WindowsFeature RSAT-AD-PowerShell
   ```<a name="find-delete-existing-spns"></a>

**To find and delete existing DNS alias SPNs on the original file system's Active Directory computer object**

1. Find any existing SPNs by using the following commands\. Replace `alias_fqdn` with the DNS alias that you associated with the file system in [Step 1](#step1-assign-dns-alias)\.

   ```
   ## Find SPNs for original file system's AD computer object
   $ALIAS = "alias_fqdn"
   SetSPN /Q ("HOST/" + $ALIAS)
   SetSPN /Q ("HOST/" + $ALIAS.Split(".")[0])
   ```

1. Delete the existing HOST SPNs returned in the previous step by using the following example script\.
   + Replace `alias_fqdn` with the full DNS alias that you associated with the file system in [Step 1](#step1-assign-dns-alias)\.
   + Replace `file_system_DNS_name` with the original file system's DNS name\.

   ```
   ## Delete SPNs for original file system's AD computer object
   $Alias = "alias_fqdn"
   $FileSystemDnsName = "file_system_dns_name"
   $FileSystemHost = (Resolve-DnsName ${FileSystemDnsName} | Where Type -eq 'A')[0].Name.Split(".")[0]
   $FSxAdComputer = (Get-AdComputer -Identity ${FileSystemHost})
   
   SetSPN /D ("HOST/" + ${Alias}) ${FSxAdComputer}.Name
   SetSPN /D ("HOST/" + ${Alias}.Split(".")[0]) ${FSxAdComputer}.Name
   ```

1. Repeat the previous steps for each DNS alias that you've associated with the file system in [Step 1](#step1-assign-dns-alias)\.

**To set SPNs on your Amazon FSx file system’s Active Directory computer object**

1. Set new SPNs for your Amazon FSx file system by running the following commands\.
   + Replace `file_system_DNS_name` with the DNS name that Amazon FSx assigned to the file system\. 

     To find your file system's DNS name on the Amazon FSx console, choose **File systems**, choose your file system, and then choose the **Network & security** pane on the file system details page\. 

     You can also get the DNS name in the response of the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\.
   + Replace `alias_fqdn` with the full DNS alias that you associated with the file system in [Step 1](#step1-assign-dns-alias)\.

   ```
   ## Set SPNs for FSx file system AD computer object
   $FSxDnsName = "file_system_DNS_name"
   $Alias = "alias_fqdn"
   $FileSystemHost = (Resolve-DnsName $FSxDnsName | Where Type -eq 'A')[0].Name.Split(".")[0]
   $FSxAdComputer = (Get-AdComputer -Identity $FileSystemHost)
   
   ##Use one of the following commands, not both:
   Set-AdComputer -Identity $FSxAdComputer -Add @{"msDS-AdditionalDnsHostname"="$Alias"}
   ##Or
   SetSpn /S ("HOST/" + $Alias.Split('.')[0]) $FSxAdComputer.Name
   SetSpn /S ("HOST/" + $Alias) $FSxAdComputer.Name
   ```
**Note**  
Setting an SPN for your Amazon FSx file system will fail if an SPN for the DNS alias exists in the AD for the original file system's computer object\. For information about finding and deleting existing SPNs, see [To find and delete existing DNS alias SPNs on the original file system's Active Directory computer object](#find-delete-existing-spns)\.

1. Verify that the new SPNs are configured for the DNS alias using the following example script\. Ensure that the response includes two HOST SPNs, `HOST/alias` and `HOST/alias_fqdn`, as described previously in this procedure\.

   Replace `file_system_DNS_name` with the DNS name that Amazon FSx assigned to your file system\. To find your file system's DNS name on the Amazon FSx console, choose **File systems**, choose your file system, and then choose the **Network & security** pane on the file system details page\. 

   You can also get the DNS name in the response of the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\.

   ```
   ## Verify SPNs on FSx file system AD computer object
   $FileSystemDnsName = "file_system_dns_name"
   $FileSystemHost = (Resolve-DnsName ${FileSystemDnsName} | Where Type -eq 'A')[0].Name.Split(".")[0]
   $FSxAdComputer = (Get-AdComputer -Identity ${FileSystemHost})
   SetSpn /L ${FSxAdComputer}.Name
   ```

1. Repeat the previous steps for each DNS alias that you've associated with the file system in [Step 1](#step1-assign-dns-alias)\.

For information about how to enforce clients to use Kerberos authentication and encryption when connecting to your Amazon FSx file system, see [Enforcing Kerberos authentication using GPOs](#enforce-kerberos)\.

## Step 3: Update or create a DNS CNAME record for the file system<a name="step4-configure-dns-cname"></a>

After you properly configure SPNs for your file system, you can cut over to Amazon FSx by replacing each DNS record that resolved to the original file system with a DNS record that resolves to the default DNS name of the Amazon FSx file system\.

The `dnsserver` and `activedirectory` Windows modules are required to run the commands presented in this section\.

**To install the required PowerShell cmdlets**

1. Log on to a Windows instance joined to the Active Directory that your Amazon FSx file system is joined to as a user that is a member of a group that has DNS administration permissions \(**AWSAWS Delegated Domain Name System Administrators** in AWS Managed Active Directory, and **Domain Admins** or another group to which you've delegated DNS administration permissions in your self\-managed Active Directory\)\. 

   For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open PowerShell as administrator\.

1. The PowerShell DNS Server module is required to perform the instructions in this procedure\. Install it using the following command\.

   ```
   Install-WindowsFeature RSAT-DNS-Server
   ```

**To update or create a custom DNS name to your Amazon FSx file system**

1. Connect to your Amazon EC2 instance as a user that is a member of a group that has DNS administration permissions \(**AWS Delegated Domain Name System Administrators** in AWS Managed Active Directory, and **Domain Admins** or another group to which you've delegated DNS administration permissions in your self\-managed Active Directory\)\.

   For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. At the command prompt, run the following script\. This script migrates any existing DNS CNAME records to your Amazon FSx file system\. If none are found, it creates a new DNS CNAME record for the DNS alias `alias_fqdn` that resolves to the default DNS name for your Amazon FSx file system\. 

   To run the script:
   + Replace `alias_fqdn` with the DNS alias that you associated with the file system\.
   + Replace `file_system_DNS_name` with the DNS name Amazon FSx has assigned to the file system\.

   ```
   $Alias="alias_fqdn"
   $FSxDnsName="file_system_dns_name"
   $AliasHost=$Alias.Split('.')[0]
   $ZoneName=((Get-WmiObject Win32_ComputerSystem).Domain)
   $DnsServerComputerName = (Resolve-DnsName $ZoneName -Type NS | Where Type -eq 'A' | Select -ExpandProperty Name)
   
   Add-DnsServerResourceRecordCName -Name $AliasHost -ComputerName $DnsServerComputerName -HostNameAlias $FSxDnsName -ZoneName $ZoneName
   ```

1. Repeat the previous step for each DNS alias that you associated with the file system in [Step 1](#step1-assign-dns-alias)\.

You've now added a DNS CNAME value for your Amazon FSx file system with the DNS alias\. You can now use the DNS alias to access your data\.

**Note**  
When updating a DNS CNAME record to point to an Amazon FSx file system previously pointed to another file system, clients might not be able to connect with file system for a brief period of time\. When the client DNS cache refreshes, they should be able to connect using the DNS alias\. For more information, see [Can't access the file system using a DNS alias](unable-to-access.md#cant-connect-using-dns-alias)\.

## Enforcing Kerberos authentication using GPOs<a name="enforce-kerberos"></a>

You can enforce Kerberos authentication when accessing the file system by setting the following Group Policy Objects \(GPOs\) in your Active Directory:
+ **Restrict NTLM: Outgoing NTLM traffic to remote servers** \- Use this policy setting to deny or audit outgoing NTLM traffic from a computer to any remote server running the Windows operating system\.
+ **Restrict NTLM: Add remote server exceptions for NTLM authentication** \- Use this policy setting to create an exception list of remote servers to which client devices are allowed to use NTLM authentication if the *Network security: Restrict NTLM: Outgoing NTLM traffic to remote servers* policy setting is configured\.

1. Log on to a Windows instance joined to the Active Directory to which your Amazon FSx file system is joined as an administrator\. If you are configuring a self\-managed Active Directory, apply these steps directly to your Active Directory\.

1. Choose **Start**, choose **Administrative Tools**, and then choose **Group Policy Management**\.

1. Choose **Group Policy Objects**\.

1. If your Group Policy Object does not already exist, create it\.

1. Locate the existing **Network Security: Restrict NTLM: Outgoing NTLM traffic to remote servers** policy\. \(If there is no existing policy, create a new policy\.\) In the **Local security setting** tab, open the context \(right\-click\) menu, and choose **Properties**\.

1. Choose **Deny all**\.

1. Choose **Apply** to save the security setting\.

1. To set exceptions for NTLM connections to specific remote servers for the client, locate the **Network security: Restrict NTLM: Add remote server exceptions**\.

   Open the context \(right\-click\) menu, and choose **Properties** in the **Local security setting** tab\.

1. Enter the names of any servers to add to the exception list\.

1. Choose **Apply** to save the security setting\.
