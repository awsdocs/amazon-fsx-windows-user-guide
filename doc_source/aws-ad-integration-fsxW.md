# Working with Microsoft Active Directory in FSx for Windows File Server<a name="aws-ad-integration-fsxW"></a>

Amazon FSx works with Microsoft Active Directory \(AD\) to integrate with your existing Microsoft Windows environments\. Active Directory is the Microsoft directory service used to store information about objects on the network and make this information easy for administrators and users to find and use\. These objects typically include shared resources such as file servers and network user and computer accounts\. 

When you create a file system with Amazon FSx, you join it to your Active Directory domain to provide user authentication and file\- and folder\-level access control\. Your users can then use their existing user identities in Active Directory to authenticate themselves and access the Amazon FSx file system\. Users can also use their existing identities to control access to individual files and folders\. In addition, you can migrate your existing files and folders and these items' security access control list \(ACL\) configuration to Amazon FSx without any modifications\. 

Amazon FSx provides you with two options for using your FSx for Windows File Server file system with Active Directory: [Using Amazon FSx with AWS Directory Service for Microsoft Active Directory](fsx-aws-managed-ad.md) and [Using Amazon FSx with your self\-managed Microsoft Active Directory](self-managed-AD.md)\. 

**Note**  
Amazon FSx supports [Microsoft Azure Active Directory Domain Services](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/overview), which you can join to a [Microsoft Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis)\.

After you create a joined Active Directory configuration for a file system, you can update only the following properties:
+ Service user credentials
+ DNS server IP addresses

You *cannot* change the following properties for your joined Microsoft AD:
+ DomainName
+ OrganizationalUnitDistinguishedName
+ FileSystemAdministratorsGroup

However, you can create a new file system from a backup and change these properties in the Microsoft Active Directory integration configuration for that file system\. For more information, see [Walkthrough 2: Create a file system from a backup](walkthrough02-create-from-backup.md)\. 

**Note**  
Amazon FSx does not support [Active Directory Connector](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_ad_connector.html) and [Simple Active Directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_simple_ad.html)\.

**Topics**
+ [Using Amazon FSx with AWS Directory Service for Microsoft Active Directory](fsx-aws-managed-ad.md)
+ [Using Amazon FSx with your self\-managed Microsoft Active Directory](self-managed-AD.md)