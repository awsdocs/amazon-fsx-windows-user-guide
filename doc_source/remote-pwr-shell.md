# You Can't Access Your File System Using Remote PowerShell<a name="remote-pwr-shell"></a>

There are a number of potential causes for being unable to connect to your file system using Remote PowerShell, each with their own resolution, as follows\.

**Potential Cause**  
The file system's security group lacks the required inbound rules to allow a remote PowerShell connection\.

**Resolution**  
The file system's security group must have an inbound rule that allows traffic on port 5985 in order to establish a Remote PowerShell session\. For more information, see [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups)\.

**Potential Cause**  
A language localization error occurs when trying to initiate a remote PowerShell session\.

**Resolution**  
You need to add the following `-SessionOption` to your command: `-SessionOption (New-PSSessionOption -uiCulture "en-US")`

Following are two examples using `-SessionOption` when initiating a remote PowerShell session on your file system\.

```
PS C:\Users\delegateadmin> Invoke-Command -ComputerName FSxFileSystem-DNS-Name -ConfigurationName FSxRemoteAdmin -scriptblock {fsx-command} -SessionOption (New-PSSessionOption -uiCulture "en-US")
```

```
PS C:\Users\delegateadmin> Enter-Pssession -ComputerName FSxFileSystem-DNS-Name -ConfigurationName FsxRemoteAdmin -SessionOption (New-PSSessionOption -uiCulture "en-US")
```