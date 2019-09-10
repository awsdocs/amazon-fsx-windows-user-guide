# Amazon FSx for Windows File Server Windows User Guide

-----
*****Copyright &copy; 2019 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What Is Amazon FSx for Windows File Server?](what-is.md)
+ [Setting Up](setting-up.md)
+ [Getting Started with Amazon FSx](getting-started.md)
   + [Amazon FSx File System Status](file-system-lifecycle-states.md)
+ [Supported Clients, Access Methods, and Environments for Amazon FSx for Windows File Server](supported-fsx-clients.md)
+ [Using Microsoft Windows File Shares](using-file-shares.md)
+ [Working with Active Directory in Amazon FSx for Windows File Server](aws-ad-integration-fsxW.md)
   + [Using Amazon FSx with AWS Directory Service for Microsoft Active Directory](fsx-aws-managed-ad.md)
      + [Using Amazon FSx with AWS Managed Microsoft AD in a Different VPC or Account](shared-mad.md)
   + [Using Amazon FSx with Your Self-Managed Microsoft Active Directory](self-managed-AD.md)
      + [Best Practices for Joining Amazon FSx for Windows File Server File Systems to a Self-managed Microsoft Active Directory Domain](self-managed-AD-best-practices.md)
      + [Self-Managed Active Directory Feature Support](ad-features.md)
      + [Joining an Amazon FSx File System to a Self-Managed Microsoft Active Directory Domain](creating-joined-ad-file-systems.md)
+ [Migrating Existing Files to Amazon FSx](migrate-to-fsx.md)
+ [Getting the Best Performance in Amazon FSx for Windows File Server](performance.md)
   + [Scaling Out Performance with Shards](scale-out-performance.md)
   + [Scaling Out Read Performance with Read Replicas](scale-out-read.md)
+ [Protecting Your Data with Shadow Copies and Backups](data-protection.md)
   + [Working with Shadow Copies](shadow-copies-fsxW.md)
      + [Managing Shadow Copies](manage-shadow-cpy.md)
   + [Working with Backups](using-backups.md)
      + [Setting Up a Custom Backup Schedule](custom-backup-schedule.md)
+ [Grouping Multiple File Systems into a Common Namespace](group-file-systems.md)
+ [Deploying Multi-AZ File Systems](multi-az-deployments.md)
+ [Monitoring Amazon FSx for Windows File Server](monitoring_overview.md)
   + [How to Use Amazon FSx for Windows File Server Metrics](how_to_use_metrics.md)
   + [Accessing CloudWatch Metrics](accessingmetrics.md)
   + [Creating CloudWatch Alarms to Monitor Amazon FSx](creating_alarms.md)
+ [Security in Amazon FSx](security.md)
   + [File- and Folder-Level Access Control Using Windows ACLs](limit-access-file-folder.md)
   + [File System Access Control with Amazon VPC](limit-access-security-groups.md)
   + [Resource Administration Access Control with IAM for Amazon FSx](access-control-overview.md)
      + [Using Service-Linked Roles for Amazon FSx](using-service-linked-roles.md)
      + [Amazon FSx API Permissions: Actions, Resources, and Conditions Reference](fsx-api-permissions-ref.md)
   + [Encryption of Data at Rest and Data in Transit](encryption.md)
+ [Logging Amazon FSx for Windows File Server API Calls with AWS CloudTrail](logging-using-cloudtrail-win.md)
+ [Working with Amazon FSx Maintenance Windows](maintenance-windows.md)
+ [Limits](limits.md)
+ [Amazon FSx Walkthroughs](walkthroughs.md)
   + [Walkthrough 1: Prerequisites for Getting Started](walkthrough01-prereqs.md)
   + [Walkthrough 2: Create a File System from a Backup](walkthrough02-create-from-backup.md)
   + [Walkthrough 3: Update an Existing File System](walkthrough03-update-file-system.md)
   + [Walkthrough 4: Using Amazon FSx with Amazon AppStream 2.0](walkthrough04-fsx-with-appstream2.md)
   + [Walkthrough 5: Using a Custom DNS Name for Your File System](walkthrough05-file-system-custom-CNAME.md)
+ [Troubleshooting Problems for Amazon FSx](troubleshooting.md)
   + [You Can't Access Your File System](unable-to-access.md)
   + [Trying to Create an Amazon FSx File System Fails](unable-to-create-fs.md)
      + [Troubleshooting File Systems Joined to an AWS-Managed Microsoft Active Directory](unable-to-create-aws-mad.md)
      + [Troubleshooting File Systems Joined to a Self-Managed Active Directory](unable-to-create-self-ad.md)
   + [File System Is In a Misconfigured State](misconfigured-ad-config.md)
   + [You can't Access Your File System Using Remote PowerShell](remote-pwr-shell.md)
+ [Document History](doc-history.md)