# AWS managed policies for Amazon FSx<a name="security-iam-awsmanpol"></a>

To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the `ViewOnlyAccess` AWS managed policy provides read\-only access to many AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.



## AWS managed policy: AmazonFSxDeleteServiceLinkedRoleAccess<a name="security-iam-awsmanpol-AmazonFSxDeleteServiceLinkedRoleAccess"></a>

You can't attach `AmazonFSxDeleteServiceLinkedRoleAccess` to your IAM entities\. This policy is linked to a service and used only with the service\-linked role for that service\. You cannot attach, detach, modify, or delete this policy\. For more information, see [Using service\-linked roles for Amazon FSx](using-service-linked-roles.md)\.

This policy grants administrative permissions that allow Amazon FSx to delete its Service Linked Role for Amazon S3 access, used only by Amazon FSx for Lustre\.

**Permissions details**

This policy includes permissions in `iam` to allow Amazon FSx to view, delete, and view the deletion status for the FSx Service Linked Roles for Amazon S3 access\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "iam:DeleteServiceLinkedRole",
                "iam:GetServiceLinkedRoleDeletionStatus",
                "iam:GetRole"
            ],
            "Resource": "arn:*:iam::*:role/aws-service-role/s3.data-source.lustre.fsx.amazonaws.com/AWSServiceRoleForFSxS3Access_*"
        }
    ]
}
```

## AWS managed policy: AmazonFSxFullAccess<a name="security-iam-awsmanpol-AmazonFSxFullAccess"></a>

You can attach AmazonFSxFullAccess to your IAM entities\. Amazon FSx also attaches this policy to a service role that allows Amazon FSx to perform actions on your behalf\. 

Provides full access to Amazon FSx and access to related AWS services\.

**Permissions details**

This policy includes the following permissions\.




+ `fsx` – Allows principals full access to perform all Amazon FSx actions\.
+ `ds` – Allows principals to view information about the AWS Directory Service directories\.
+ `iam` – Allows principles to create an Amazon FSx service linked role on the user's behalf\. This is required so that Amazon FSx can manage AWS resources on the user's behalf\.
+ `logs` – Allows principals to create log groups, log streams, and write events to log streams\. This is required so that users can monitor FSx for Windows File Server file system access by sending audit access logs to CloudWatch Logs\.
+ `firehose` – Allows principals to write records to a Amazon Kinesis Data Firehose\. This is required so that users can monitor FSx for Windows File Server file system access by sending audit access logs to Kinesis Data Firehose\.
+ `ec2` – Allows principals to create tags under the specified conditions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ds:DescribeDirectories",
                "fsx:*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "fsx.amazonaws.com"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "s3.data-source.lustre.fsx.amazonaws.com"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:/aws/fsx/*:log-group:*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "firehose:PutRecord"
            ],
            "Resource": [
                "arn:aws:firehose:*:*:deliverystream/aws-fsx-*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags"
            ],
            "Resource": [
                "arn:aws:ec2:*:*:route-table/*"
            ],
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/AmazonFSx": "ManagedByAmazonFSx"
                },
                "ForAnyValue:StringEquals": {
                    "aws:CalledVia": ["fsx.amazonaws.com"]
                }
            }
        }
    ]
}
```

## AWS managed policy: AmazonFSxConsoleFullAccess<a name="security-iam-awsmanpol-AmazonFSxConsoleFullAccess"></a>

You can attach the `AmazonFSxConsoleFullAccess` policy to your IAM identities\.

This policy grants administrative permissions that allow full access to Amazon FSx and access to related AWS services via the AWS Management Console\.

**Permissions details**

This policy includes the following permissions\.




