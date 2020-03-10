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

 When you join your file system directly to your self\-managed AD, your Amazon FSx for Windows File Server resides in the same AD forest \(the top\-most logical container in an AD configuration that contains domains, users, and computers\) and in the same AD domain as your users and existing resources \(including existing file servers\)\. 

**Note**  
If you’d like to beneﬁt from a resource forest isolation model, where you isolate your resources, including your Amazon FSx ﬁle systems, into a separate AD forest from the one where your users reside, you can alternately choose to join your ﬁle system to an AWS Managed AD and establish a one\-way forest trust relationship between an AWS Managed AD you create and your existing self\-managed AD\. 

## Prerequisites<a name="self-manage-prereqs"></a>

Before you create an Amazon FSx file system joined to your Microsoft AD domain, make sure that you have the following:
+ An on\-premises or other self\-managed Microsoft AD that the Amazon FSx file system is to join, with the following:
  +  The domain functional level of your AD domain controller is at Windows Server 2008 R2 or higher
  +  DNS server IP addresses and AD domain controller IP addresses that are either in the same VPC CIDR range as the one in which your Amazon FSx file system is being created or in the private IP address range, as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html): 
    + 10\.0\.0\.0–10\.255\.255\.255 \(10/8 prefix\)
    + 172\.16\.0\.0–172\.31\.255\.255 \(172\.16/12 prefix\)
    + 192\.168\.0\.0–192\.168\.255\.255 \(192\.168/16 prefix\)
  +  Domain name that is not in the Single Label Domain \(SLD format\)\. Amazon FSx currently does not support SLD domains\. 
+  Connectivity configured between the VPC based on Amazon VPC where you want to create the file system and your self\-managed Active Directory\. You can set up connectivity using AWS Direct Connect, AWS VPN, VPC peering, or Transit Gateway\.
+  VPC Security Groups that you've associated with your Amazon FSx file system, along with any VPC Network ACLs, configured to allow outbound network traffic on the following ports:     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/self-managed-AD.html)
+  Windows Firewall on your Active Directory domain controllers configured to allow inbound traffic on the above mentioned ports from the subnet\(s\) where you'd like to have your Amazon FSx file system\. 

   In the case where the domain controllers are in AWS, the VPC Security Groups that you've associated with them, along with any VPC Network ACLs, configured to allow inbound traffic on the above mentioned ports\.
+  A service account in your self\-managed Microsoft AD with delegated permissions to join computers to the domain\. A *service account *is a user account in your self\-managed Microsoft AD that has been delegated certain tasks\. 

   The service account also needs to, at a minimum, be delegated the following permissions in the OU that you're joining the file system to: 
  + Ability to reset passwords
  + Ability to restrict accounts from reading and writing data
  + Validated ability to write to the DNS host name 
  + Validated ability to write to the service principal name 
  + Be delegated control to create and delete computer objects

To learn more about creating a service account with the correct permissions, see [ Delegating Privileges to Your Amazon FSx Service Account ](self-managed-AD-best-practices.md#connect_delegate_privileges)\.

**Note**  
Amazon FSx requires a valid service account throughout the lifetime of your Amazon FSx file system\. Amazon FSx must be able to fully manage the file system and perform tasks that require unjoining and rejoining your AD domain using, such as replacing a failed file server or patching Windows Server software\. Please keep your Active Directory configuration, including the service account credentials, updated with Amazon FSx\. To learn how, see [Keeping Your Active Directory Configuration Updated with Amazon FSx](self-managed-AD-best-practices.md#keep-ad-config-updated)\.

 If this is your first time using AWS and Amazon FSx for Windows File Server, make sure to set up before starting\. For more information, see [Setting Up](setting-up.md)\. 