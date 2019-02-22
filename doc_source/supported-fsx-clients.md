# Supported Clients, Access Methods, and Environments for Amazon FSx for Windows File Server<a name="supported-fsx-clients"></a>

Amazon FSx supports access to your file systems using a variety of clients and methods from both AWS and on\-premises environments\.

**Topics**
+ [Supported Clients](#supported-clients-fsx)
+ [Supported Access Methods](#access-methods)
+ [Supported Environments](#access-environments)

## Supported Clients<a name="supported-clients-fsx"></a>

Amazon FSx supports connecting to your file system from a wide variety of compute instances and operating systems by supporting access through the Server Message Block \(SMB\) protocol, versions 2\.0 through 3\.1\.1\. 

The following AWS compute instances are supported for use with Amazon FSx:
+ Amazon Elastic Compute Cloud \(Amazon EC2\) instances
+ Amazon WorkSpaces instances
+ Amazon AppStream 2\.0 instances
+ VMs running in VMware Cloud on AWS environments

The following operating systems are supported for use with Amazon FSx:
+ Windows Server 2008, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, and Windows Server 2016
+ Windows Vista, Windows 7, Windows 8, Windows 8\.1, and Windows 10 \(including the Windows 7 and Windows 10 desktop experiences of Amazon WorkSpaces\)
+ Linux, using the `cifs-utils` tool

## Supported Access Methods<a name="access-methods"></a>

You can use the following access methods and approaches with Amazon FSx\.

### Accessing Amazon FSx File Systems Using DNS Names<a name="dns-name"></a>

An Amazon FSx for Windows File Server file system is accessible through an elastic network interface\. This elastic network interface resides in the virtual private cloud \(VPC\) based on the Amazon Virtual Private Cloud \(Amazon VPC\) service that you associate with your file system\. Amazon FSx for Windows File Server provides a DNS name for every file system in the form of `fs-1234567891011121.domain.com`\. This name maps to the file system's elastic network interface\. The DNS name is composed as follows:
+ The file system's AWS resource identifier, `fs-1234567891011121` 
+ The Microsoft Active Directory domain name associated with your file system, `domain.com`

You access your Amazon FSx for Windows File Server file system using this DNS name\.

### Working with Amazon FSx for Windows File Server File Systems and DFS Namespaces<a name="dfs-namespace"></a>

Amazon FSx for Windows File Server supports the use of Microsoft Distributed File System \(DFS\) Namespaces\. You can use DFS Namespaces to organize file shares on multiple file systems into one common folder structure \(a namespace\) that you use to access the entire file dataset\. You can use a name in your DFS Namespace to access your Amazon FSx file system by configuring its link target to be the file system's DNS name\.

## Supported Environments<a name="access-environments"></a>

You can access your file system from resources that are in the same VPC as your file system\. For more information and detailed instructions, see [Walkthrough 1: Prerequisites for Getting Started](walkthrough01-prereqs.md)\.

In addition to accessing your file system from resources within the same VPC, you can also access your file systems from on\-premises resources and from resources that are in a different VPC, AWS account, or AWS Region\. Following, you can find information about how to access your Amazon FSx for Windows File Server file systems from on\-premises and from different VPCs, AWS accounts, or AWS Regions\.

As specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html), Amazon FSx supports access from any on\-premises compute instances with an IP address in the following private Internet Protocol version 4 \(IPv4\) address ranges\.
+ 10\.0\.0\.0–10\.255\.255\.255 \(10/8 prefix\)
+ 172\.16\.0\.0–172\.31\.255\.255 \(172\.16/12 prefix\)
+ 192\.168\.0\.0–192\.168\.255\.255 \(192\.168/16 prefix\)

### Accessing Amazon FSx for Windows File Server File Systems from On\-Premises<a name="on-premise-access"></a>

Amazon FSx for Windows File Server supports the use of AWS Direct Connect or AWS VPN to access your file systems from your on\-premises compute instances\. With support for AWS Direct Connect, Amazon FSx for Windows File Server enables you to access your file system over a dedicated network connection from your on\-premises environment\. With support for AWS VPN, Amazon FSx for Windows File Server enables you to access your file system from your on\-premises devices over a secure and private tunnel\.

After you connect your on\-premises environment to the VPC associated with your Amazon FSx file system, you can access your file system using its DNS name\. You do so just as you do from compute instances within the VPC\. For more information on AWS Direct Connect, see the *[AWS Direct Connect User Guide](https://docs.aws.amazon.com/directconnect/latest/UserGuide/)\.* For more information on setting up a VPN connection, see [VPN Connections](https://docs.aws.amazon.com/vpc/latest/userguide/vpn-connections.html) in the *Amazon VPC User Guide*\. 

### Accessing Amazon FSx for Windows File Server File Systems from Another VPC, Account, or AWS Region<a name="different-vpc-account-access"></a>

You can use VPC peering or transit gateways with Amazon FSx for Windows File Server to access your file systems from compute instances that are in a different VPC, AWS account, or AWS Region from the one associated with the file system\. When you use a VPC peering connection or transit gateway to connect VPCs, compute instances that are in one VPC can access Amazon FSx file systems in another VPC\. This access is possible even if the VPCs belong to different accounts, and even if the VPCs reside in different AWS Regions\. 

A *VPC peering connection* is a networking connection between two VPCs that you can use to route traffic between them using private IPv4 or IP version 6 \(IPv6\) addresses\. You can use VPC peering to connect VPCs within the same AWS Region or between AWS Regions\. For more information on VPC peering, see [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon VPC Peering Guide*\.

A *transit gateway *is a network transit hub that you can use to interconnect your VPCs and on\-premises networks\. For more information about using VPC transit gateways, see [Getting Started with Transit Gateways](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html) in the *Amazon VPC Transit Gateways*\.

After you set up a VPC peering or transit gateway connection, you can access your file system using its DNS name\. You do so just as you do from compute instances within the associated VPC\.