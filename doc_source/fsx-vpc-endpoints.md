# Amazon FSx for Windows File Server and interface VPC endpoints<a name="fsx-vpc-endpoints"></a>

You can improve the security posture of your VPC by configuring Amazon FSx to use an interface VPC endpoint\. Interface VPC endpoints are powered by [AWS PrivateLink](http://aws.amazon.com/privatelink), a technology that enables you to privately access Amazon FSx APIs without an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to communicate with Amazon FSx APIs\. Traffic between your VPC and Amazon FSx does not leave the AWS network\.

Each interface VPC endpoint is represented by one or more elastic network interfaces in your subnets\. A network interface provides a private IP address that serves as an entry point for traffic to the Amazon FSx API\.



## Considerations for Amazon FSx interface VPC endpoints<a name="privatelink-considerations"></a>

Before you set up an interface VPC endpoint for Amazon FSx, be sure to review [ Interface VPC endpoint properties and limitations](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#vpce-interface-limitations) in the *Amazon VPC User Guide*\.

You can call any of the Amazon FSx API operations from your VPC\. For example, you can create an FSx for Windows File Server file system by calling the CreateFileSystem API from within your VPC\. For the full list of Amazon FSx APIs, see [Actions](https://docs.aws.amazon.com/fsx/latest/APIReference/API_Operations.html) in the Amazon FSx API Reference\.

### VPC peering considerations<a name="privatelink-vpc-peering"></a>

You can connect other VPCs to the VPC with interface VPC endpoints using VPC peering\. VPC peering is a networking connection between two VPCs\. You can establish a VPC peering connection between your own two VPCs, or with a VPC in another AWS account\. The VPCs can also be in two different AWS Regions\.

Traffic between peered VPCs stays on the AWS network and does not traverse the public internet\. Once VPCs are peered, resources like Amazon Elastic Compute Cloud \(Amazon EC2\) instances in both VPCs can access the Amazon FSx API through interface VPC endpoints created in the one of the VPCs\.

## Creating an interface VPC endpoint for Amazon FSx API<a name="create-vpce-fsx"></a>

You can create a VPC endpoint for the Amazon FSx API using either the Amazon VPC console or the AWS Command Line Interface \(AWS CLI\)\. For more information, see [ Creating an interface VPC endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint) in the *Amazon VPC User Guide*\.

To create an interface VPC endpoint for Amazon FSx, use one of the following:
+ `com.amazonaws.region.fsx` – Creates an endpoint for Amazon FSx API operations\.
+ **`com.amazonaws.region.fsx-fips`** – Creates an endpoint for the Amazon FSx API that complies with [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

To use the private DNS option, you must set the `enableDnsHostnames` and `enableDnsSupport` attributes of your VPC\. For more information, see [ Viewing and updating DNS support for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-updating) in the *Amazon VPC User Guide*\.

Excluding AWS Regions in China, if you enable private DNS for the endpoint, you can make API requests to Amazon FSx with the VPC endpoint using its default DNS name for the AWS Region, for example `fsx.us-east-1.amazonaws.com`\. For the China \(Beijing\) and China \(Ningxia\) AWS Regions, you can make API requests with the VPC endpoint using `fsx-api.cn-north-1.amazonaws.com.cn` and `fsx-api.cn-northwest-1.amazonaws.com.cn`, respectively\.

For more information, see [ Accessing a service through an interface VPC endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#access-service-though-endpoint) in the *Amazon VPC User Guide*\.

## Creating a VPC endpoint policy for Amazon FSx<a name="create-vpce-policy-fsx"></a>

To further control access to the Amazon FSx API, you can optionally attach an AWS Identity and Access Management \(IAM\) policy to your VPC endpoint\. The policy specifies the following:
+ The principal that can perform actions\.
+ The actions that can be performed\.
+ The resources upon which actions can be performed\.

For more information, see [Controlling access to services with VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide*\.