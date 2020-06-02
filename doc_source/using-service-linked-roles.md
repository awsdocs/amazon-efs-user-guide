# Using the Amazon EFS Service\-Linked Role<a name="using-service-linked-roles"></a>

Amazon Elastic File System uses an AWS Identity and Access Management \(IAM\)[ service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. The Amazon EFS service\-linked role is a unique type of IAM role that is linked directly to Amazon EFS\. The predefined Amazon EFS service\-linked role includes permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up Amazon EFS easier because you don't have to manually add the necessary permissions\. Amazon EFS defines the permissions of its service\-linked role, and only Amazon EFS can assume its role\. The defined permissions include the trust policy and the permissions policy, and that permissions policy can't be attached to any other IAM entity\.

You can delete the Amazon EFS service\-linked role only after first deleting your Amazon EFS file systems\. This protects your Amazon EFS resources because you can't inadvertently remove permission to access the resources\.

The service\-linked role enables all API calls to be visible through CloudTrail\. This helps with monitoring and auditing requirements because you can track all actions that Amazon EFS performs on your behalf\. For more information, see [Log Entries for EFS Service Linked Roles](logging-using-cloudtrail.md#efs-service-linked-role-ct)\.

## Service\-Linked Role Permissions for Amazon EFS<a name="slr-permissions"></a>

Amazon EFS uses the service\-linked role named `AWSServiceRoleForAmazonElasticFileSystem` to allow Amazon EFS to call and manage AWS resources on behalf of your EFS file systems\.

The AWSServiceRoleForAmazonElasticFileSystem service\-linked role trusts the following services to assume the role:
+ `elasticfilesystem.amazonaws.com`

The role permissions policy allows Amazon EFS to complete the following actions:
+ `ec2:CreateNetworkInterface`
+ `ec2:DeleteNetworkInterface`
+ `ec2:DescribeSecurityGroups`
+ `ec2:DescribeSubnets`
+ `ec2:DescribeNetworkInterfaceAttribute`
+ `ec2:ModifyNetworkInterfaceAttribute`

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. For more information, see [Service\-Linked Role Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

**Note**  
 You must manually configure IAM permissions for AWS KMS when creating a new Amazon EFS file system that is encrypted at rest\. To learn more, see [Encrypting Data at Rest](encryption-at-rest.md)\. 

## Creating a Service\-Linked Role for Amazon EFS<a name="create-slr"></a>

You don't need to manually create a service\-linked role\. When you create mount targets for your EFS file system in the AWS Management Console, the AWS CLI, or the AWS API, Amazon EFS creates the service\-linked role for you\. 

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\. When you create mount targets for your EFS file system, Amazon EFS creates the service\-linked role for you again\. 

## Editing a Service\-Linked Role for Amazon EFS<a name="edit-slr"></a>

Amazon EFS doesn't allow you to edit the `AWSServiceRoleForAmazonElasticFileSystem` service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Deleting a Service\-Linked Role for Amazon EFS<a name="delete-slr"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. That way you don’t have an unused entity that is not actively monitored or maintained\. However, you must clean up the resources for your service\-linked role before you can manually delete it\.

**Note**  
If the Amazon EFS service is using the role when you try to delete the resources, then the deletion might fail\. If that happens, wait for a few minutes and try the operation again\.

**To delete Amazon EFS resources used by the AWSServiceRoleForAmazonElasticFileSystem**

Complete the following steps to delete Amazon EFS resources used by the AWSServiceRoleForAmazonElasticFileSystem\. For the detailed procedure, see [Step 4: Clean Up Resources and Protect Your AWS Account](gs-step-five-cleanup.md)

1.  On your Amazon EC2 instance, unmount the Amazon EFS file system\. 

1.  Delete the Amazon EFS file system\. 

1.  Delete the custom security group for the file system\. 
**Warning**  
If you used the default security group for your VPC, DO NOT delete it\.

**To manually delete the service\-linked role using IAM**

Use the IAM console, the AWS CLI, or the AWS API to delete the AWSServiceRoleForAmazonElasticFileSystem service\-linked role\. For more information, see [Deleting a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.