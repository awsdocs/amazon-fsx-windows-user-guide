# What Is Amazon FSx for Windows File Server?<a name="what-is"></a>

Amazon FSx for Windows File Server provides fully managed Microsoft Windows file servers, backed by a fully native Windows file system\. Amazon FSx for Windows File Server has the features, performance, and compatibility to easily lift and shift enterprise applications to AWS\.

Amazon FSx supports a broad set of enterprise Windows workloads with fully managed file storage built on Microsoft Windows Server\. Amazon FSx has native support for Windows file system features and for the industry\-standard Server Message Block \(SMB\) protocol to access file storage over a network\. Amazon FSx is optimized for enterprise applications in the AWS Cloud, with native Windows compatibility, enterprise performance and features, and consistent submillisecond latencies\.

With file storage on Amazon FSx, the code, applications, and tools that Windows developers and administrators use today can continue to work unchanged\. Windows applications and workloads ideal for FSx include business applications, home directories, web serving, content management, data analytics, software build setups, and media processing workloads\.

As a fully managed service, Amazon FSx for Windows File Server eliminates the administrative overhead of setting up and provisioning file servers and storage volumes\. Additionally, Amazon FSx keeps Windows software up\-to\-date, detects and addresses hardware failures, and performs backups\. It also provides rich integration with other AWS services like AWS Directory Service for Microsoft Active Directory, Amazon WorkSpaces, AWS Key Management Service, and AWS CloudTrail\.

## Amazon FSx Resources: File Systems and File Shares<a name="fsx-resources"></a>

The primary resources in Amazon FSx are *file systems* and *backups*\. A file system is where you store and access your files and folders\. A file system is made up of a Windows file server and storage volumes, and is accessed with its DNS name\. When you create a file system, you specify an amount of storage capacity \(in GiB\) and an amount of throughput capacity \(in MBps\)\. All storage is SSD\-based, providing consistent submillisecond file operation latencies\.

A Windows file share is a specific folder \(and its subfolders\) within your file system that you make accessible to your compute instances with SMB\. Your file system already comes with a default Windows file share\. You can create and manage as many other Windows file shares as you want using the Windows **Shared Folders** graphical user interface \(GUI\) tool\.

### Accessing File Shares<a name="fsx-access-shares"></a>

Amazon FSx is accessible from compute instances with the SMB protocol \(supporting versions 2\.0 to 3\.1\.1\)\. You can access your shares from all Windows versions starting from Windows Server 2008 and Windows 7, as well as from current versions of Linux\. You can map your FSx file shares on Amazon Elastic Compute Cloud \(Amazon EC2\) instances, as well as on Amazon WorkSpaces instances, Amazon AppStream 2\.0 instances, and VMware Cloud on AWS VMs\. 

