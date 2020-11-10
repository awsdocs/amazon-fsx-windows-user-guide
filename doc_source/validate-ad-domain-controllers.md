# Validating connectivity to your Active Directory domain controllers<a name="validate-ad-domain-controllers"></a>

 Before you create an Amazon FSx for Windows File Server file system joined to your Active Directory, use the Amazon FSx Active Directory Validation tool to validate the connectivity to your Active Directory domain\. You can use this test whether you are using Amazon FSx for Windows File Server with AWS Managed Microsoft Active Directory or with a self\-managed Active Directory configuration\. The Domain Controller Network Connectivity test \(Test\-FSxADControllerConnection\) does not run the full suite of network connectivity checks against every domain controller in the domain\. Instead, use this test to run network connectivity validation against a specific set of domain controllers\. <a name="test-ad-controller-connectivity"></a>

**To validate connectivity to your Active Directory domain controllers**

1. Launch an Amazon EC2 Windows instance in the same subnet and with the same Amazon VPC security groups that you will use for your Amazon FSx for Windows File Server file system\. For Multi\-AZ deployment types, use the subnet for the preferred active file server\. 

1. Join your EC2 Windows instance to your Active Directory\. For more information, see [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html) in the *AWS Directory Service Administration Guide*\.

1. Connect to your EC2 instance\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open a Windows PowerShell window \(using **Run as Administrator**\) on the EC2 instance\. 

   To test whether the required Active Directory module for Windows PowerShell is installed, use the following test command\.

   ```
   PS C:\> Import-Module ActiveDirectory
   ```

   If above returns an error, install it using the following command\.

   ```
   PS C:\> Install-WindowsFeature RSAT-AD-PowerShell
   ```

1. Download the network validation tool using the following command\. 

   ```
   PS C:\> Invoke-WebRequest "https://docs.aws.amazon.com/fsx/latest/WindowsGuide/samples/AmazonFSxADValidation.zip" -OutFile "AmazonFSxADValidation.zip"
   ```

1. Expand the zip file by using the following command\.

   ```
   PS C:\> Expand-Archive -Path "AmazonFSxADValidation.zip"
   ```

1. Add the AmazonFSxADValidation module to the current session\.

   ```
   PS C:\> Import-Module .\AmazonFSxADValidation
   ```

1. Set the value for the Active Directory domain controller IP address and run the connectivity test using the following commands:

   ```
   $ADControllerIp = '10.0.75.243'
   $Result = Test-FSxADControllerConnection -ADControllerIp $ADControllerIp
   ```

1. The following example demonstrates retrieving the test output, with results of a successful connectivity test\.

   ```
   PS C:\AmazonFSxADValidation> $Result
   
   Name                           Value
   ----                           -----
   TcpDetails                     {@{Port=88; Result=Listening; Description=Kerberos authentication}, @{Port=135; Resul...
   Server                         10.0.75.243
   UdpDetails                     {@{Port=88; Result=Timed Out; Description=Kerberos authentication}, @{Port=123; Resul...
   Success                        True
   
   
   PS C:\AmazonFSxADValidation> $Result.TcpDetails
   
   Port Result    Description
   ---- ------    -----------
     88 Listening Kerberos authentication
    135 Listening DCE / EPMAP (End Point Mapper)
    389 Listening Lightweight Directory Access Protocol (LDAP)
    445 Listening Directory Services SMB file sharing
    464 Listening Kerberos Change/Set password
    636 Listening Lightweight Directory Access Protocol over TLS/SSL (LDAPS)
   3268 Listening Microsoft Global Catalog
   3269 Listening Microsoft Global Catalog over SSL
   9389 Listening Microsoft AD DS Web Services, PowerShell
   ```

    The following example shows running the test and getting a failed result\. 

   ```
   PS C:\AmazonFSxADValidation> $Result = Test-FSxADControllerConnection -ADControllerIp $ADControllerIp
   WARNING: TCP 9389 failed to connect. Required for Microsoft AD DS Web Services, PowerShell. 
   Verify security group and firewall settings on both client and directory controller.
   WARNING: 1 ports failed to connect to 10.0.75.243. Check pre-requisites in
   https://docs.aws.amazon.com/fsx/latest/WindowsGuide/self-managed-AD.html#self-manage-prereqs
   
   PS C:\AmazonFSxADValidation> $Result
   
   Name                           Value
   ----                           -----
   TcpDetails                     {@{Port=88; Result=Listening; Description=Kerberos authentication}, @{Port=135; Resul...
   Server                         10.0.75.243
   UdpDetails                     {@{Port=88; Result=Timed Out; Description=Kerberos authentication}, @{Port=123; Resul...
   Success                        False
   FailedTcpPorts                 {9389}
   
   
   PS C:\AmazonFSxADValidation> $Result.FailedTcpPorts
   9389
   ```
   
   Windows socket error code mapping
   
   https://msdn.microsoft.com/en-us/library/ms740668.aspx
   ```