# To Mount a File Share on an Amazon EC2 Linux Instance Not Joined to Your Active Directory<a name="map-file-share-ec2-linux-command"></a>

The following procedure mounts an Amazon FSx file share to an Amazon EC2 Linux instance that is not joined to your active directory \(AD\)\. For an EC2 Linux instance that is *not* joined to your Active Directory, you can only mount an Amazon FSx for Windows File Server file share by using it's private IP address\. You can get the file system's private IP address in the Amazon FSx console \([https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\), in the **Network & security** tab, the **Preferred File Server IP Address**\.

 This example uses NTLM authentication\. To do this, you mount the file system as a user that is a member of the Microsoft Active Directory domain that the Amazon FSx for Windows File Server file system is joined to\. The credentials for the user account are provided in a text file that you create on your EC2 instance, `creds.txt`\. This file contains the user name, password, and domain for the user\.

```
$ cat creds.txt
username=user1
password=Password123
domain=EXAMPLE.COM
```

**To launch and configure the Amazon Linux EC2 instance**

1. Launch an Amazon Linux EC2 instance using the Amazon EC2 console [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\. For more information, see [Launch an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Connect to your Amazon Linux EC2 instance\. For more information, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Run the following command to install the `cifs-utils` package\. This package is used to mount network file systems like Amazon FSx on Linux\.

   ```
   $ sudo yum install cifs-utils
   ```

1. Create the mount point **/mnt/fsxx** where you plan to mount the Amazon FSx file system\.

   ```
   $ sudo mkdir -p /mnt/fsx
   ```

1. Create the `creds.txt` credentials file in the `/home/ec2-user` directory, using the format shared above\.

1. Set the `creds.txt` file permissions so that only you \(the owner\) can read and write to the file by running the following command\.

   ```
   $ chmod 700 creds.txt
   ```<a name="mnt-ip-addr"></a>

**To mount the file system**

1. You mount a file share not joined to your Active Directory by using its private IP address\. You can get the file system's private IP address using the Amazon FSx console \([https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\), in the **Network & security** tab, the **Preferred File Server IP Address**\. 

1. Mount the file system using the following command:

   ```
   $ sudo mount -t cifs //file-system-IP-address/file_share /mnt/fsx --verbose -o vers=3.0,sec=ntlmsspi,cred=/home/ec2-user/creds.txt,rsize=CIFSMaxBufSize,wsize=CIFSMaxBufSize,cache=none
   ```

   Replace `CIFSMaxBufSize` with the largest value allowed by your kernel\. Run the following command to get this value\.

   ```
   $ modinfo cifs | grep CIFSMaxBufSize
   parm:           CIFSMaxBufSize:Network buffer size (not including header). Default: 16384 Range: 8192 to 130048 (uint)
   ```

   The output shows the maximum buffer size is 130048\.

1. Verify that the file system is mounted by running the following command, which returns only CIFS file systems\.

   ```
   $ mount -l -t cifs
   //file-system-IP-address/file_share on /mnt/fsx type cifs (rw,relatime,vers=3.0,sec=ntlmsspi,cache=strict,username=user1,domain=CORP.EXAMPLE.COM,uid=0,noforceuid,gid=0,noforcegid,addr=192.0.2.0,file_mode=0755,dir_mode=0755,soft,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,echo_interval=60,actimeo=1)
   ```

The mount command used in this procedure does the following at the given points:
+ `//file-system-IP-address/file_share` – Specifies the IP address and share of the file system to mount\.
+ `-t cifs vers=3.0` – Specifies the type of file system as CIFS and the protocol version as 3\.0\.
+ `sec=ntlmsspi` – Specifies to use NT LAN Manager Security Support Provider Interface \(NTLMSSPI\) for authentication\.
+ `cred=/home/ec2-user/creds.txt` – Specifies where to get the user credentials\.
+ `/mnt/fsx` – Specifies the mount point for the FSx file share on your EC2 instance\.
+ `rsize=CIFSMaxBufSize,wsize=CIFSMaxBufSize` – Specifies the read and write buffer size as the maximum allowed by the CIFS protocol\. Replace `CIFSMaxBufSize` with the largest value allowed by your kernel\. Determine the `CIFSMaxBufSize` by running the following command\.

  ```
  $ modinfo cifs | grep CIFSMaxBufSize
  parm:           CIFSMaxBufSize:Network buffer size (not including header). Default: 16384 Range: 8192 to 130048 (uint)
  ```

  The output shows the maximum buffer size is 130048\.
+ `cache=none` – Sets the CIFS cache mode to none, that is not to cache file data at all\.