# Updating the self\-managed Active Directory configuration<a name="update-self-ad-config"></a>

You can use the AWS Management Console, Amazon FSx API, or AWS CLI to update the username and password for the service account and the IP addresses for the Active Directory DNS servers of the self\-managed Active Directory configuration\. You can track the progress of a self\-managed Active Directory configuration update at any time using the AWS Management Console, CLI, and API\. For more information, see [Monitoring self\-managed Active Directory updates](#monitor-self-ad-update)\.

**To update the self\-managed Active Directory configuration \(Console\)**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems**, and choose the Windows file system for which you want to update self\-managed AD configuration\.

1. In the **Network & security** tab, then choose **Update** for the **DNS server IP addresses**, or for the service account username, depending on which Active Directory properties you are updating\.

1. Enter the new DNS server IP addresses, or the new service account credentials in the dialog that appears\.

1. Choose **Update** to initiate the Active Directory configuration update\.

   You can [monitor the update progress](#monitor-self-ad-update) using the AWS Management Console or the AWS CLI\.

**To update the self\-managed Active Directory configuration \(CLI\)**
+ To update the self\-managed Active Directory configuration of an FSx for Windows File Server file system, use the AWS CLI command [update\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html)\. Set the following parameters:
  + `--file-system-id` to the ID of the file system you are updating\.
  + `UserName` the new username for the self\-managed AD service account\.
  + `Password` the new password for the self\-managed AD service account\.
  + `DnsIps` the IP addresses for the self\-managed AD DNS servers\.

  ```
  aws fsx update-file-system \
      --file-system-id fs-0123456789abcdef0 \
      --windows-configuration SelfManagedActiveDirectoryConfiguration={UserName=username,Password=password,DnsIps=[192.0.2.0,192.0.2.24]},
  ```

  If the update action is successful, the service sends back an HTTP 200 response\. The AdminstrativeActions data in the response describes the request and its status\. For more information, see [Monitoring self\-managed Active Directory updates](#monitor-self-ad-update)\.

## Monitoring self\-managed Active Directory updates<a name="monitor-self-ad-update"></a>

You can monitor the progress of a self\-managed Active Directory configuration update using the AWS Management Console, the API, or the AWS CLI\.

### Monitoring updates in the console<a name="monitor-self-ad-update-action-console"></a>

In the **Updates** tab in the **File system details** window, you can view the 10 most recent updates for each update type\.

![\[Console screen shot showing recent updates list.\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/images/fs-updates-panel.png)

For self\-managed Active Directory updates, you can view the following information\.

****Update type****  
Supported types are as follows:  
+ DNS server IP address
+ Service account credentials

****Target value****  
The desired value to update the file system property to\. For **Service account credentials** updates, only the user name is shown, service account passwords are never included in this field\.

****Status****  
The current status of the update\. For self\-managed Active Directory updates, the possible values are as follows:  
+ **Pending** – Amazon FSx has received the update request, but has not started processing it\.
+ **In progress** – Amazon FSx is processing the update request\.
+ **Completed** – The file system update completed successfully\.
+ **Failed** – The file system update failed\. Choose the question mark \(**?**\) to see details about the failure\.

****Progress %****  
Displays the progress of the file system update as percent complete\.

****Request time****  
The time that Amazon FSx received the update action request\.

### Monitoring updates using the AWS CLI and API<a name="monitor-self-ad-update-action-cli-api"></a>

You can view and monitor file system update requests that are in progress using the [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command and the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API action\. The `AdministrativeActions` array lists the 10 most recent update actions for each administrative action type\. 

The following example shows an excerpt of the response of a describe\-file\-systems CLI command show two self\-managed AD file system updates\.

```
        {
            "OwnerId": "111122223333",
            .
            .
            .
            "StorageCapacity": 1000,
            "AdministrativeActions": [
                {
                    "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
                    "RequestTime": 1581694766.757,
                    "Status": "PENDING",
                    "TargetFileSystemValues": {
                        "WindowsConfiguration": {
                            "SelfManagedActiveDirectoryConfiguration": {
                                "UserName": "serviceUser",
                            }
                        }
                    }
                },
                {
                    "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
                    "RequestTime": 1619032957.759,
                    "Status": "FAILED",
                    "TargetFileSystemValues": {
                        "WindowsConfiguration": {
                            "SelfManagedActiveDirectoryConfiguration": {
                            "DnsIps": [
                                    "10.0.138.161"
                                ]
                            }
                        }
                    },
                    "FailureDetails": {
                        "Message": "Failure details message."
                    }
                }
            ],
     .
     .
     .
```