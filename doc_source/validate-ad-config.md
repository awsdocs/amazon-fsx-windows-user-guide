# Validating Your Active Directory Configuration<a name="validate-ad-config"></a>

 Before you create an Amazon FSx for Windows File Server file system joined to your Active Directory, we recommend that you validate the network connectivity to your DNS servers using the Amazon FSx Network Validation tool\. To run the tool, you'll need the IPv4 addresses for your DNS servers\. <a name="test-ad-network-config"></a>

**To validate network connectivity to your DNS servers**

1. Launch an Amazon EC2 Windows instance in the same subnet and with the same Amazon VPC security groups that you will use for your Amazon FSx for Windows File Server file system\.

1. Join your EC2 Windows instance to your Active Directory\. For more information, see [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html) in the *AWS Directory Service Administration Guide*\.

1. Connect to your EC2 instance\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open a Windows PowerShell window on the EC2 instance\.

1. Download the network validation tool using the following command\.

   ```
   PS C:\> Invoke-WebRequest "https://docs.aws.amazon.com/fsx/latest/WindowsGuide/samples/AmazonFSxNetworkValidation.zip" -OutFile "AmazonFSxNetworkValidation.zip"
   ```

1. Expand the zip file by using the following command\.

   ```
   PS C:\> Expand-Archive -Path "AmazonFSxNetworkValidation.zip"
   ```

1. Add the AmazonFSxNetworkValidation module to the current session\.

   ```
   PS C:\> Import-Module .\AmazonFSxNetworkValidation
   ```

1. Validate the network connectivity to your AD domain controller by running the following command\. Replace *IP\_ADDRESS\_1* and *IP\_ADDRESS\_2* with the IPv4 addresses of the DNS servers for your AD domain that you will use when creating your Amazon FSx file system\.

   ```
   PS C:\> @("IP_ADDRESS_1", "IP_ADDRESS_2") | Test-FSxADControllerConnection
   ```

   The following is an example of a successful test reult\.

   ```
   PS C:\> $Result
   
   Name                           Value
   ----                           -----
   TcpDetails                     {@{Port=53; Result=Listening; Description=Domain Name System (DNS)}, @{Port=88; Resul...
   ADControllerIp                 10.0.75.243
   UdpDetails                     {@{Port=53; Result=Timed Out; Description=Domain Name System (DNS)}, @{Port=88; Resul...
   Success                        True
   ```

   The following is an example of a test result with errors\.

   ```
   PS C:\> $Result
   
   Name                           Value
   ----                           -----
   TcpDetails                     {@{Port=53; Result=Listening; Description=Domain Name System (DNS)}, @{Port=88; Resul...
   ADControllerIp                 10.0.75.243
   UdpDetails                     {@{Port=53; Result=Timed Out; Description=Domain Name System (DNS)}, @{Port=88; Resul...
   Success                        False
   FailedTcpPorts                 {9389}
   ```