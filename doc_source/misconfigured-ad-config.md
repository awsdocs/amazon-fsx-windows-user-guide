# File system is in a misconfigured state<a name="misconfigured-ad-config"></a>

An Amazon FSx for Windows File Server file system can get into a **Misconfigured** state due to a number of reasons\. This can happen when you update your file system's self\-managed Active Directory configuration\.

## Misconfigured file system: Amazon FSx can't reach either the DNS servers or domain controllers for your domain\.<a name="w118aac41c13b7"></a>

A file system will go into a `Misconfigured` state when Amazon FSx can't communicate with your Microsoft Active Directory domain controller or controllers\.

To resolve this situation, do the following:

1. Make sure that your networking configuration allows traffic from the file system to the domain controller\.

1. Use the [ Amazon FSx Active Directory Validation tool](validate-ad-config.md) to test and verify the network settings for your self\-managed Active Directory\. For more information, see [Using Amazon FSx with Your Self\-Managed Microsoft Active Directory](self-managed-AD.md)\. 

1. Review the file system's self\-managed Active Directory configuration in the Amazon FSx console\.

1. To update the file system's self\-managed Active Directory configuration, you can use the Amazon FSx console\.

   1. On the navigation pane, choose **File systems**, and choose the file system to update; the **File system details** page appears\.

   1. On **File system details** page, choose **Update** on the **Networking and security** tab\.

   You can also use the Amazon FSx CLI `update-file-system` command or the API operation [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html)\.

## Misconfigured file system: The service account credentials are invalid<a name="w118aac41c13b9"></a>

Amazon FSx can't establish a connection with your Microsoft Active Directory domain controller or controllers\. This is because the service account credentials provided are invalid\. For more information, see [Using Amazon FSx with Your Self\-Managed Microsoft Active Directory](self-managed-AD.md)\. 

To resolve the misconfiguration, do the following:

1. Verify that you are using the correct service account, and you are using the correct credentials for that account\.

1. Then update the file system's configuration with the correct service account or account credentials using the Amazon FSx console\.

   1. On the navigation pane, choose **File systems**, and choose the misconfigured file system to update\.

   1. On the **File system details** page, choose **Update** in the **Networking and security** tab\.

   You can also use the Amazon FSx API operation `update-file-system`\. To learn more, see the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) in the *Amazon FSx API Reference*\.

## Misconfigured file system: The service account provided doesn't have permission to join the file system to the domain<a name="w118aac41c13c11"></a>

Amazon FSx can't establish a connection to your Microsoft Active Directory domain controllers\. This is because the service account provided doesn't have permission to join the file system to the domain with the specified OU\. 

To resolve the misconfiguration, do the following:

1. Add the required permissions to the Amazon FSx service account, or create a new service account with the required permissions\. For more information about doing this, see [ Delegating Privileges to Your Amazon FSx Service Account ](self-managed-AD-best-practices.md#connect_delegate_privileges)\.

1. Then update the file system's self\-managed Active Directory configuration with the new service account credentials\. To update the configuration, you can use the Amazon FSx console\. 

   1. On the navigation pane, choose **File systems**, and choose the file system to update; the **File system details** page appears\.

   1. On **File system details** page, choose **Update** on the **Networking and security** tab\. 

   You can also use the Amazon FSx API operation `update-file-system`\. To learn more, see the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) in the *Amazon FSx API Reference*\.

## Misconfigured file system: The service account can't join any more computers to domain<a name="w118aac41c13c13"></a>

Amazon FSx can't establish a connection to your Microsoft Active Directory domain controllers\. In this case, this is because the service account provided has reached the maximum number of computers that it can join to the domain\. 

To resolve the misconfiguration, do the following:

1. Identify another service account or create a new service account that can join new computers to the domain\.

1. Then update the file system's self\-managed Active Directory configuration with the new service account credentials using the Amazon FSx console\.

   1. On the navigation pane, choose **File systems**, and choose the file system to update; the **File system details** page appears\.

   1. On **File system details** page, choose **Update** on the **Networking and security** tab\.

   You can also use the Amazon FSx API operation `update-file-system`\. To learn more, see the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) in the *Amazon FSx API Reference*\.

## Misconfigured file system: The service account doesn't have access to the OU<a name="w118aac41c13c15"></a>

Amazon FSx can't establish a connection to your Microsoft Active Directory domain controllers because the service account provided doesn't have access to the OU specified\. 

To resolve the misconfiguration, do the following:

1.  Identify another service account or create a new service account that has access to the OU\.

1. Then update the file system's self\-managed Active Directory configuration with the new service account credentials\.

   1. On the navigation pane, choose **File systems**, and choose the file system to update; the **File system details** page appears\.

   1. On **File system details** page, choose **Update** on the **Networking and security** tab\.

   You can also use the Amazon FSx API operation `update-file-system`\. To learn more, see the [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) in the *Amazon FSx API Reference*\.