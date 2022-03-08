# Supported clients, access methods, and environments for Amazon FSx for Windows File Server<a name="supported-fsx-clients"></a>

You can access your Amazon FSx file systems using a variety of supported clients and methods from both AWS and on\-premises environments\.

**Topics**
+ [Supported clients](#supported-clients-fsx)
+ [Supported access methods](#access-methods)
+ [Supported environments](#access-environments)

## Supported clients<a name="supported-clients-fsx"></a>

Amazon FSx supports connecting to your file system from a wide variety of compute instances and operating systems\. It does this by supporting access through the Server Message Block \(SMB\) protocol, versions 2\.0 through 3\.1\.1\.

The following AWS compute instances are supported for use with Amazon FSx:
+ Amazon Elastic Compute Cloud \(Amazon EC2\) instances, including Microsoft Windows, Mac, Amazon Linux and Amazon Linux 2 instances\. For more information, see [Accessing file shares](using-file-shares.md#accessing-file-shares)\.
+ Amazon Elastic Container Service \(Amazon ECS\) containers\. For more information, see [ FSx for Windows File Server volumes](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/wfsx-volumes.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ WorkSpaces instances – To learn more, see the AWS blog post [ Using FSx for Windows File Server with Amazon WorkSpaces](http://aws.amazon.com/blogs/desktop-and-application-streaming/using-amazon-fsx-for-windows-file-server-with-amazon-workspaces/)\.
+ Amazon AppStream 2\.0 instances – To learn more, see the AWS blog post [ Using Amazon FSx with Amazon AppStream 2\.0](http://aws.amazon.com/blogs/desktop-and-application-streaming/using-amazon-fsx-with-amazon-appstream-2-0/)\. 
+ VMs running in VMware Cloud on AWS environments – To learn more, see the AWS blog post [Storing and Sharing Files with FSx for Windows File Server in a VMware Cloud on AWS Environment](http://aws.amazon.com/blogs/apn/storing-and-sharing-files-with-amazon-fsx-in-a-vmware-cloud-on-aws-environment/)\. 

The following operating systems are supported for use with Amazon FSx:
+ Windows Server 2008, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019\.
+ Windows Vista, Windows 7, Windows 8, Windows 8\.1, and Windows 10 \(including the Windows 7 and Windows 10 desktop experiences of WorkSpaces\)\.
+ Linux, using the `cifs-utils` tool\.

## Supported access methods<a name="access-methods"></a>

You can use the following access methods and approaches with Amazon FSx\.

### Accessing file systems using their default DNS names<a name="dns-name"></a>

FSx for Windows File Server provides a Domain Name System \(DNS\) name for every file system\. You access your FSx for Windows File Server file system by mapping a drive letter on your compute instance to your Amazon FSx file share using this DNS name\. To learn more, see [Using Microsoft Windows file shares](using-file-shares.md)\.

**Important**  
Amazon FSx only registers DNS records for a file system if you are using Microsoft DNS as the default DNS\. If you are using a third\-party DNS, you must manually set up DNS entries for your Amazon FSx file systems\. For information about choosing the correct IP addresses to use for the file system, see [Obtaining the correct file system IP addresses to use for DNS](file-system-ip-addresses-for-dns.md)\.

 To find the DNS name:
+ In the Amazon FSx console, choose **File systems**, and then choose **Details**\. View the DNS name in the **Network & Security** section\.
+ Or, view it in the response of the CreateFileSystem or DescribeFileSystems API command\.

For all Single\-AZ file systems joined to an AWS Managed Microsoft Active Directory, the DNS name looks like the following: `fs-0123456789abcdef0.ad-dns-domain-name`

For all Single\-AZ file systems joined to a self\-managed Active Directory, and any Multi\-AZ file system, the DNS name looks like the following: `amznfsxaa11bb22.ad-domain.com`

#### Using DNS names with Kerberos authentication<a name="kerberos-with-dns-name"></a>

We recommend that you use Kerberos\-based authentication and encryption in transit with Amazon FSx\. Kerberos provides the most secure authentication for clients accessing your file system\. To enable Kerberos\-based authentication and encryption of data in transit for your SMB sessions, use the file system's DNS name provided by Amazon FSx to access your file system\. 

If you have an external trust configured between your AWS Managed Microsoft Active Directory and your on\-premises Active Directory, to use the Amazon FSx Remote PowerShell with Kerberos authentication, you must configure a local group policy on the client for forest search order\. For more information, see [Configure Kerberos Forest Search Order \(KFSO\)](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh921473(v=ws.10)?redirectedfrom=MSDN) in the Microsoft documentation\.

### Accessing file systems using DNS aliases<a name="dns-aliases"></a>

FSx for Windows File Server provides a DNS name for every file system that you can use to access your file shares\. You can also enable access to Amazon FSx from DNS names other than the default DNS name that Amazon FSx creates by registering aliases for your FSx for Windows File Server file systems\. 

Using DNS aliases, you can move your Windows file share data to Amazon FSx and continue using your existing DNS names to access data on Amazon FSx\. DNS aliases also allow you to use meaningful names that make it easier to administer tools and applications to connect to your Amazon FSx file systems\. For more information, see [Managing DNS aliases](managing-dns-aliases.md)\.

#### Using DNS aliases with Kerberos authentication<a name="kerberos-with-aliases"></a>

We recommend that you use Kerberos\-based authentication and encryption in transit with Amazon FSx\. Kerberos provides the most secure authentication for clients accessing your file system\. To enable Kerberos authentication for clients that access Amazon FSx using a DNS alias, you must add service principal names \(SPNs\) that correspond to the DNS alias on your Amazon FSx file system’s Active Directory computer object\. 

You can optionally enforce clients that access the file system using a DNS alias to use Kerberos authentication and encryption by setting the following Group Policy Objects \(GPOs\) in your Active Directory:
+ **Restrict NTLM: Outgoing NTLM traffic to remote servers** \- Use this policy setting to deny or audit outgoing NTLM traffic from a computer to any remote server running the Windows operating system\.
+ **Restrict NTLM: Add remote server exceptions for NTLM authentication** \- Use this policy setting to create an exception list of remote servers to which client devices are allowed to use NTLM authentication if the *Network security: Restrict NTLM: Outgoing NTLM traffic to remote servers* policy setting is configured\.

For more information, see [Walkthrough 5: Using DNS aliases to access your file system](walkthrough05-file-system-custom-CNAME.md)\. 

### Working with FSx for Windows File Server file systems and DFS namespaces<a name="dfs-namespace"></a>

FSx for Windows File Server supports the use of Microsoft Distributed File System \(DFS\) Namespaces\. You can use DFS Namespaces to organize file shares on multiple file systems into one common folder structure \(a namespace\) that you use to access the entire file dataset\. You can use a name in your DFS Namespace to access your Amazon FSx file system by configuring its link target to be the file system's DNS name\. For more information, see [Grouping multiple file systems with DFS Namespaces](group-file-systems.md)\.

## Supported environments<a name="access-environments"></a>

You can access your file system from resources that are in the same VPC as your file system\. For more information and detailed instructions, see [Walkthrough 1: Prerequisites for getting started](walkthrough01-prereqs.md)\.

You can also access file systems created after February 22, 2019, from on\-premises resources and from resources that are in a different VPC, AWS account, or AWS Region\. The following table illustrates the environments from which Amazon FSx supports access from clients in each of the supported environments, depending on when the file system was created\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/supported-fsx-clients.html)

**Note**  
In some cases, you might want to access a file system that was created before December 17, 2020 from on\-premises using a non\-private IP address range\. To do this, create a new file system from a backup of the file system\. For more information, see [Working with backups](using-backups.md)\.

Following, you can find information about how to access your FSx for Windows File Server file systems from on\-premises and from different VPCs, AWS accounts, or AWS Regions\.

### Accessing FSx for Windows File Server file systems from on\-premises<a name="on-premise-access"></a>

FSx for Windows File Server supports the use of AWS Direct Connect or AWS VPN to access your file systems from your on\-premises compute instances\. With support for AWS Direct Connect, FSx for Windows File Server enables you to access your file system over a dedicated network connection from your on\-premises environment\. With support for AWS VPN, FSx for Windows File Server enables you to access your file system from your on\-premises devices over a secure and private tunnel\.

After you connect your on\-premises environment to the VPC associated with your Amazon FSx file system, you can access your file system using its DNS name or a DNS alias\. You do so just as you do from compute instances within the VPC\. For more information on AWS Direct Connect, see the *[AWS Direct Connect User Guide](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html)*\. For more information on setting up AWS VPN connections, see [VPN Connections](https://docs.aws.amazon.com/vpc/latest/userguide/vpn-connections.html) in the *Amazon VPC User Guide*\.

FSx for Windows File Server also supports the use of Amazon FSx File Gateway to provide low latency, seamless access to your in\-cloud FSx for Windows File Server file shares from your on\-premises compute instances\. For more information, see the *[Amazon FSx File Gateway User Guide](https://docs.aws.amazon.com/filegateway/latest/filefsxw/what-is-file-fsxw.html)*\.

### Accessing FSx for Windows File Server file systems from another VPC, account, or AWS Region<a name="different-vpc-account-access"></a>

You can access your FSx for Windows File Server file system from compute instances in a different VPC, AWS account, or AWS Region from that associated with your file system\. To do so, you can use VPC peering or transit gateways\. When you use a VPC peering connection or transit gateway to connect VPCs, compute instances that are in one VPC can access Amazon FSx file systems in another VPC\. This access is possible even if the VPCs belong to different accounts, and even if the VPCs reside in different AWS Regions\. 

A *VPC peering connection* is a networking connection between two VPCs that you can use to route traffic between them using private IPv4 or IP version 6 \(IPv6\) addresses\. You can use VPC peering to connect VPCs within the same AWS Region or between AWS Regions\. For more information on VPC peering, see [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon VPC Peering Guide*\.

A *transit gateway* is a network transit hub that you can use to interconnect your VPCs and on\-premises networks\. For more information about using VPC transit gateways, see [Getting Started with Transit Gateways](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html) in the *Amazon VPC Transit Gateways*\.

After you set up a VPC peering or transit gateway connection, you can access your file system using its DNS name\. You do so just as you do from compute instances within the associated VPC\.