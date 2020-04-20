# Using Amazon FSx with AWS Directory Service for Microsoft Active Directory<a name="fsx-aws-managed-ad"></a>

AWS Directory Service for Microsoft Active Directory \(AWS Managed Microsoft AD\) provides fully managed, highly available, actual Active Directory \(AD\) directories in the cloud\. You can use these AD directories in your workload deployment\. 

If your organization is using AWS Managed Microsoft AD to manage identities and devices, we recommend that you integrate your Amazon FSx file system with AWS Managed Microsoft AD\. By doing this, you get a turnkey solution using Amazon FSx with AWS Managed Microsoft AD\. AWS handles the deployment, operation, high availability, reliability, security, and seamless integration of the two services, enabling you to focus on operating your own workload effectively\.

To use Amazon FSx with your AWS Managed Microsoft AD setup, you can use the Amazon FSx console\. When you create a new Amazon FSx for Windows File Server file system in the console, choose **AWS Managed AD** under the **Windows Authentication** section\. You also choose the specific directory that you want to use\. For more information, see [Step 1: Create Your File System](getting-started-step1.md)\. 

 If you have an existing corporate AD domain running in AWS in a virtual private cloud \(VPC\) using EC2 instances, you can enable user\-based authentication and access control\. You do this by establishing a trust relationship between your AWS Managed Microsoft AD and your corporate domain\. For Windows authentication in Amazon FSx, you only need a one\-way directional forest trust, where the AWS managed forest trusts the corporate domain forest\.

Your organization might manage identities and devices on a self\-managed Active Directory domain \(on\-premises or in the cloud\)\. If so, you can join your Amazon FSx file system directly to your existing, self\-managed AD domain\. For more information, see [Using Amazon FSx with Your Self\-Managed Microsoft Active Directory](self-managed-AD.md)\. 

You can also set up your system to benefit from a resource forest isolation model\. In this model, you isolate your resources, including your Amazon FSx file systems, into a separate AD forest from the one where your users are\. Or you can choose to join your file system to an AWS Managed Microsoft AD setup\. You then establish a one\-way forest trust relationship between an AWS Managed Microsoft AD domain that you create and your existing self\-managed AD domain\. 

Your corporate domain takes the role of the trusted domain, and the AWS Directory Service managed domain takes the role of the trusting domain\. Validated authentication requests travel between the domains in only one direction—allowing accounts in your corporate domain to authenticate against resources shared in the managed domain\. In this case, Amazon FSx interacts only with the managed domain\. The managed domain then passes on the authentication requests to your corporate domain\.

## Test Your Active Directory Configuration<a name="test-ad-config"></a>

Before creating your Amazon FSx file system, we recommend that you validate your Active Directory configuration using the Amazon FSx Network Validation tool\. For more information, see [Validating Your Active Directory Configuration](validate-ad-config.md)\.

The following related resources can help you as you use AWS Directory Service for Microsoft Active Directory with Amazon FSx for Windows File Server:
+ [What Is AWS Directory Service](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html) in the *AWS Directory Service Administration Guide*
+ [Create Your AWS Managed AD Directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_create_directory.html) in the *AWS Directory Service Administration Guide*
+ [When to Create a Trust Relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html) in the *AWS Directory Service Administration Guide*
+  [Walkthrough 1: Prerequisites for Getting Started](walkthrough01-prereqs.md) 