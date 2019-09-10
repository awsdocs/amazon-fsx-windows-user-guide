# Using Microsoft Windows File Shares<a name="using-file-shares"></a>

A Microsoft Windows *file share* is a specific folder \(and its subfolders\) within your file system that you make accessible to your compute instances with the Server Message Block \(SMB\) protocol\. Your file system comes with a default Windows file share, named **share**\. You can create and manage as many other Windows file shares as you’d like using the Windows **Shared Folders** graphical user interface \(GUI\) tool\.

**Topics**
+ [Accessing File Shares](#accessing-file-shares)
+ [Managing File Shares](#managing-file-shares)

## Accessing File Shares<a name="accessing-file-shares"></a>

To access your file shares, you use the Windows *Map Network Drive* functionality to map a drive letter on your compute instance to your Amazon FSx file share\. The process of mapping a file share to a drive on your compute instance, known as mounting a file share in Linux, differs depending on the type of compute instance and the operating system\. After your file share is mapped, your applications and users can access files and folders on your file share as if they are local files and folders\.

Following, you can find procedures for mapping a file share on the different supported compute instances\.

### To Map a File Share on an Amazon EC2 Windows Instance \(Console\)<a name="map-file-share-ec2-win-console"></a>

1. Launch the Amazon EC2 instance and connect it to the Microsoft Active Directory to which you've joined your Amazon FSx file system\. To do this, choose one of the following procedures from the *AWS Directory Service Administration Guide*:
   + [Seamlessly Join a Windows EC2 Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/launching_instance.html)
   + [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html)

1. Connect to your instance\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. After you are connected, open File Explorer\.

1. On the navigation pane, open the context \(right\-click\) menu for **Network** and choose **Map Network Drive**\.

1. Choose a drive letter of your choice for **Drive**\.

1. Enter the fully qualified domain name \(FQDN\) name for your file share, which you construct from the Domain Name Service \(DNS\) name for your file system and the name of your Windows file share\. An example is `\\fs-012345678901234567.ad-domain.com\share` for **Folder**\.

1. Choose an option for **Reconnect at sign\-in**, which indicates whether the file share should reconnect at sign in, and then choose **Finish**\.

### To Map a File Share on an Amazon EC2 Windows Instance \(Command Prompt\)<a name="map-file-share-ec2-win-command"></a>

1. Launch the Amazon EC2 instance and connect it to the Microsoft Active Directory to which you've joined your Amazon FSx file system\. To do this, choose one of the following procedures from the *AWS Directory Service Administration Guide*:
   + [Seamlessly Join a Windows EC2 Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/launching_instance.html)
   + [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html)

1. Connect to your instance as a user in your AWS Managed Microsoft AD directory\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. After you are connected, open a command prompt window\.

1. Mount the file share with the following command, with a drive letter of your choice, for example `H`\.

   ```
   net use DriveLetter: \\fs-012345678901234567.ad-domain.com\share /persistent:yes
   ```

### To Mount a File Share on an Amazon EC2 Linux Instance<a name="map-file-share-ec2-linux-command"></a>

1. Launch the Amazon EC2 instance and connect it to the Microsoft Active Directory to which you've joined your Amazon FSx file system\. To do this, choose one of the following procedures from the *AWS Directory Service Administration Guide*:
   + [Seamlessly Join a Windows EC2 Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/launching_instance.html)
   + [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html)

1. Connect to your instance\. For more information, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. After you are connected, open a terminal\.

1. Run the following command to install the `cifs-utils` package\. This package is used to mount network file systems like Amazon FSx on Linux\.

   ```
   sudo yum install cifs-utils
   ```

1. Mount the file share with the following command\.

   ```
   sudo mount -t cifs -o vers=3.0,sec=krb5,cruid=user@DOMAIN //fs-012345678901234567.ad-domain.com/share local_path
   ```

## Managing File Shares<a name="managing-file-shares"></a>

Following, you can find a description of the various methods you can use to manage your file shares\. You can manage file shares on your Amazon FSx file system with the **Shared Folders** app\. The Shared Folders tool provides a central location for managing all shared folders on a Windows server\. The following procedures detail how to manage your file shares\.

1. Launch the Amazon EC2 instance and connect it to the Microsoft Active Directory to which you've joined your Amazon FSx file system\. To do this, choose one of the following procedures from the *AWS Directory Service Administration Guide*:
   + [Seamlessly Join a Windows EC2 Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/launching_instance.html)
   + [Manually Join a Windows Instance](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html)

1. Connect to your instance as a user that is a member of the file system administrators group \(**AWS Delegated FSx Administrators** in AWS Managed AD, and **Domain Admins** or the custom group you specified during creation for file system administration in your self\-managed Microsoft AD\)\. For more information, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. Open the **Start** menu and run **fsmgmt\.msc** using `Run As Administrator`\. Doing this opens the **Shared Folders** GUI tool\.

1. Choose **Action**, and then choose **Connect to another computer**\.

1. For **Another computer**, enter the DNS name of your Amazon FSx file system, for example `fs-012345678901234567.ad-domain.com`

1. Choose **OK**\. An entry for your Amazon FSx file system then appears in the list of **Shared Folders**\.

Now that you've connected **Shared Folders** to your FSx file system, you can manage the Windows file shares on the file system\. You can do so with the following actions:
+ **Create a new file share** – From **Shared Folders**, choose **Shares** in the left pane to see the active shares for your Amazon FSx file system\. Choose **New Share** and complete the **Create a Shared Folder** wizard\.
+ **Modify a file share** – From the **Shared Folders** app, open the context \(right\-click\) menu for the file share that you want to modify in the right pane, and choose **Properties**\. Modify the properties and choose **OK**\.
+ **Remove a file share** – From the **Shared Folders** app, open the context \(right\-click\) menu for the file share that you want to remove in the right pane, and then choose **Stop Sharing**\.