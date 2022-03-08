# How to use FSx for Windows File Server metrics<a name="how_to_use_metrics"></a>

The metrics reported by Amazon FSx provide information that you can analyze in different ways\. The list following shows some common uses for the metrics\. These are suggestions to get you started, not a comprehensive list\.


| How do I determine\.\.\. | Relevant metrics | 
| --- | --- | 
| My file system's IOPS? | Total IOPS = SUM\(DataReadOperations \+ DataWriteOperations \+ MetadataOperations\)/Period \(in seconds\) | 
| My file system's throughput? | SUM\(DataReadBytes \+ DataWriteBytes\)/Period \(in seconds\)  | 

**Note**  
We recommend that you maintain an average throughput capacity utilization under 50% to ensure that you have enough spare throughput capacity for unexpected spikes in your workload, as well as for any background Windows storage operations \(such as storage synchronization, deduplication, or shadow copies\)\.