# Amazon FSx file system status<a name="file-system-lifecycle-states"></a>

You can view the status of an Amazon FSx file system by using the Amazon FSx console, the AWS CLI command [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html), or the API operation [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html)\.


| File system status  | Description | 
| --- | --- | 
| AVAILABLE | The file system is in a healthy state, and is reachable and available for use\. | 
| CREATING | Amazon FSx is creating a new file system\. | 
| DELETING | Amazon FSx is deleting an existing file system\. | 
| UPDATING | The file system is undergoing a customer\-initiated update\. | 
| MISCONFIGURED | The file system is in an impaired state due to a change in your Active Directory environment\. Your file system is either currently unavailable or at risk of losing availability, and backups may not succeed\. For information on restoring availability, see [File system is in a misconfigured state](misconfigured-ad-config.md)\. | 
| MISCONFIGURED\_UNAVAILABLE | The file system is currently unavailable due to a change in your Active Directory environment\. For information on restoring availability, see [File system is in a misconfigured state](misconfigured-ad-config.md)\. | 
| FAILED |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/file-system-lifecycle-states.html)  | 