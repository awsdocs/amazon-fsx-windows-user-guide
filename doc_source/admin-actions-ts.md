# Storage or throughput capacity updates fail<a name="admin-actions-ts"></a>

There are a number of potential causes for file system storage and throughput capacity update requests to fail, each with their own resolution\.

## Storage capacity increase fails because Amazon FSx can't access the file system's KMS encryption key<a name="w265aac43c19b5"></a>

A storage capacity increase request failed because Amazon FSx was unable to access the file system's AWS Key Management Service \(AWS KMS\) encryption key\.

You need to ensure that Amazon FSx has access to the AWS KMS key in order to run the administrative action\. Use the following information to resolve the key access issue\. 
+ If the KMS key has been deleted, you must create a new file system from a backup using a new KMS key\. For more information, see [Walkthrough 2: Create a file system from a backup](walkthrough02-create-from-backup.md)\. You can retry the request after the new file system is available\.
+ If the KMS key is disabled, re\-enable it, and then retry the storage capacity increase request\. For more information, see [Enabling and disabling keys](https://docs.aws.amazon.com/kms/latest/developerguide/enabling-keys.html) in the *AWS Key Management Service Developer Guide*\.
+ If the key is invalid because of its pending deletion, you must create a new file system from a backup using a new KMS key\. You can retry the request after the new file system is available\. For more information, see [Walkthrough 2: Create a file system from a backup](walkthrough02-create-from-backup.md)\.
+ If the key is invalid because of its pending import, you must wait until the import has completed, and then retry the storage increase request\.
+ If the key's grant limit has been exceeded, you must request an increase in the number of grants for the key\. For more information, see [Resource quotas](https://docs.aws.amazon.com/kms/latest/developerguide/resource-limits.html) in the *AWS Key Management Service Developer Guide*\. When the quota increase is granted, retry the storage increase request\.

## Storage or throughput capacity update fails because the self\-managed Active Directory is misconfigured<a name="w265aac43c19b7"></a>

The storage capacity or throughput capacity update request failed because your file system's self\-managed Active Directory is in a misconfigured state\.

To resolve the specific misconfigured state, see [File system is in a misconfigured state](misconfigured-ad-config.md)\.

## Storage capacity increase fails because of insufficient throughput capacity<a name="w265aac43c19b9"></a>

The storage capacity increase request failed because the file system's throughput capacity is set to 8 MB/s\.

Increase the file system's throughput capacity to a minimum of 16 MB/s, then retry the request\. For more information, see [Managing throughput capacity](managing-throughput-capacity.md)\.

## Throughput capacity update to 8 MB/s fails<a name="w265aac43c19c11"></a>

A request to modify a file system's throughput capacity to 8 MB/s failed\.

This can occur when a storage capacity increase request is pending or in progress\. Storage capacity increases require a minimum throughput of 16 MB/s\. Wait until the storage capacity increase request has completed, and then retry the throughput capacity modification request\.