+ `fsx` – Allows principals to perform all actions in the Amazon FSx management console\.
+ `cloudwatch` – Allows principals to view CloudWatch Alarms and Metrics in the Amazon FSx management console\.
+ `ds` – Allows principals to list information about an AWS Directory Service directory\.
+ `ec2` – Allows principals to create tags on route tables, list network interfaces, route tables, security groups, subnets and the VPC associated with an Amazon FSx file system\.
+ `kms` – Allows principals to list aliases for AWS Key Management Service keys\.
+ `s3` – Allows principals to list some or all of the objects in an Amazon S3 bucket \(up to 1000\)\.
+ `iam` – Grants permission to create a service linked role that allows Amazon FSx to perform actions on the user's behalf\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:DescribeAlarms",
                "cloudwatch:GetMetricData",
                "ds:DescribeDirectories",
                "ec2:DescribeNetworkInterfaceAttribute",
                "ec2:DescribeRouteTables",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcs",
                "firehose:ListDeliveryStreams",
                "fsx:*",
                "kms:ListAliases",
                "logs:DescribeLogGroups",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "fsx.amazonaws.com"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "s3.data-source.lustre.fsx.amazonaws.com"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags"
            ],
            "Resource": [
                "arn:aws:ec2:*:*:route-table/*"
            ],
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/AmazonFSx": "ManagedByAmazonFSx"
                },
                "ForAnyValue:StringEquals": {
                    "aws:CalledVia": ["fsx.amazonaws.com"]
                }
            }
        }
    ]
}
```

## AWS managed policy: AmazonFSxConsoleReadOnlyAccess<a name="security-iam-awsmanpol-AmazonFSxConsoleReadOnlyAccess"></a>

You can attach the `AmazonFSxConsoleReadOnlyAccess` policy to your IAM identities\.



This policy grants read\-only permissions to Amazon FSx and related AWS services so that users can view information about these services in the AWS Management Console\.

**Permissions details**

This policy includes the following permissions\.




+ `fsx` – Allows principals to view information about Amazon FSx file systems, including all tags, in the Amazon FSx Management Console\.
+ `cloudwatch` – Allows principals to view CloudWatch Alarms and Metrics in the Amazon FSx Management Console\.
+ `ds` – Allows principals to view information about an AWS Directory Service directory in the Amazon FSx Management Console\.
+ `ec2` – Allows principals to view network interfaces, security groups, subnets and the VPC associated with an Amazon FSx file system in the Amazon FSx Management Console\.
+ `kms` – Allows principals to view aliases for AWS Key Management Service keys in the Amazon FSx Management Console\.
+ `log` – Allows principals to describe the Amazon CloudWatch Logs log groups associated with the account making the request\. This is required so that principals can view the existing file access auditing configuration for an FSx for Windows File Server file system\.
+ `firehose` – Allows principals to describe the Amazon Kinesis Data Firehose delivery streams associated with the account making the request\. This is required so that principals can view the existing file access auditing configuration for an FSx for Windows File Server file system\.



```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:DescribeAlarms",
                "cloudwatch:GetMetricData",
                "ds:DescribeDirectories",
                "ec2:DescribeNetworkInterfaceAttribute",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcs",
                "firehose:ListDeliveryStreams",
                "fsx:Describe*",
                "fsx:ListTagsForResource",
                "kms:DescribeKey",
                "logs:DescribeLogGroups"
            ],
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AmazonFSxReadOnlyAccess<a name="security-iam-awsmanpol-AmazonFSxReadOnlyAccess"></a>

You can attach the `AmazonFSxReadOnlyAccess` policy to your IAM identities\.

