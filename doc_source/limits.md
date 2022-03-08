# Quotas<a name="limits"></a>

Following, you can find out about quotas when working with Amazon FSx for Windows File Server\.

**Topics**
+ [Quotas that you can increase](#soft-limits)
+ [Resource quotas for each file system](#limits-MFS-resources-file-system)
+ [Additional considerations](#limits-additional-considerations)
+ [Quotas specific to Microsoft Windows](#ntfs-limits)

## Quotas that you can increase<a name="soft-limits"></a>

Following are the quotas for Amazon FSx for Windows File Server for each AWS account, per AWS Region, that you can increase\.


****  

| Resource | Default | Description | 
| --- | --- | --- | 
|  Windows file systems  |  100  |  The maximum number of Amazon FSx for Windows Server file systems that you can create in this account\.  | 
|  Windows throughput capacity  |  10240  |  The total amount of throughput capacity \(in MBps\) allowed for all Amazon FSx for Windows file systems in this account\.  | 
|  Windows HDD storage capacity  |  524288  |  The maximum amount of HDD storage capacity \(in GiB\) allowed for all Amazon FSx for Windows File Server file systems in this account\.  | 
|  Windows SSD storage capacity  |  524288  |  The maximum amount of SSD storage capacity \(in GiB\) allowed for all Amazon FSx for Windows File Server file systems in this account\.  | 
|  Windows backups  |  500  |  The maximum number of user\-initiated backups for all Amazon FSx for Windows File Server file systems that you can have in this account\.  | 

**To request a quota increase**

1. Open the [Service Quotas console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/dashboard)\.

1. In the navigation pane, choose **AWS services**\.

1. Choose **Amazon FSx**\.

1. Choose a quota\.

1. Choose **Request quota increase**, and follow the directions to request a quota increase\.

1. To view the status of the quota request, choose **Quota request history** in the console navigation pane\.

For more information, see [Requesting a quota increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-quota-increase.html) in the *Service Quotas User Guide*\.

## Resource quotas for each file system<a name="limits-MFS-resources-file-system"></a>

Following are the quotas on Amazon FSx for Windows File Server resources for each file system in an AWS Region\.


****  

| Resource | Limit per file system | 
| --- | --- | 
| Maximum number of tags | 50 | 
| Maximum retention period for automated backups | 90 days | 
| Maximum number of backup copy requests in progress to a single destination Region per account\. | 5 | 
| Minimum storage capacity, SSD file systems | 32 GiB | 
| Minimum storage capacity, HDD file systems | 2,000 GiB | 
| Maximum storage capacity, SSD and HDD | 64 TiB | 
| Minimum throughput capacity | 8 MBps | 
| Maximum throughput capacity | 2,048 MBps | 
| Maximum number of file shares | 100,000 | 

## Additional considerations<a name="limits-additional-considerations"></a>

In addition, note the following:
+ You can use each AWS Key Management Service \(AWS KMS\) key on up to 125 Amazon FSx file systems\.
+ For a list of AWS Regions where you can create file systems, see [Amazon FSx Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/fsxn.html) in the *AWS General Reference*\.
+ You map your file shares from Amazon EC2 instances in your virtual private cloud \(VPC\) with their Domain Name Service \(DNS\) names\.

## Quotas specific to Microsoft Windows<a name="ntfs-limits"></a>

For more information, see [NTFS](https://docs.microsoft.com/en-us/windows/desktop/FileIO/filesystem-functionality-comparison#limits) limits on the Microsoft Windows Dev Center\.