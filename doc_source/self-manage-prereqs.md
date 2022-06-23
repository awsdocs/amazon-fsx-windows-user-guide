# Prerequisites for using a self\-managed Microsoft AD<a name="self-manage-prereqs"></a>

Before you create an Amazon FSx file system joined to your self\-managed Microsoft AD domain, make sure that you have created and set up the following requirements:
+ An on\-premises or other self\-managed Microsoft AD that the Amazon FSx file system is to join, with the following configuration:
  + The domain functional level of your AD domain controller is at Windows Server 2008 R2 or higher\.
  + DNS server IP addresses and AD domain controller IP addresses as follows, depending on when your file system was created:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/self-manage-prereqs.html)

    If you need to access your FSx for Windows File Server file system that was created before December 17, 2020 using a non\-private IP address range, you can create a new file system by restoring a backup of the file system\. For more information, see [Working with backups](using-backups.md)\.
  +  Domain name that is not in the Single Label Domain \(SLD\) format\. Amazon FSx does not support SLD domains\. 
  + For Single\-AZ 2 and all Multi\-AZ file systems, the Active Directory domain name cannot exceed 47 characters\.
  + If you have Active Directory sites defined, you must make sure that the subnets in the VPC associated with your Amazon FSx file system are defined in an Active Directory site, and that no conflicts exist between the subnets in your VPC and the subnets in your other sites\.
+ The following network configurations:
  + Connectivity configured between the Amazon VPC where you want to create the file system and your self\-managed Active Directory\. You can set up connectivity using AWS Direct Connect, AWS VPN, VPC peering, or AWS Transit Gateway\.
  + For **VPC security groups**, the default security group for your default Amazon VPC is already added to your file system in the console\. Please ensure that the security group and the VPC Network ACLs for the subnet\(s\) where you're creating your FSx file system allow traffic on the ports and in the directions shown in the following diagram\.  
![\[FSx for Windows File Server port configuration requirements for VPC security groups and network ACLs for the subnets where the file system is being created.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/Windows-port-requirements.png)

    The following table identifies the role of each port\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/self-manage-prereqs.html)
**Important**  
Allowing outbound traffic on TCP port 9389 is required for Single\-AZ 2 and all Multi\-AZ file system deployments\.
**Note**  
If you're using VPC network ACLs, you must also allow outbound traffic on dynamic ports \(49152\-65535\) from your FSx file system\.
  + Ensure that these traffic rules are also mirrored on the firewalls that apply to each of the AD domain controllers, DNS servers, FSx clients and FSx administrators\.
**Important**  
While Amazon VPC security groups require ports to be opened only in the direction that network traffic is initiated, most Windows firewalls and VPC network ACLs require ports to be open in both directions\.

  Use the [Amazon FSx Active Directory Validation tool](validate-ad-config.md#test-ad-network-config) to test these network settings before attempting to join your file system to your self\-managed AD\.
+  A service account in your self\-managed Microsoft AD with delegated permissions to join computers to the domain\. A *service account *is a user account in your self\-managed Microsoft AD that has been delegated certain tasks\. 

   The service account also needs to, at a minimum, be delegated the following permissions in the OU that you're joining the file system to: 
  + Ability to reset passwords
  + Ability to restrict accounts from reading and writing data
  + Validated ability to write to the DNS host name 
  + Validated ability to write to the service principal name 
  + Be delegated control to create and delete computer objects
  + Validated ability to read and write Account Restrictions

  These represent the minimum set of permissions that are required to join computer objects to your Active Directory\. For more information, see the Microsoft Windows Server documentation topic [ Error: Access is denied when non\-administrator users who have been delegated control try to join computers to a domain controller](https://support.microsoft.com/en-us/help/932455/error-message-when-non-administrator-users-who-have-been-delegated-con)\.

To learn more about creating a service account with the correct permissions, see [ Delegating privileges to your Amazon FSx service account ](self-managed-AD-best-practices.md#connect_delegate_privileges)\.

**Note**  
Amazon FSx requires a valid service account throughout the lifetime of your Amazon FSx file system\. Amazon FSx must be able to fully manage the file system and perform tasks that require unjoining and rejoining your AD domain using, such as replacing a failed file server or patching Windows Server software\. Please keep your Active Directory configuration, including the service account credentials, updated with Amazon FSx\. To learn how, see [Keeping your Active Directory configuration updated with Amazon FSx](self-managed-AD-best-practices.md#keep-ad-config-updated)\.

**Note**  
Amazon FSx requires connectivity to all domain controllers in your AD environment\. If you have multiple domain controllers, ensure that all of them meet the requirements above, and ensure that any changes to your service account are propagated to all domain controllers\. You can validate your AD configuration \(including testing connectivity of multiple domain controllers\) using the [Amazon FSx Active Directory Validation tool](validate-ad-config.md)\.

 If this is your first time using AWS and FSx for Windows File Server, make sure to set up before starting\. For more information, see [Setting up](setting-up.md)\. 

**Important**  
Do not move computer objects that Amazon FSx creates in the OU after your file system is created\. Doing so will cause your file system to become misconfigured\.