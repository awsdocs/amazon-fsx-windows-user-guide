# Troubleshooting shadow copies<a name="shadow-copy-ts"></a>

There are a number of potential causes when shadow copies are missing or inaccessible, as described in the following section\.

**Topics**
+ [Oldest shadow copies are missing](#w157aac41c23b7)
+ [All of my shadow copies are missing](#w157aac41c23b9)
+ [Cannot create Amazon FSx backups or access shadow copies on a recently restored or updated file system](#w157aac41c23c11)

## Oldest shadow copies are missing<a name="w157aac41c23b7"></a>

The oldest shadow copies are deleted in either of these situations:
+ If you have 512 shadow copies, the next shadow copy replaces the oldest shadow copy, regardless of the remaining allocated storage volume space for shadow copies\.
+ If the maximum shadow copy storage amount configured is reached, the next shadow copy replaces one or more of the oldest shadow copies, even if you have fewer than 512 shadow copies\.

Both results are expected behavior\. If you have insufficient storage allocated for shadow copies, consider increasing the storage you have allocated\.

## All of my shadow copies are missing<a name="w157aac41c23b9"></a>

During the creation of shadow copies, having insufficient I/O performance capacity on your file system \(for example, because you're using HDD storage, because the HDD storage has run out of burst capacity, or because the throughput capacity is insufficient\) can cause all shadow copies to be deleted by Windows Server because it is unable to maintain the shadow copies with the available I/O performance capacity\. Consider the following recommendations to help prevent this problem:
+ If you're using HDD storage, switch to using SSD storage\. You can do so by taking a backup of your file system and restoring it with the storage type being switched to SSD\.
+ Increase the file system's throughput capacity to a value three times your expected workload\.
+ Make sure that your file system has at least 320 MB of free space, in addition to the maximum shadow copy storage amount configured\.
+ Schedule shadow copies when you expect your file system to be idle\.

For more information, see [File System Recommendations for Shadow Copies](shadow-copies-fsxW.md#shadow-cpy-config-recommend)\.

## Cannot create Amazon FSx backups or access shadow copies on a recently restored or updated file system<a name="w157aac41c23c11"></a>

This is expected behavior\. Amazon FSx rebuilds shadow\-copy state on a recently restored file system and does not allow access to shadow copies or backups while rebuilding the shadow copy state\.