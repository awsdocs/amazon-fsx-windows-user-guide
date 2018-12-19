# Walkthrough 5: Using a Custom DNS Name for your File System<a name="walkthrough05-file-system-custom-CNAME"></a>

Amazon FSx for Windows File Server provides a DNS name for every file system of the form *filesystemID\.your\-domain* using Active Directory integrated DNS\. This DNS name is created on the AWS Directory Service for Microsoft Active Directory \(AWS Managed Microsoft AD\) directory to which you join your file system\. You have the option of adding a custom DNS name for your Amazon FSx file system within your AWS Managed Microsoft AD directory\. This can make it easier to manage mapping the file shares within your file systems for large organizations or for file systems with discrete use cases\. You can add a custom DNS name for your file system from a Windows Server client that is joined to the same AWS Microsoft AD directory using the DNS management console \(`dnsmgmt.msc`\)\.

**To add a custom DNS name to your Amazon FSx file system**

1. Before you can add your custom DNS name to your file system, launch the instance and connect it to the AWS Directory Service for Microsoft Active Directory to which you've joined your Amazon FSx file systems\. To perform this action, choose one of the following procedures from the *AWS Directory Service Administration Guide*:
   + [Seamlessly Join a Windows EC2 Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/launching_instance.html)
   + [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html)

1. Connect to your instance as a user in the **AWS Delegated Domain Name System Administrators** group\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open the **Start** menu and type **dnsmgmt\.msc** to open the **DNS Manager** application\.
**Note**  
If the DNS management console isn't already installed, you can install it from the **Add Roles and Features Wizard** of the **Server Manager** application\.

1. In **DNS Manager**, choose **Forward Lookup Zones** from the left side navigation\.

1. Context\-click on the name of your AWS Managed Microsoft AD directory, and choose **New Alias \(CNAME\)\.\.\.** to open the **New Resource Record** dialog box\.

1. Type in the **Alias name** that you'd like to use for your Amazon FSx file system\.

1. From **Fully qualified domain name \(FQDN\) for target host:** choose **Browse**\.

1. In the **Browse** dialog box that opens, double\-click on the record name for your target host\.

1. Double\-click on **Forward Lookup Zone**\.

1. Double\-click on the original name of your Amazon FSx file server\.

1. Choose the name of a server and choose **OK**\.

You've now added a CNAME for your Amazon FSx file system that you can use for accessing your data within this AWS Managed Microsoft AD directory\.