You can access your file shares from on\-premises compute instances using AWS Direct Connect or AWS VPN\. In addition to accessing file shares that are in the same VPC, AWS Account, and AWS Region as the file system, you can also access your shares on compute instances that are in a different Amazon VPC, AWS account, or AWS Region\. You do so using VPC peering or transit gateways\. For more information, see [Supported Access Methods](supported-fsx-clients.md#access-methods)\. 

## Security and Data Protection<a name="security-considerations"></a>

Amazon FSx provides multiple levels of security and compliance to help ensure that your data is protected\. It automatically encrypts data at rest \(for both file systems and backups\) using keys that you manage in AWS Key Management Service \(AWS KMS\)\. Data in transit is also automatically encrypted using SMB Kerberos session keys\. It has been assessed to comply with ISO and PCI\-DSS certifications, and is HIPAA eligible\.

Amazon FSx provides access control at the file and folder level with Windows access control lists \(ACLs\)\. It provides access control at the file system level using Amazon Virtual Private Cloud \(Amazon VPC\) security groups\. In addition, it provides access control at the API level using AWS Identity and Access Management \(IAM\) access policies\. Users accessing file systems are authenticated with Microsoft Active Directory\. Amazon FSx integrates with AWS CloudTrail to monitor and log your API calls letting you see actions taken by users on your Amazon FSx resources\.

Additionally, it protects your data by taking highly durable backups of your file system automatically on a daily basis and allows you to take additional backups at any point\.

## DFS Support<a name="DFS-support"></a>

Amazon FSx supports the use of Microsoft Distributed File System \(DFS\)\. Amazon FSx uses DFS for file system deployments across multiple AZs, organizing data on multiple file systems into one common folder structure, for scale\-out performance\.

## Pricing for Amazon FSx<a name="pricing"></a>

With Amazon FSx, there are no upfront hardware or software costs, and you pay for only the resources used, with no minimum commitments, setup costs, or additional fees\. For information about the pricing and fees associated with the service, see [Amazon FSx for Windows File Server Pricing](https://aws.amazon.com//fsx/windows/pricing)\.

## Assumptions<a name="assumptions"></a>

To use Amazon FSx, you need an AWS account with an Amazon EC2 instance, Amazon WorkSpaces instance, AppStream 2\.0 instance, or VM running in VMware Cloud on AWS environments of the supported type\.

In this guide, we make the following assumptions:
+ If you're using Amazon EC2, we assume that you're familiar with Amazon EC2\. For more information on how to use Amazon EC2, see [Amazon Elastic Compute Cloud documentation](https://docs.aws.amazon.com/ec2)\.
+ If you're using Amazon WorkSpaces, we assume that you're familiar with Amazon WorkSpaces\. For more information on how to use Amazon WorkSpaces, see [Amazon WorkSpaces User Guide](https://docs.aws.amazon.com/workspaces/latest/userguide/)\.
+ If you're using VMware Cloud on AWS, we assume that you're familiar with it\. For more information, see [VMware Cloud on AWS](https://aws.amazon.com/vmware)\.
+ We assume that you are familiar with Microsoft Active Directory concepts\.

### Prerequisites<a name="prerequisites"></a>

To create an Amazon FSx file system, you need the following:
+ An AWS account with the permissions necessary to create an Amazon FSx file system and an Amazon EC2 instance\. For more information, see [Setting Up](setting-up.md)\.
+ An Amazon EC2 instance running Microsoft Windows Server in the VPC based on the Amazon VPC service that you want to associate with your Amazon FSx file system\. For information on how to create one, see [Getting Started with Amazon EC2 Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2_GetStarted.html) in the *Amazon EC2 User Guide for Windows Instances\.*
+ Amazon FSx works with Microsoft Active Directory to perform user authentication\. You join your FSx file system to an AWS Directory Service for Microsoft Active Directory\. For more information, see [Step 1: Create Your File System](getting-started.md#getting-started-step1)\.
+ This guide assumes that you haven't changed the rules on the default security group for your VPC based on the Amazon VPC service\. If you have, you need to ensure that you add the necessary rules to allow network traffic from your Amazon EC2 instance to your Amazon FSx file system\. For more details, see [Security](security.md)\.
+ Install and configure the AWS Command Line Interface \(AWS CLI\)\. Supported versions are 1\.9\.12 and newer\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide\.*
**Note**  
You can check the version of the AWS CLI you're using with the `aws --version` command\.

## Amazon FSx for Windows File Server Forums<a name="fsx-forums"></a>

If you encounter issues while using Amazon FSx, use the [forums](https://forums.aws.amazon.com/forum.jspa?forumID=308)\.

## Are You a First\-Time User of Amazon FSx?<a name="first-time-user"></a>

If you are a first\-time user of Amazon FSx, we recommend that you read the following sections in order:

1. If you're ready to create your first Amazon FSx file system, try the [Getting Started with Amazon FSx](getting-started.md)\.

1. For information on performance, see [Performance in Amazon FSx for Windows File Server ](performance.md)\.

1. For Amazon FSx security details, see [Security](security.md)\.

1. For information on the Amazon FSx API, see [Amazon FSx API Reference](https://docs.aws.amazon.com/fsx/latest/APIReference/Welcome.html)\.