# Troubleshooting file systems joined to an AWS Managed Microsoft Active Directory<a name="unable-to-create-aws-mad"></a>

Use the following sections to help troubleshoot problems trying to create an Amazon FSx for Windows File Server file system joined to your self\-managed Active Directory\.

## VPC security groups and network ACLs aren't using recommended security group configuration<a name="network-acls-sg-config"></a>

Make sure that the VPC security groups and network ACLs are configured using the recommended security group configuration\. For more information, see [Creating FSx Security Groups, Step 6](limit-access-security-groups.md#vpc-sg-step6)\.