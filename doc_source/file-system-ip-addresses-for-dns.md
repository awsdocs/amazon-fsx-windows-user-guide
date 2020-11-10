# Obtaining the correct file system IP addresses to use for DNS<a name="file-system-ip-addresses-for-dns"></a>

Amazon FSx only registers DNS records for a file system if you are using Microsoft DNS as the default DNS service\. If you are using a third\-party DNS, you will need to manually setup DNS entries for your Amazon FSx file systems\. This section describes how to obtain the correct file system IP addresses to use if you have to manually add the file system to your DNS\.

**How to obtain file system IP addresses to use for DNS A entries**

1. In the [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/), choose the file system that you want to obtain the IP address of to display the file system details page\.

1. In the **Network & security** tab do one of the following:
   + For Single\-AZ 1 file systems:
     + In the **Subnet** panel, choose the elastic network interface shown under **Network interface** to open the **Network Interfaces** page in the Amazon EC2 console\.
     + The IP address for the Single\-AZ 1 file system to use is shown in the **Primary private IPv4 IP** column\.
   + For Single\-AZ 2 or Multi\-AZ file systems:
     + In the **Preferred subnet** panel, choose the elastic network interface shown under **Network interface** to open the **Network Interfaces** page in the Amazon EC2 console\.
     + The IP address for the preferred subnet to use is shown in the **Secondary private IPv4 IP** column\.
     + In the Amazon FSx **Standby subnet** panel, choose the elastic network interface shown under **Network interface** to open the **Network Interfaces** page in the Amazon EC2 console\.
     + The IP address for the standby subnet to use is shown in the **Secondary private IPv4 IP** column\.