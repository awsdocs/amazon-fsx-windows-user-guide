# Limits<a name="limits"></a>

Following, you can find out about limits when working with Amazon FSx for Windows File Server\.

**Topics**
+ [Limits That You Can Increase](#soft-limits)
+ [Resource Limits for Each File System](#limits-MFS-resources-file-system)
+ [Additional Considerations](#limits-additional-considerations)
+ [Limits Specific to Microsoft Windows](#ntfs-limits)

## Limits That You Can Increase<a name="soft-limits"></a>

Following are the limits for Amazon FSx for Windows File Server for each AWS account, per AWS Region, that you can increase by contacting AWS Support\.


****  

| Resource | Default Limit | Can Be Increased Up To | 
| --- | --- | --- | 
| Number of file systems | 100 | Thousands | 
| Total storage for all file systems | 512 TiB | Multiple PiBs | 
| Total throughput capacity for all file systems | 10 GBps | Hundreds of GBps | 
| Total number of user\-initiated backups for all file system |  500  | Thousands | 

**To request a limit increase**

1. Open the [AWS Support Center](https://console.aws.amazon.com/support/home#/) page, sign in if necessary, and then choose **Create Case**\.

1. Under **Regarding**, choose **Service Limit Increase**\.

1. Under **Limit Type**, choose the type of limit to increase, fill in the necessary fields in the form, and then choose your preferred method of contact\.

## Resource Limits for Each File System<a name="limits-MFS-resources-file-system"></a>

Following are the limits on Amazon FSx for Windows File Server resources for each file system in an AWS Region\. 


****  

| Resource | Limit per file system | 
| --- | --- | 
| Number of tags | 50 | 
| Minimum storage capacity | 300 GB | 
| Maximum storage capacity | 64 TB | 
| Minimum throughput capacity | 8 MBps | 
| Maximum throughput capacity | 2,048 MBps | 
| Maximum number of file shares | 100,000 | 
| Retention period for automated backups | 35 days | 

For information on throughput capacity, see [Performance in Amazon FSx for Windows File Server ](performance.md)\.

## Additional Considerations<a name="limits-additional-considerations"></a>

In addition, note the following:
+ You can use each AWS Key Management Service \(AWS KMS\) key on up to 125 Amazon FSx file systems\.
+ For a list of AWS Regions where you can create file systems, see the [AWS General Reference](https://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem_region)\.
+ You map your file shares from Amazon EC2 instances in your virtual private cloud \(VPC\) with their Domain Name Service \(DNS\) names\. You can also map your file share on your EC2\-Classic instances \(which are not in a VPC\), but you must link them to your VPC by using ClassicLink\. For more information about using ClassicLink, see [ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Limits Specific to Microsoft Windows<a name="ntfs-limits"></a>

For more information, see [NTFS](https://docs.microsoft.com/en-us/windows/desktop/FileIO/filesystem-functionality-comparison#limits) limits on the Microsoft Windows Dev Center\.