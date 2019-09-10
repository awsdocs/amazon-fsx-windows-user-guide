# Using Amazon FSx with Your Self\-Managed Microsoft Active Directory<a name="self-managed-AD"></a>

Your organization might manage identities and devices on a self\-managed Active Directory \(on\-premises or in the cloud\)\. If so, you can join your Amazon FSx file system directly to your existing self\-managed AD domain\. To use Amazon FSx with your AWS Managed Microsoft AD setup, you can use the Amazon FSx console\. When you create a new Amazon FSx for Windows File Server file system in the console, choose **Self\-managed Microsoft Active Directory** under the **Windows Authentication** section\. Provide the following details for your self\-managed AD: 
+  A fully qualified domain name of your self\-managed directory 
**Note**  
Domain name must not be in the Single Label Domain \(SLD\) format\. Amazon FSx currently does not support SLD domains\.
+  IP addresses of the DNS servers for your domain 
+  User name and password for a service account on your AD domain, for Amazon FSx to use to join the file system to your AD domain 
+  \(Optional\) The Organizational Unit \(OU\) in your domain in which you want your file system to be joined
+ \(Optional\) The domain group to which you want to delegate authority to perform administrative actions on your file system\. For example, this domain group might manage Windows file shares, manage ACLs on the file system's root folder, take ownership of files and folders, and so on\. If you don’t specify this group, Amazon FSx delegates this authority to the Domain Admins group in your AD domain by default\. 

  For more information, see [Joining an Amazon FSx File System to a Self\-Managed Microsoft Active Directory Domain](creating-joined-ad-file-systems.md)\.

**Note**  
If you’d like to beneﬁt from a resource forest isolation model, where you isolate your resources, including your Amazon FSx ﬁle systems, into a separate AD forest from the one where your users reside, you can alternately choose to join your ﬁle system to an AWS Managed AD and establish a one\-way forest trust relationship between an AWS Managed AD you create and your existing self\-managed AD\. 

## Prerequisites<a name="self-manage-prereqs"></a>

Before you create an Amazon FSx file system joined to your Microsoft AD domain, make sure that you have the following:
+ An on\-premises or other self\-managed Microsoft AD that the Amazon FSx file system is to join, with the following:
  +  The domain functional level of your AD domain controller is at Windows Server 2008 R2 or higher
  +  DNS server IP addresses that are either in the same VPC CIDR range as the one in which your Amazon FSx file system is being created or in the private IP address range, as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html): 
    + 10\.0\.0\.0–10\.255\.255\.255 \(10/8 prefix\)
    + 172\.16\.0\.0–172\.31\.255\.255 \(172\.16/12 prefix\)
    + 192\.168\.0\.0–192\.168\.255\.255 \(192\.168/16 prefix\)
  +  Domain name that is not in the Single Label Domain \(SLD format\)\. Amazon FSx currently does not support SLD domains\. 
+  Connectivity configured between the VPC based on Amazon VPC where you want to create the file system and your self\-managed Active Directory\. You can set up connectivity using AWS Direct Connect, AWS VPN, VPC peering, or Transit Gateway\.
+  VPC Security Groups that you've associated with your Amazon FSx file system, along with any VPC Network ACLs, configured to allow outbound network traffic on all ports\. 
**Note**  
If you want to implement least privilege, you can allow outbound traffic only to the specific ports required for communication with the Active Directory domain controllers\. For more information, see [ Microsoft's Active Directory documentation](https://support.microsoft.com/en-us/help/179442/how-to-configure-a-firewall-for-domains-and-trusts)\.
+  Windows Firewall on your Active Directory domain controllers configured to allow inbound traffic on all ports from the subnet where you'd like to have your Amazon FSx file system\. 
**Note**  
If you want to implement least privilege, you can allow inbound traffic only on the specific ports required for communication between member servers and the Active Directory domain controllers\. For more information, see [ Microsoft's Active Directory documentation](https://support.microsoft.com/en-us/help/179442/how-to-configure-a-firewall-for-domains-and-trusts)\. 
+  A service account in your self\-managed Microsoft AD with delegated permissions to join computers to the domain\. A *service account *is a user account in your self\-managed Microsoft AD that has been delegated certain tasks\. These tasks are creating and deleting computer objects in the OU in your domain where you're joining the file system\. The service account needs to have, at a minimum, the following permissions:
  + Ability to reset passwords 
  +  Ability to restrict accounts from reading and writing data
  +  Validated ability to write to the DNS host name 
  +  Validated ability to write to the service principal name 

 If this is your first time using AWS and Amazon FSx for Windows File Server, make sure to set up before starting\. For more information, see [Setting Up](setting-up.md)\. 