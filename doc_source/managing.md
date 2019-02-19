# Managing Amazon EFS File Systems<a name="managing"></a>

File system management tasks refer to creating and deleting file systems, managing tags, and managing network accessibility of an existing file system\. Managing network accessibility is about creating and managing mount targets\. 

You can perform these file system management tasks using the Amazon EFS console, AWS Command Line Interface \(AWS CLI\), or programmatically, as discussed in the following sections\.

**Topics**
+ [Managing File System Network Accessibility](manage-fs-access.md)
+ [Managing File System Tags](manage-fs-tags.md)
+ [EFS Storage Classes](storage-classes.md)
+ [EFS Lifecycle Management](lifecycle-management-efs.md)
+ [Metering: How Amazon EFS Reports File System and Object Sizes](metered-sizes.md)
+ [Deleting an Amazon EFS File System](manage-delete-fs.md)
+ [Managing Access to Encrypted File Systems](#managing-encrypt)

If you are new to Amazon EFS, we recommend that you try the following exercises that provide you with first\-hand end\-to\-end experience using an Amazon EFS file system:
+ [Getting Started](getting-started.md) – This exercise provides a console\-based, end\-to\-end setup in which you create a file system, mount it on an Amazon EC2 instance, and test the setup\. The console takes care of many things for you and thus helps you quickly set up the end\-to\-end experience\.
+ [Walkthrough: Create an Amazon EFS File System and Mount It on an Amazon EC2 Instance Using the AWS CLI](wt1-getting-started.md) – This walkthrough is similar to the Getting Started exercise, but it uses the AWS CLI to perform most of the tasks\. Because the CLI commands closely map to the Amazon EFS API, the walkthrough can help you familiarize yourself with the Amazon EFS API\. 

## Managing Access to Encrypted File Systems<a name="managing-encrypt"></a>

Using Amazon EFS, you can create encrypted file systems\. Amazon EFS supports two forms of encryption for file systems, encryption in transit and encryption at rest\. Any key management you need to perform is only related to encryption at rest\. Amazon EFS automatically manages the keys for encryption in transit\. 

If you create a file system that uses encryption at rest, data and metadata are encrypted at rest\. Amazon EFS uses AWS Key Management Service \(AWS KMS\) for key management\. When you create a file system using encryption at rest, you specify a customer master key \(CMK\)\. The CMK can be `aws/elasticfilesystem` \(the AWS\-managed CMK for Amazon EFS\), or it can be a CMK that you manage\.

File data—the contents of your files—is encrypted at rest using the CMK that you specified when you created your file system\. Metadata—file names, directory names, and directory contents—is encrypted by a key that Amazon EFS manages\.

The AWS\-managed CMK for your file system is used as the master key for the metadata in your file system, for example file names, directory names, and directory contents\. You own the CMK used to encrypt file data \(the contents of your files\) at rest\.

You manage who has access to your CMKs and the contents of your encrypted file systems\. This access is controlled by both AWS Identity and Access Management \(IAM\) policies and AWS KMS\. IAM policies control a user's access to Amazon EFS API actions\. AWS KMS key policies control a user's access to the CMK you specified when the file system was created\. For more information, see the following:
+ [IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) in the *IAM User Guide*
+ [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*
+ [Using Grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) in the *AWS Key Management Service Developer Guide*\.

As a key administrator, you can import external keys and you can modify keys by enabling, disabling, or deleting them\. The state of the CMK that you specified \(when you created the file system with encryption at rest\) affects access to its contents\. The CMK must be in the `enabled` state for users to have access to the contents of an encrypted\-at\-rest file system\.

### Performing Administrative Actions on Amazon EFS Customer Master Keys<a name="use-kms-key"></a>

Following, you can find how to enable, disable, or delete the CMKs associated with your Amazon EFS file system\. You can also learn about the behavior to expect from your file system when you perform these actions\.

#### Disabling, Deleting, or Revoking Access to the CMK for a File System<a name="disable-efs-cmk"></a>

You can disable or delete your customer managed CMKs, or you can revoke Amazon EFS access to your CMKs\. Disabling and revoking access for Amazon EFS to your keys are reversible actions\. Exercise significant caution when deleting CMKs\. Deleting a CMK is an irreversible action\.

If you disable or delete the CMK used for your mounted file system, the following is true:
+ That CMK can't be used as the master key for new encrypted\-at\-rest file systems\.
+ Existing encrypted\-at\-rest file systems that use that CMK stop working after a period of time\.

If you revoke Amazon EFS access to a grant for any existing mounted file system, the behavior is the same as if you disabled or deleted the associated CMK\. In other words, the encrypted\-at\-rest file system continues to function, but stops working after a period of time\.

To prevent access to a mounted encrypted\-at\-rest file system that has a CMK that you disabled, deleted, or revoked Amazon EFS access to, unmount the file system and delete your Amazon EFS mount targets\.

You can't immediately delete an AWS KMS CMK, but you can schedule it for deletion in 7\-30 days\. While a CMK is scheduled for deletion, you cannot use it for cryptographic operations\. You can also cancel a CMK's scheduled deletion\.

To learn how to disable and re\-enable customer managed CMKs, see [Enabling and Disabling Keys](https://docs.aws.amazon.com/kms/latest/developerguide/enabling-keys.html) in the AWS Key Management Service Developer Guide\. To learn how to schedule deletion of customer managed CMKs, see [Deleting Customer Master Keys](https://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html) in the AWS Key Management Service Developer Guide\.

### Related Topics<a name="related-managing-encryption"></a>
+ For more information on encrypted data and metadata at rest in Amazon EFS, see [Encrypting Data and Metadata in EFS](encryption.md)\.
+ For example key policies, see [Amazon EFS Key Policies for AWS KMS](encryption.md#EFSKMSPolicy)\.
+ For a list of AWS CloudTrail log entries associated with an encrypted file system, see [Amazon EFS Log File Entries for Encrypted\-at\-Rest File Systems](logging-using-cloudtrail.md#efs-encryption-cloudtrail)\.
+ For more information on determining what accounts and services have access to your CMKs, see [Determining Access to an AWS KMS Customer Master Key](https://docs.aws.amazon.com/kms/latest/developerguide/determining-access.html) in the *AWS Key Management Service Developer Guide*\.