This policy grants administrative permissions that allow read\-only access to Amazon FSx\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "fsx:Describe*",
                "fsx:ListTagsForResource"
            ],
            "Resource": "*"
        }
    ]
}
```

## Amazon FSx updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>

View details about updates to AWS managed policies for Amazon FSx since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the Amazon FSx [Document history](doc-history.md) page\.


| Change | Description | Date | 
| --- | --- | --- | 
| [AmazonFSxConsoleReadOnlyAccess](#security-iam-awsmanpol-AmazonFSxConsoleReadOnlyAccess) – Update to an existing policy | Amazon FSx added new permissions to enable users to view enhanced performance metrics and recommended actions for FSx for Windows File Server file systems in the Amazon FSx console\. | September 21, 2022 | 
| [AmazonFSxConsoleFullAccess](#security-iam-awsmanpol-AmazonFSxConsoleFullAccess) – Update to an existing policy | Amazon FSx added new permissions to enable users to view enhanced performance metrics and recommended actions for FSx for Windows File Server file systems in the Amazon FSx console\. | September 21, 2022 | 
| [AmazonFSxReadOnlyAccess](#security-iam-awsmanpol-AmazonFSxReadOnlyAccess) – Started tracking policy | This policy grants read\-only access to all Amazon FSx resources and any tags associated with them\. | February 4, 2022 | 
| [AmazonFSxDeleteServiceLinkedRoleAccess](#security-iam-awsmanpol-AmazonFSxDeleteServiceLinkedRoleAccess) – Started tracking policy | This policy grants administrative permissions that allow Amazon FSx to delete its Service Linked Role for Amazon S3 access\. | January 7, 2022 | 
| [AmazonFSxServiceRolePolicy](using-service-linked-roles.md#slr-permissions) – Update to an existing policy | Amazon FSx added new permissions to allow Amazon FSx to manage network configurations for Amazon FSx for NetApp ONTAP file systems\. | September 2, 2021 | 
| [AmazonFSxFullAccess](#security-iam-awsmanpol-AmazonFSxFullAccess) – Update to an existing policy | Amazon FSx added new permissions to allow Amazon FSx to create tags on EC2 route tables for scoped down calls\. | September 2, 2021 | 
| [AmazonFSxConsoleFullAccess](#security-iam-awsmanpol-AmazonFSxConsoleFullAccess) – Update to an existing policy | Amazon FSx added new permissions to allow Amazon FSx to create Amazon FSx for NetApp ONTAP Multi\-AZ file systems\. | September 2, 2021 | 
| [AmazonFSxConsoleFullAccess](#security-iam-awsmanpol-AmazonFSxConsoleFullAccess) – Update to an existing policy | Amazon FSx added new permissions to allow Amazon FSx to create tags on EC2 route tables for scoped down calls\. | September 2, 2021 | 
|  [AmazonFSxServiceRolePolicy](using-service-linked-roles.md#slr-permissions) – Update to an existing policy  |  Amazon FSx added new permissions to allow Amazon FSx to describe and write to CloudWatch Logs log streams\. This is required so that users can view file access audit logs for FSx for Windows File Server file systems using CloudWatch Logs\.  | June 8, 2021 | 
|  [AmazonFSxServiceRolePolicy](using-service-linked-roles.md#slr-permissions) – Update to an existing policy  |  Amazon FSx added new permissions to allow Amazon FSx to describe and write to Amazon Kinesis Data Firehose delivery streams\. This is required so that users can view file access audit logs for an FSx for Windows File Server file system using Amazon Kinesis Data Firehose\.  | June 8, 2021 | 
|  [AmazonFSxFullAccess](#security-iam-awsmanpol-AmazonFSxFullAccess) – Update to an existing policy  |  Amazon FSx added new permissions to allow principals to describe and create CloudWatch Logs log groups, log streams, and write events to log streams\. This is required so that principals can view file access audit logs for FSx for Windows File Server file systems using CloudWatch Logs\.  | June 8, 2021 | 
|  [AmazonFSxFullAccess](#security-iam-awsmanpol-AmazonFSxFullAccess) – Update to an existing policy  |  Amazon FSx added new permissions to allow principals to describe and write records to a Amazon Kinesis Data Firehose\. This is required so that users can view file access audit logs for an FSx for Windows File Server file system using Amazon Kinesis Data Firehose\.  | June 8, 2021 | 
|  [AmazonFSxConsoleFullAccess](#security-iam-awsmanpol-AmazonFSxConsoleFullAccess) – Update to an existing policy  |  Amazon FSx added new permissions to allow principals to describe the Amazon CloudWatch Logs log groups associated with the account making the request\. This is required so that principals can choose an existing CloudWatch Logs log group when configuring file access auditing for an FSx for Windows File Server file system\.  | June 8, 2021 | 
|  [AmazonFSxConsoleFullAccess](#security-iam-awsmanpol-AmazonFSxConsoleFullAccess) – Update to an existing policy  |  Amazon FSx added new permissions to allow principals to describe the Amazon Kinesis Data Firehose delivery streams associated with the account making the request\. This is required so that principals can choose an existing Kinesis Data Firehose delivery stream when configuring file access auditing for an FSx for Windows File Server file system\.  | June 8, 2021 | 
|  [AmazonFSxConsoleReadOnlyAccess](#security-iam-awsmanpol-AmazonFSxConsoleReadOnlyAccess) – Update to an existing policy  |  Amazon FSx added new permissions to allow principals to describe the Amazon CloudWatch Logs log groups associated with the account making the request\. This is required so that principals can view the existing file access auditing configuration for an FSx for Windows File Server file system\.  | June 8, 2021 | 
|  [AmazonFSxConsoleReadOnlyAccess](#security-iam-awsmanpol-AmazonFSxConsoleReadOnlyAccess) – Update to an existing policy  |  Amazon FSx added new permissions to allow principals to describe the Amazon Kinesis Data Firehose delivery streams associated with the account making the request\. This is required so that principals can view the existing file access auditing configuration for an FSx for Windows File Server file system\.  | June 8, 2021 | 
|  Amazon FSx started tracking changes  |  Amazon FSx started tracking changes for its AWS managed policies\.  | June 8, 2021 | 