# Migrating file share configurations to Amazon FSx<a name="migrate-file-share-config-to-fsx"></a>

You can migrate an existing file share configuration to Amazon FSx by using the following procedure\. In this procedure, the source file server is the file server whose file share configuration you want to migrate to Amazon FSx\.

**Note**  
First migrate your files to Amazon FSx before migrating your file share configuration\. For more information, see [Migrating existing file storage to Amazon FSx for Windows File Server](migrate-files-fsx.md)\.

**To migrate existing file shares to Amazon FSx for Windows File Server**

1. On the source file server, choose **Run as Administrator** from the context menu\. Open **Windows PowerShell** as an administrator\.

1. Export the source file server's file shares to a file named `SmbShares.xml` by running the following commands in the PowerShell\. Replace F: in this example with the drive letter on your file server from which you are exporting file shares\.

   ```
   $shareFolder = Get-SmbShare -Special $false | ? { $_.Path -like “F:\*” }
   $shareFolder | Export-Clixml -Path F:\SmbShares.xml
   ```

1. Edit the `SmbShares.xml` file, replacing all references to F: \(your drive letter\) to D: as Amazon FSx file systems reside on D:\.

1. Import the existing file share configuration to Amazon FSx for Windows File Server\. On a client that has access to your destination Amazon FSx file system and the source file server, copy the saved file share configuration\. Then import it into a variable by using the following command\.

   ```
   $shares = Import-Clixml -Path F:\SmbShares.xml
   ```

1. Prepare the credential object required to create the file shares on your Amazon FSx for Windows File Server file server using one of the following options\.

   To generate the credential object interactively, use the following command\.

   ```
   $credential = Get-Credential
   ```

   To generate the credential object using an AWS Secrets Manager resource, use the following command\.

   ```
   $credential = ConvertFrom-Json -InputObject (Get-SECSecretValue -SecretId $AdminSecret).SecretString
   $FSxAdminUserCredential = (New-Object PSCredential($credential.UserName,(ConvertTo-SecureString $credential.Password -AsPlainText -Force)))
   ```

1. Migrate the file share configuration to your Amazon FSx file server using the following script\.

   ```
   $FSxAcceptedParameters = ("ContinuouslyAvailable", "Description", "ConcurrentUserLimit", "CATimeout", "FolderEnumerationMode", "CachingMode", "FullAccess", "ChangeAccess", "ReadAccess", "NoAccess", "SecurityDescriptor", "Path", "Name", "EncryptData")
   ForEach ($item in $shares) {
       $param = @{};
       Foreach ($property in $item.psObject.properties) {
           if ($property.Name -In $FSxAcceptedParameters) {
               $param[$property.Name] = $property.Value
           }
       }
       Invoke-Command -ConfigurationName FSxRemoteAdmin -ComputerName amznfsxxxxxxxxx.corp.com -ErrorVariable errmsg -ScriptBlock { New-FSxSmbShare -Credential $Using:credential @Using:param }
   }
   ```