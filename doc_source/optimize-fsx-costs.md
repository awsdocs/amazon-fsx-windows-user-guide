# Optimizing Costs with Amazon FSx<a name="optimize-fsx-costs"></a>

Amazon FSx for Windows File Server provides several features to help you optimize your total cost of ownership \(TCO\) based on your application needs\. You can pick the storage type \(HDD or SSD\) to achieve the right balance of cost and performance needs for your application\. You have the flexibility to pick throughput capacity separately from the amount of storage capacity to optimize your costs\. And, you can use data deduplication to optimize storage costs by eliminating redundant data on your file system\.

**Topics**
+ [Storage Type Options](#storage-type-options)
+ [Flexibility to Choose Storage and Throughput Independently](#configure-storage-throughput)
+ [Optimizing Storage Costs Using Data Deduplication](#optimize-cost-data-dedup)

## Storage Type Options<a name="storage-type-options"></a>

Amazon FSx for Windows File Server provides two types of storage—hard disk drives \(HDD\) and solid state drives \(SSD\)—to enable you to optimize cost/performance to meet your workload needs\. HDD storage is designed for a broad spectrum of workloads, including home directories, user and departmental shares, and content management systems\. SSD storage is designed for the highest\-performance and most latency\-sensitive workloads, including databases, media processing workloads, and data analytics applications\. For more information, see [Latency](performance.md#latency-fsxW) and [Amazon FSx for Windows File Server Pricing](http://aws.amazon.com/fsx/windows/pricing)\.

## Flexibility to Choose Storage and Throughput Independently<a name="configure-storage-throughput"></a>

With Amazon FSx for Windows File Server, you can configure your file system's storage and throughput capacity independently\. This gives you flexibility to achieve the right mix of cost and performance\. For example, you can choose to have a large amount of storage with a relatively small amount of throughput capacity for cold \(generally inactive\) workloads to save on unneeded throughput costs\. Or, as another example, you could choose to have a large amount of throughput capacity for a relatively small amount of storage capacity\. Higher throughput capacity comes with higher amounts of memory for caching on the file server, and you can take advantage of fast caching on the file server to optimize performance for actively accessed data\. For more information, see [Amazon FSx for Windows File Server PerformancePerformance](performance.md)\.

## Optimizing Storage Costs Using Data Deduplication<a name="optimize-cost-data-dedup"></a>

Large datasets often have redundant data, which increases data storage costs\. For example, user file shares can have multiple copies of the same file, stored by multiple users\. Software development shares can contain many binaries that remain unchanged from build to build\. You can reduce your data storage costs by turning on *data deduplication* for your file system\. When it's turned on, data deduplication automatically reduces or eliminates redundant data by storing duplicated portions of the dataset only once\. For more information about data deduplication, and how to easily turn it on for your Amazon FSx file system, see [Data Deduplication](using-data-dedup.md)\.