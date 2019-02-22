# File System Access Control with Amazon VPC<a name="limit-access-security-groups"></a>

An Amazon FSx file system is accessible through an elastic network interface that resides in the virtual private cloud \(VPC\) based on the Amazon Virtual Private Cloud \(Amazon VPC\) service that you associate with your file system\. You access your Amazon FSx file system through its Domain Name Service \(DNS\) name, which maps to the file system's elastic network interface\. Only resources within the associated VPC, resources connected with the associated VPC by AWS Direct Connect or VPN, or resources within peered VPCs can access your file system's network interface\. For more information, see [What is Amazon VPC?](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) in the* Amazon VPC User Guide\.*

## Amazon VPC Security Groups<a name="fsx-vpc-security-groups"></a>

To further control network traffic going through your file system's elastic network interface within your VPC, you use security groups to limit access to your file systems\. A *security group *acts as a virtual firewall to control the traffic for its associated resources\. In this case, the associated resource is your file system's network interface\.

To use a security group to control access to your Amazon FSx file system, you add the inbound rules to control incoming traffic and outbound rules to control the outgoing traffic from your file system\. Make sure that you have the right network traffic rules in your security group to map your Amazon FSx file system's file share to a folder on your supported compute instance\.

For more information on security group rules, see [Security Group Rules](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#security-group-rules) in the *Amazon EC2 User Guide for Linux Instances\.*

**To create a security group for Amazon FSx**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2](https://console.aws.amazon.com/ec2)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. Specify a name and description for the security group\.

1. For **VPC**, choose the Amazon VPC associated with your file system to create the security group within that VPC\.

1. Add the following inbound rules:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/WindowsGuide/limit-access-security-groups.html)

Your Active Directory's security group must enable inbound access from the Amazon FSx file system’s security group\. By default, this access is enabled, though it can be manually removed if you modified the rules for your directory's security group\.

Likewise, the security group for your Amazon FSx file system must enable outbound access to your directory\. If you remove or change the default outbound rule for your file system's security group, you need to add the following outbound rule\.


| Type | Protocol | Port Range | Source | Description | 
| --- | --- | --- | --- | --- | 
| All traffic | All | 0\-65535 | Security group of the AWS Directory Service for Microsoft Active Directory to which you will join your file system | Communication with the managed Active Directory for Amazon FSx | 

Now that you've created your security group, you can associate it with your Amazon FSx file system's elastic network interface\.

**To associate a security group with your Amazon FSx file system**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. On the console dashboard, select your file system to view its details\.

1. From the menu, choose the **Network & Security** tab, and choose your file system's network interface ID \(for example, ENI\-01234567890123456\)\.

1. Choose **Actions**, **Change Security Groups**\.

1. In the **Change Security Groups** dialog box, select the security groups to use, and choose **Save**\.

## Amazon VPC Network ACLs<a name="limit-access-acl"></a>

Another option for securing access to the file system within your VPC is to establish network access control lists \(network ACLs\)\. Network ACLs are separate from security groups, but have similar functionality to add an additional layer of security to the resources in your VPC\. For more information on network ACLs, see [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_ACLs.html) in the *Amazon VPC User Guide\.*