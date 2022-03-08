# Managing encryption in transit<a name="manage-encrypt-in-transit"></a>

You can use a set of custom PowerShell commands to control the encryption of your data in transit between your FSx for Windows File Server file system and clients\. You can limit file system access to only clients supporting SMB encryption so that data\-in\-transit is always encrypted\. When enforcement is turned on for encryption of data\-in\-transit, users accessing the file system from clients that do not support SMB 3\.0 encryption will not be able to access file shares for which encryption is turned on\.

You can also control encryption of data\-in\-transit on a file share\-level instead of file server\-level\. You can use file share\-level encryption controls to have a mix of encrypted and unencrypted file shares on the same file system if you want to enforce encryption in\-transit for some file shares that have sensitive data, and allow all users to access some other file shares\. Server\-wide encryption has precedence over share level encryption\. If global encryption is enabled, you cannot selectively disable encryption for certain shares\.

You can manage user in\-transit encryption on your file system using the Amazon FSx CLI for remote management on PowerShell\. To learn how to use this CLI, see [Getting started with the Amazon FSx CLI for remote management on PowerShellGetting started](remote-pwrshell.md)\. 

Following are commands that you can use to manage user in\-transit encryption on your file system\.


| Encryption in Transit Command | Description | 
| --- | --- | 
|  Get\-FSxSmbServerConfiguration  |  Retrieves the Server Message Block \(SMB\) server configuration\.  | 
|  Set\-FSxSmbServerConfiguration  |  This command has two options for configurig in\-transit encryption: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/manage-encrypt-in-transit.html)  | 

The online help for each command provides a reference of all command options\. To access this help, run the command with \-?, for example Get\-FSxSmbServerConfiguration \-?\. 