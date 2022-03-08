# Troubleshooting file systems joined to a self\-managed Active Directory<a name="unable-to-create-fs-self-ad"></a>

**Topics**
+ [Amazon FSx can't reach self\-managed AD DNS server or domain controllers\. File system creation failed\.](#w213aac41c11b9b5)
+ [Can't connect to Microsoft AD domain controllers due to invalid service account credentials](#w213aac41c11b9b7)
+ [Amazon FSx can't connect to Microsoft AD domain controllers due to insufficient service account permissions](#w213aac41c11b9b9)
+ [Amazon FSx can't connect to the Microsoft AD domain controllers because the service account provided can't join any more computers to the domain](#w213aac41c11b9c11)
+ [Amazon FSx can't connect to the Microsoft AD domain controllers because the organizational unit specified doesn't exist or isn't accessible](#w213aac41c11b9c13)
+ [Amazon FSx can't apply the Microsoft AD configuration because the file system administrators group doesn't exist or isn't accessible to the service account](#w213aac41c11b9c15)
+ [Amazon FSx can't apply your Microsoft Active Directory configuration\.](#w213aac41c11b9c17)
+ [File system creation failed\. The service account provided does not have permission to join the file system to the domain with the specified organizational unit \(OU\)](#w213aac41c11b9c19)
+ [Amazon FSx is unable to create a file system within the specified Microsoft Active Directory\.](#w213aac41c11b9c21)

## Amazon FSx can't reach self\-managed AD DNS server or domain controllers\. File system creation failed\.<a name="w213aac41c11b9b5"></a>

 Creating a file system joined to your self\-managed Active Directory fails with the following error message:

```
Amazon FSx can't reach the DNS servers provided or the domain controllers for
your self-managed directory in Microsoft Active Directory. File system creation failed. Amazon FSx is
unable to communicate with your Microsoft Active Directory domain controllers.
This is because Amazon FSx can't reach the DNS servers provided or domain controllers
for your domain. To fix this problem, delete your file system and create a new
one with valid DNS servers and networking configuration that allows traffic from
the file system to the domain controller.
```

Use the following steps to troubleshoot and resolve the issue\.

1. Verify that you followed the prerequisites for having network connectivity and routing established between the subnet where you're creating an Amazon FSx file system, and your self\-managed Active Directory\. For more information, see [Prerequisites for using a self\-managed Microsoft AD](self-manage-prereqs.md)\.

   Use the [Amazon FSx Active Directory Validation tool](validate-ad-config.md) to test and verify these network settings\.
**Note**  
If you have multiple Active Directory sites defined, ensure that the subnets in the VPC associated with your Amazon FSx file system are defined in an Active Directory site and that no IP conflicts exist between the subnets in your VPC and the subnets in your other sites\. You can view and change these settings using the Active Directory Sites and Services MMC snap\-in\.

1. Verify that you configured the VPC security groups that you associated with your Amazon FSx file system, along with any VPC network ACLs, to allow outbound network traffic on all ports\.
**Note**  
If you want to implement least privilege, you can allow outbound traffic only to the specific ports required for communication with the Active Directory domain controllers\. For more information, see the [Microsoft Active Directory documentation](https://support.microsoft.com/en-us/help/179442/how-to-configure-a-firewall-for-domains-and-trusts)\.

1. Verify that the values for Microsoft Windows file server or network administrative properties do not contain non\-Latin\-1 characters\. For example, the file system creation fails if you use `Domänen-Admins` as the name of the file system administrators group\.

1.  Verify that your Active Directory domain's DNS servers and domain controllers are active and able to respond to requests for the domain provided\.

1.  Ensure that the functional level of your Active Directory domain is Windows Server 2008 R2 or higher\.

1.  Make sure that the firewall rules on your Active Directory domain's domain controllers allow traffic from your Amazon FSx file system\. For more information, see the [Microsoft Active Directory documentation](https://support.microsoft.com/en-us/help/179442/how-to-configure-a-firewall-for-domains-and-trusts)\. 

## Can't connect to Microsoft AD domain controllers due to invalid service account credentials<a name="w213aac41c11b9b7"></a>

 Creating a file system joined to your self\-managed Active Directory fails with the following error message:

```
Amazon FSx is unable to establish a connection with your Microsoft 
Active Directory domain controllers because the service account credentials provided are 
invalid. To fix this problem, delete your file system and create a new one using a valid service 
account.
```

Use the following steps to troubleshoot and resolve the issue\.

1. Verify that you're entering only the user name as input for the **Service account username**, such as `ServiceAcct`, in the self\-managed Active Directory configuration\. 
**Important**  
DO NOT include a domain prefix \(`corp.com\ServiceAcct`\) or domain suffix \(`ServiceAcct@corp.com`\) when entering the service account user name\.  
DO NOT use the distinguished name \(DN\) when entering the service account user name \(CN=ServiceAcct,OU=example,DC=corp,DC=com\)\.

1. Verify that the service account that you provided exists in your Active Directory domain\. 

1. Make sure that you delegated the required permissions to the service account that you provided\. The service account must be able to create and delete computer objects in the OU in the domain to which you're joining the file system\. The service account also needs, at a minimum, to have permissions to do the following:
   +  Reset passwords 
   +  Restrict accounts from reading and writing data 
   +  Validated ability to write to the DNS hostname 
   +  Validated ability to write to the service principal name 

    For more information about creating a service account with correct permissions, see [ Delegating privileges to your Amazon FSx service account ](self-managed-AD-best-practices.md#connect_delegate_privileges)\. 

## Amazon FSx can't connect to Microsoft AD domain controllers due to insufficient service account permissions<a name="w213aac41c11b9b9"></a>

 Creating a file system joined to your self\-managed Active Directory fails with the following error message:

```
Amazon FSx is unable to establish a connection with your
Microsoft Active Directory domain controllers. This is because the service account provided does not 
have permission to join the file system to the domain with the specified organizational unit. 
To fix this problem, delete your file system and create a new one using a service account with 
permission to join the file system to the domain with the specified organizational unit.
```

Use the following procedure to troubleshoot and resolve the issue\.
+  Make sure that you delegated the required permissions to the service account that you provided\. The service account must be able to create and delete computer objects in the OU in the domain to which you're joining the file system\. The service account also needs, at a minimum, to have permissions to do the following: 
  +  Reset passwords 
  +  Restrict accounts from reading and writing data 
  +  Validated ability to write to the DNS hostname 
  +  Validated ability to write to the service principal name 

   For more information about creating a service account with correct permissions, see [ Delegating privileges to your Amazon FSx service account ](self-managed-AD-best-practices.md#connect_delegate_privileges)\. 

## Amazon FSx can't connect to the Microsoft AD domain controllers because the service account provided can't join any more computers to the domain<a name="w213aac41c11b9c11"></a>

 Creating a file system joined to your self\-managed Active Directory fails with the following error message:

```
Amazon FSx can't establish a connection with your Microsoft Active Directory
domain controllers. This is because the service account provided has reached the
maximum number of computers that it can join to the domain. To fix this problem,
delete your file system and create a new one, supplying a service account that
is able to join new computers to the domain.
```

To resolve the issue, verify that the service account you provided has reached the maximum number of computers it can join to the domain\. If it has reached the maximum limit, create a new service account with the correct permissions\. Use the new service account and create a new file system\. For more information, see [ Delegating privileges to your Amazon FSx service account ](self-managed-AD-best-practices.md#connect_delegate_privileges)\.

## Amazon FSx can't connect to the Microsoft AD domain controllers because the organizational unit specified doesn't exist or isn't accessible<a name="w213aac41c11b9c13"></a>

Creating a file system joined to your self\-managed Active Directory fails with the following error message:

```
Amazon FSx can't establish a connection with your Microsoft Active Directory domain controller(s). 
This is because the organizational unit you specified either doesn't exist or isn't accessible 
to the service account provided. To fix this problem, delete your file system and create a new one specifying an 
organizational unit to which the service account can join the file system.
```

 Use the following steps to troubleshoot and resolve the issue\. 

1.  Verify that the OU you provided is in your Active Directory domain\. 

1. Make sure that you have delegated the required permissions to the service account that you provided\. The service account must be able to create and delete computer objects in the OU in the domain that you're joining the file system to\. The service account also needs to have, at a minimum, permissions to do the following: 
   +  Reset passwords 
   +  Restrict accounts from reading and writing data 
   +  Validated ability to write to the DNS hostname 
   +  Validated ability to write to the service principal name 
   + Be delegated control to create and delete computer objects
   + Validated ability to read and write Account Restrictions

    For more information about creating a service account with the correct permissions, see [ Delegating privileges to your Amazon FSx service account ](self-managed-AD-best-practices.md#connect_delegate_privileges)\. 

## Amazon FSx can't apply the Microsoft AD configuration because the file system administrators group doesn't exist or isn't accessible to the service account<a name="w213aac41c11b9c15"></a>

Creating a file system joined to your self\-managed Active Directory fails with the following error message:

```
Amazon FSx is unable to apply your Microsoft Active Directory configuration. This is because the file system 
administrators group you provided either doesn't exist or isn't accessible to the service account you 
provided. To fix this problem, delete your file system and create a new one specifying a file 
system administrators group in the domain that is accessible to the service account 
provided.
```

Use the following steps to troubleshoot and resolve the issue\.

1.  Ensure that you’re providing just the name of the group as a string for the administrators group parameter\. 
**Important**  
 DO NOT include a domain prefix \(`corp.com\FSxAdmins`\) or domain suffix \(`FSxAdmins@corp.com`\) when providing the group name parameter\.   
 DO NOT use the distinguished name \(DN\) for the group\. An example of a distinguished name is CN=FSxAdmins,OU=example,DC=corp,DC=com\. 

1. Ensure that the administrators group provided exists in the same Active Directory domain as the one that you want to join the file system to\.

1.  If you did not provide an administrator group parameter, Amazon FSx attempts to use the `Builtin Domain Admins` group in your Active Directory domain\. If the name of this group has been changed, or if you’re using a different group for domain administration, you need to provide that name for the group\. 

## Amazon FSx can't apply your Microsoft Active Directory configuration\.<a name="w213aac41c11b9c17"></a>

Creating a file system joined to your self\-managed Active Directory fails with the following error message:

```
Amazon FSx is unable to apply your Microsoft Active 
Directory configuration. To fix this problem, delete your file system and create a new one 
meeting the pre-requisites described in the Amazon FSx user guide.
```

 When creating your file system, Amazon FSx was able to reach your Active Directory domain’s DNS servers and domain controllers, and join the file system successfully to your Active Directory domain\. However, while completing file system creation, Amazon FSx lost connectivity to or membership in your domain\. Use the following steps to troubleshoot and resolve the issue\.

1.  Ensure that network connectivity continues to exist between your Amazon FSx file system and your Active Directory\. And, ensure that network traffic continues to be allowed between them by using routing rules, VPC security group rules, VPC network ACLs, and domain controller firewall rules\. 

1.  Ensure that the computer objects created by Amazon FSx for your file systems in your Active Directory domain are still active, and were not deleted or otherwise manipulated\. 

## File system creation failed\. The service account provided does not have permission to join the file system to the domain with the specified organizational unit \(OU\)<a name="w213aac41c11b9c19"></a>

Creating a file system joined to your self\-managed Active Directory fails with the following error message:

```
File system creation failed. Amazon FSx is unable to establish a connection with your Microsoft Active Directory domain controller(s). 
This is because the service account provided does not have permission to join the file system to the domain with the specified 
organizational unit (OU). To fix this problem, delete your file system and create a new one using a service account with permission 
to create computer objects and reset passwords within the specified organizational unit.
```

Make sure that you have delegated the required permissions to the service account that you provided\. Use the following steps to troubleshoot and resolve the issue\.

 The service account needs to have, at a minimum, the following permissions: 
+ Be delegated control to create and delete computer objects in the OU that you’re joining the file system to
+ Have the following permissions in the OU that you’re joining the file system to:
  + Ability to reset passwords
  + Ability to restrict accounts from reading and writing data
  + Validated ability to write to the DNS hostname 
  + Validated ability to write to the service principal name 

  For more information about creating a service account with the correct permissions, see [ Delegating privileges to your Amazon FSx service account ](self-managed-AD-best-practices.md#connect_delegate_privileges)\.

## Amazon FSx is unable to create a file system within the specified Microsoft Active Directory\.<a name="w213aac41c11b9c21"></a>

 Creating a file system joined to your self\-managed Active Directory fails with the following error message:

```
File system creation failed. Amazon FSx is unable to create a file system within the specified
Microsoft Active Directory. To fix this problem, please delete your file system and create a new one
meeting the pre-requisites described in the Amazon FSx user guide.
```

Amazon FSx does not support Unicode characters\. Verify that none of the creation parameters have Unicode characters, such as accent marks\. This includes parameters that can be left blank where a default value is filled in automatically\. Ensure the corresponding default values in your Active Directory also do not contain Unicode characters\.

If you encounter problems not listed here while using Amazon FSx, ask a question in the [Amazon FSx Forum](https://forums.aws.amazon.com/forum.jspa?forumID=308) or contact [Amazon Web Services Support](https://aws.amazon.com/premiumsupport/)\.