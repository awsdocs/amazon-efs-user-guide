# Managing access to encrypted file systems<a name="managing-encrypt"></a>

Using Amazon EFS, you can create encrypted file systems\. Amazon EFS supports two forms of encryption for file systems, encryption in transit and encryption at rest\. Any key management you need to perform is only related to encryption at rest\. Amazon EFS automatically manages the keys for encryption in transit\. 

If you create a file system that uses encryption at rest, data and metadata are encrypted at rest\. Amazon EFS uses AWS Key Management Service \(AWS KMS\) for key management\. When you create a file system using encryption at rest, you specify an AWS KMS key \. The KMS key can be `aws/elasticfilesystem` \(the AWS managed key for Amazon EFS\), or it can be a customer managed key that you manage\.

File data—the contents of your files—is encrypted at rest using the KMS key that you specified when you created your file system\. Metadata—file names, directory names, and directory contents—is encrypted using a key that Amazon EFS manages\.

The EFS AWS managed key for your file system is used as the KMS key for encrypting the metadata in your file system, for example file names, directory names, and directory contents\. You own the customer managed key used to encrypt file data \(the contents of your files\) at rest\.

You manage who has access to your KMS keys and the contents of your encrypted file systems\. This access is controlled by both AWS Identity and Access Management \(IAM\) policies and AWS KMS\. IAM policies control a user's access to Amazon EFS API actions\. AWS KMS key policies control a user's access to the KMS key you specified when the file system was created\. For more information, see the following:
+ [IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) in the *IAM User Guide*
+ [Using key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*
+ [Using grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) in the *AWS Key Management Service Developer Guide*\.

As a key administrator, you can import external keys\. You can also modify keys by enabling them, disabling them, or deleting them\. The state of the KMS key that you specified \(when you created the file system with encryption at rest\) affects access to its contents\. The KMS key must be in the `enabled` state for users to have access to the contents of an encrypted\-at\-rest file system that is encrypted using that key\.

## Performing administrative actions on Amazon EFS KMS keys<a name="use-kms-key"></a>

Following, you can find how to enable, disable, or delete the KMS keys associated with your Amazon EFS file system\. You can also learn about the behavior to expect from your file system when you perform these actions\.

### Disabling, deleting, or revoking access to the KMS key for a file system<a name="disable-efs-cmk"></a>

You can disable or delete your customer managed KMS keys, or you can revoke Amazon EFS access to your KMS keys\. Disabling and revoking access for Amazon EFS to your keys are reversible actions\. Exercise significant caution when deleting KMS keys\. Deleting a KMS key is an irreversible action\.

If you disable or delete the KMS key used for your mounted file system, the following is true:
+ That KMS key can't be used as the key for new encrypted\-at\-rest file systems\.
+ Existing encrypted\-at\-rest file systems that use that KMS key stop working after a period of time\.

If you revoke Amazon EFS access to a grant for any existing mounted file system, the behavior is the same as if you disabled or deleted the associated KMS key\. In other words, the encrypted\-at\-rest file system continues to function, but stops working after a period of time\.

You can prevent access to a mounted encrypted\-at\-rest file system that has a KMS key that you disabled, deleted, or revoked Amazon EFS access to\. To do this, unmount the file system and delete your Amazon EFS mount targets\.

You can't immediately delete an AWS KMS key, but you can schedule it for deletion in 7\-30 days\. While a KMS key is scheduled for deletion, you can't use it for cryptographic operations\. You can also cancel a KMS key's scheduled deletion\.

To learn how to disable and re\-enable customer managed KMS keys, see [Enabling and disabling keys](https://docs.aws.amazon.com/kms/latest/developerguide/enabling-keys.html) in the AWS Key Management Service Developer Guide\. To learn how to schedule deletion of customer managed KMS keys, see [Deleting KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html) in the AWS Key Management Service Developer Guide\.

## Related topics<a name="related-managing-encryption"></a>
+ For more information on encrypted data and metadata at rest in Amazon EFS, see [Data encryption in Amazon EFS](encryption.md)\.
+ For example key policies, see [Amazon EFS key policies for AWS KMS](encryption-at-rest.md#EFSKMSPolicy)\.
+ For a list of AWS CloudTrail log entries associated with an encrypted file system, see [Amazon EFS Log File Entries for Encrypted\-at\-Rest File Systems](logging-using-cloudtrail.md#efs-encryption-cloudtrail)\.
+ For more information on determining what accounts and services have access to your KMS keys, see [Determining access to an AWS KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/determining-access.html) in the *AWS Key Management Service Developer Guide*\.