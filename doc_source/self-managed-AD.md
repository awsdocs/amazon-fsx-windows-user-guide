# Using Amazon FSx with your self\-managed Microsoft Active Directory<a name="self-managed-AD"></a>

Your organization might manage identities and devices on a self\-managed Active Directory \(on\-premises or in the cloud\)\. If so, you can join your Amazon FSx file system directly to your existing self\-managed AD domain\. To use Amazon FSx with your AWS Managed Microsoft AD setup, you can use the Amazon FSx console\. When you create a new FSx for Windows File Server file system in the console, choose **Self\-managed Microsoft Active Directory** under the **Windows Authentication** section\. Provide the following details for your self\-managed AD: 
+  A fully qualified domain name of your self\-managed directory 
**Note**  
Domain name must not be in the Single Label Domain \(SLD\) format\. Amazon FSx currently does not support SLD domains\.
**Note**  
For Single\-AZ 2 and all Multi\-AZ file systems, the Active Directory domain name cannot exceed 47 characters\.
+  IP addresses of the DNS servers for your domain 

  The DNS server IP addresses, AD domain controller IP addresses, and client network must meet the following requirements:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/self-managed-AD.html)
**Note**  
Your AD domain controllers must be writable\. 
+  User name and password for a service account on your AD domain, for Amazon FSx to use to join the file system to your AD domain 
+  \(Optional\) The Organizational Unit \(OU\) in your domain in which you want your file system to be joined
+ \(Optional\) The domain group to which you want to delegate authority to perform administrative actions on your file system\. For example, this domain group might manage Windows file shares, manage ACLs on the file system's root folder, take ownership of files and folders, and so on\. If you don’t specify this group, Amazon FSx delegates this authority to the Domain Admins group in your AD domain by default\. 

  For more information, see [Joining an Amazon FSx file system to a self\-managed Microsoft Active Directory domain](creating-joined-ad-file-systems.md)\.

**Important**  
Amazon FSx only registers DNS records for a file system if you are using Microsoft DNS as the default DNS service\. If you are using a third\-party DNS, you will need to manually setup DNS entries for your Amazon FSx file systems after you create them\.

 When you join your file system directly to your self\-managed AD, your FSx for Windows File Server resides in the same AD forest \(the top\-most logical container in an AD configuration that contains domains, users, and computers\) and in the same AD domain as your users and existing resources \(including existing file servers\)\. 

**Note**  
If you’d like to beneﬁt from a resource forest isolation model, where you isolate your resources, including your Amazon FSx ﬁle systems, into a separate AD forest from the one where your users reside, you can alternately choose to join your ﬁle system to an AWS Managed AD and establish a one\-way forest trust relationship between an AWS Managed AD that you create and your existing self\-managed AD\. 

**Topics**
+ [Prerequisites for using a self\-managed Microsoft AD](self-manage-prereqs.md)
+ [Best practices for joining FSx for Windows File Server file systems to a self\-managed Microsoft Active Directory domain](self-managed-AD-best-practices.md)
+ [Validating your Active Directory configuration](validate-ad-config.md)
+ [Joining an Amazon FSx file system to a self\-managed Microsoft Active Directory domain](creating-joined-ad-file-systems.md)
+ [Obtaining the correct file system IP addresses to use for DNS](file-system-ip-addresses-for-dns.md)
+ [Updating the self\-managed Active Directory configuration](update-self-ad-config.md)