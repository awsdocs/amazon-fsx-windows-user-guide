# File system is in a misconfigured state<a name="misconfigured-ad-config"></a>

An FSx for Windows File Server file system can get into a **Misconfigured** state due to a change in your Active Directory environment\. In this state, your file system is at risk of losing availability and backups may not succeed\.

The **Misconfigured** state includes an error message and recommended corrective action that you can access using the Amazon FSx console, API, or AWS CLI\. After taking the corrective action, verify that your file system's state eventually changes to `Available` – note that this change can take several minutes to complete\.

Your file system can get into a **Misconfigured** state for several reasons, such as the following:
+ The DNS Server IP addresses are no longer valid\.
+ The service account credentials are no longer valid, or lack required permissions\.
+ The Active Directory domain controller is not reachable due to network connectivity issues, such as invalid VPC Security Groups, VPC Network ACL or routing table configuration, or domain controller firewall settings\.

\(For the full list of Active Directory requirements, see [Prerequisites for using a self\-managed Microsoft AD](self-manage-prereqs.md)\. You can also validate that your Active Directory environment is properly configured to meet these requirements by using the [Amazon FSx Active Directory Validation tool](validate-ad-config.md#test-ad-network-config)\.\)

Resolving some of these issues requires directly updating one or more parameters in your file system’s [Active Directory configuration](https://docs.aws.amazon.com/fsx/latest/APIReference/API_SelfManagedActiveDirectoryConfigurationUpdates.html), such as changing DNS Server IP addresses, or changing the service account username or password\. In these cases, your corrective action will necessarily involve using the Amazon FSx console, API, or AWS CLI to update the required configuration parameters\.

Other issues may not require changing any Active Directory configuration parameters, such as changing your domain controller firewall settings or VPC Security Groups\. In these cases, however, you will need to take further action before the file system can become `Available`\. After ensuring your Active Directory environment is configured properly, simply update your file system’s Active Directory configuration parameters to their current \(unchanged\) values by using the he Amazon FSx console, API, or AWS CLI\.

**Topics**
+ [Misconfigured file system: Amazon FSx can't reach either the DNS servers or domain controllers for your domain\.](#w213aac41c13c19)
+ [Misconfigured file system: The service account credentials are invalid](#w213aac41c13c21)
+ [Misconfigured file system: The service account provided doesn't have permission to join the file system to the domain](#w213aac41c13c23)
+ [Misconfigured file system: The service account can't join any more computers to domain](#w213aac41c13c25)
+ [Misconfigured file system: The service account doesn't have access to the OU](#w213aac41c13c27)

## Misconfigured file system: Amazon FSx can't reach either the DNS servers or domain controllers for your domain\.<a name="w213aac41c13c19"></a>

A file system will go into a `Misconfigured` state when Amazon FSx can't communicate with your Microsoft Active Directory domain controller or controllers\.

To resolve this situation, do the following:

1. Make sure that your networking configuration allows traffic from the file system to the domain controller\.

1. Use the [ Amazon FSx Active Directory Validation tool](validate-ad-config.md) to test and verify the network settings for your self\-managed Active Directory\. For more information, see [Using Amazon FSx with your self\-managed Microsoft Active Directory](self-managed-AD.md)\. 

1. Review the file system's self\-managed Active Directory configuration in the Amazon FSx console\.

1. To update the file system's self\-managed Active Directory configuration, you can use the Amazon FSx console\.

   1. On the navigation pane, choose **File systems**, and choose the file system to update; the **File system details** page appears\.

   1. On **File system details** page, choose **Update** on the **Networking and security** tab\.

   You can also use the Amazon FSx CLI `update-file-system` command or the API operation [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html)\.

## Misconfigured file system: The service account credentials are invalid<a name="w213aac41c13c21"></a>

Amazon FSx can't establish a connection with your Microsoft Active Directory domain controller or controllers\. This is because the service account credentials provided are invalid\. For more information, see [Using Amazon FSx with your self\-managed Microsoft Active Directory](self-managed-AD.md)\. 

To resolve the misconfiguration, do the following:

1. Verify that you are using the correct service account, and you are using the correct credentials for that account\.

1. Then update the file system's configuration with the correct service account or account credentials using the Amazon FSx console\.

   1. On the navigation pane, choose **File systems**, and choose the misconfigured file system to update\.

   1. On the **File system details** page, choose **Update** in the **Networking and security** tab\.

   You can also use the Amazon FSx API operation `update-file-system`\. To learn more, see the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) in the *Amazon FSx API Reference*\.

## Misconfigured file system: The service account provided doesn't have permission to join the file system to the domain<a name="w213aac41c13c23"></a>

Amazon FSx can't establish a connection to your Microsoft Active Directory domain controllers\. This is because the service account provided doesn't have permission to join the file system to the domain with the specified OU\. 

To resolve the misconfiguration, do the following:

1. Add the required permissions to the Amazon FSx service account, or create a new service account with the required permissions\. For more information about doing this, see [ Delegating privileges to your Amazon FSx service account ](self-managed-AD-best-practices.md#connect_delegate_privileges)\.

1. Then update the file system's self\-managed Active Directory configuration with the new service account credentials\. To update the configuration, you can use the Amazon FSx console\. 

   1. On the navigation pane, choose **File systems**, and choose the file system to update; the **File system details** page appears\.

   1. On **File system details** page, choose **Update** on the **Networking and security** tab\. 

   You can also use the Amazon FSx API operation `update-file-system`\. To learn more, see the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) in the *Amazon FSx API Reference*\.

## Misconfigured file system: The service account can't join any more computers to domain<a name="w213aac41c13c25"></a>

Amazon FSx can't establish a connection to your Microsoft Active Directory domain controllers\. In this case, this is because the service account provided has reached the maximum number of computers that it can join to the domain\. 

To resolve the misconfiguration, do the following:

1. Identify another service account or create a new service account that can join new computers to the domain\.

1. Then update the file system's self\-managed Active Directory configuration with the new service account credentials using the Amazon FSx console\.

   1. On the navigation pane, choose **File systems**, and choose the file system to update; the **File system details** page appears\.

   1. On **File system details** page, choose **Update** on the **Networking and security** tab\.

   You can also use the Amazon FSx API operation `update-file-system`\. To learn more, see the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) in the *Amazon FSx API Reference*\.

## Misconfigured file system: The service account doesn't have access to the OU<a name="w213aac41c13c27"></a>

Amazon FSx can't establish a connection to your Microsoft Active Directory domain controllers because the service account provided doesn't have access to the OU specified\. 

To resolve the misconfiguration, do the following:

1.  Identify another service account or create a new service account that has access to the OU\.

1. Then update the file system's self\-managed Active Directory configuration with the new service account credentials\.

   1. On the navigation pane, choose **File systems**, and choose the file system to update; the **File system details** page appears\.

   1. On **File system details** page, choose **Update** on the **Networking and security** tab\.

   You can also use the Amazon FSx API operation `update-file-system`\. To learn more, see the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) in the *Amazon FSx API Reference*\.