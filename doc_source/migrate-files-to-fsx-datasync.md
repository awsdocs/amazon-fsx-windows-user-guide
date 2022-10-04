# Migrating existing files to FSx for Windows File Server using AWS DataSync<a name="migrate-files-to-fsx-datasync"></a>

We recommend using AWS DataSync to transfer data between FSx for Windows File Server file systems\. DataSync is a data transfer service that simplifies, automates, and accelerates moving and replicating data between on\-premises storage systems and other AWS storage services over the internet or AWS Direct Connect\. DataSync can transfer your file system data and metadata, such as ownership, timestamps, and access permissions\.

DataSync supports copying NTFS access control lists \(ACLs\), and also supports copying file audit control information, also known as NTFS system access control lists \(SACLs\), which are used by administrators to control audit logging of user attempts to access files\.

You can use DataSync to transfer files between two FSx for Windows File Server file systems, and also move data to a file system in a different AWS Region or AWS account\. You can use DataSync with FSx for Windows File Server file systems for other tasks\. For example, you can perform one\-time data migrations, periodically ingest data for distributed workloads, and schedule replication for data protection and recovery\.

In AWS DataSync, a *location* for FSx for Windows File Server is an endpoint for an FSx for Windows File Server\. You can transfer files between a location for FSx for Windows File Server and a location for other file systems\. For information, see [Working with Locations](https://docs.aws.amazon.com/datasync/latest/userguide/working-with-locations.html) in the *AWS DataSync User Guide*\.

DataSync accesses your FSx for Windows File Server using the Server Message Block \(SMB\) protocol\. It authenticates with the user name and password that you configure in the AWS DataSync console or AWS CLI\.

## Prerequisites<a name="migrate-data-sync-prereq"></a>

To migrate data into your Amazon FSx for Windows File Server setup, you need a server and network that meet the DataSync requirements\. To learn more, see [Requirements for DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/requirements.html) in the *AWS DataSync User Guide*\.

If are using HDD storage and will be performing large data transfers with DataSync, we recommend that you switch to using SSD storage\. For more information, see [Best practices for migrating existing file storage to FSx for Windows File Server](migrate-files-fsx.md#migrate-best-practices)\.

When you have the DataSync requirements in place, you can begin transfer as discussed following\.

## Basic steps for migrating files using DataSync<a name="migrate-data-sync-basic-steps"></a>

To transfer files from a source location to a destination location using DataSync, take the following basic steps:
+ Download and deploy an agent in your environment and activate it\.
+ Create and configure a source and destination location\.
+ Create and configure a task\.
+ Run the task to transfer files from the source to the destination\.

To learn how to transfer files from an existing on\-premises file system to your FSx for Windows File Server, see [Data transfer between self\-managed storage and AWS](https://docs.aws.amazon.com/datasync/latest/userguide/how-datasync-works.html#onprem-aws), [Creating a location for SMB](https://docs.aws.amazon.com/datasync/latest/userguide/create-smb-location.html), and [Creating a location for Amazon FSx for Windows File Server](https://docs.aws.amazon.com/datasync/latest/userguide/create-fsx-location.html) in the *AWS DataSync User Guide*\.

To learn how to transfer files from an existing in\-cloud file system to your FSx for Windows File Server, see [Deploy your agent as an Amazon EC2 instance](https://docs.aws.amazon.com/datasync/latest/userguide/deploy-agents.html#ec2-deploy-agent) in the *AWS DataSync User Guide*\.  