# Encrypting Data and Metadata at Rest in EFS<a name="encryption"></a>

If you create an encrypted file system, data and metadata are encrypted at rest\. You choose whether to enable encryption for a file system when creating it\.

As with unencrypted file systems, you can create encrypted file systems through the AWS Management Console, the AWS CLI, or programmatically through the Amazon EFS API or one of the AWS SDKs\. Your organization might require the encryption of all data that meets a specific classification or is associated with a particular application, workload, or environment\. 

You can enforce data encryption policies for Amazon EFS file systems by using Amazon CloudWatch and AWS CloudTrail to detect the creation of a file system and verify that encryption is enabled\. For more information, see [Walkthrough 6: Enforcing Encryption on an Amazon EFS File System at Rest](efs-enforce-encryption.md)\.

**Note**  
The AWS key management infrastructure uses Federal Information Processing Standards \(FIPS\) 140\-2 approved cryptographic algorithms\. The infrastructure is consistent with National Institute of Standards and Technology \(NIST\) 800\-57 recommendations\.

## When to Use Encryption<a name="whenencrypt"></a>

If your organization is subject to corporate or regulatory policies that require encryption of data and metadata at rest, we recommend creating an encrypted file system\.

## Encrypting a File System Using the Console<a name="encrypt-console"></a>

You can choose to enable encryption for a file system when you create it\. The following procedure describes how to enable encryption for a new file system when you create it from the console\.

**To encrypt a new file system on the console**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create file system** to open the file system creation wizard\.

1. For **Step 1: Configure file system access**, choose your VPC, create your mount targets, and then choose **Next Step**\.

1. For **Step 2: Configure optional settings**, add any tags, choose your performance mode, check the box to encrypt your file system, and then choose **Next Step**\.

1. For **Step 3: Review and create**, review your settings, and choose **Create File System**\.

You now have a new encrypted file system\.

## How Encryption Works with Amazon EFS<a name="howencrypt"></a>

In an encrypted file system, data and metadata are automatically encrypted before being written to the file system\. Similarly, as data and metadata are read, they are automatically decrypted before being presented to the application\. These processes are handled transparently by Amazon EFS, so you don’t have to modify your applications\.

Amazon EFS uses an industry\-standard AES\-256 encryption algorithm to encrypt EFS data and metadata\. For more information, see [Cryptography Basics](http://docs.aws.amazon.com/kms/latest/developerguide/crypto-intro.html) in the *AWS Key Management Service Developer Guide*\.

### How Amazon EFS Uses AWS KMS<a name="EFSKMS"></a>

Amazon EFS integrates with AWS Key Management Service \(AWS KMS\) for key management\. Amazon EFS uses customer master keys \(CMKs\) to encrypt your file system in the following way:

+ **Encrypting metadata** – An EFS\-managed key is used to encrypt and decrypt file system metadata \(that is, file names, directory names, and directory contents\)\.

+ **Encrypting file data** – You choose the CMK used to encrypt and decrypt file data \(that is, the contents of your files\)\. You can enable, disable, or revoke grants on this CMK\. This CMK can be one of the two following types:

  + **AWS\-managed CMK** – This is the default CMK, and it's free to use\.

  + **Customer\-managed CMK** – This is the most flexible master key to use, because you can configure its key policies and grants for multiple users or services\. For more information on creating CMKs, see [Creating Keys](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the* AWS Key Management Service Developer Guide\.*

    If you use a customer\-managed CMK as your master key for file data encryption and decryption, you can enable key rotation\. When you enable key rotation, AWS KMS automatically rotates your key once per year\. Additionally, with a customer\-managed CMK, you can choose when to disable, re\-enable, delete, or revoke access to your CMK at any time\. For more information, see [Disabling, Deleting, or Revoking Access to the CMK for a File System](managing.md#disable-efs-cmk)\.

Data encryption and decryption are handled transparently\. However, AWS account IDs specific to Amazon EFS will appear in your AWS CloudTrail logs related to AWS KMS actions\. For more information, see [Amazon EFS Log File Entries for Encrypted File Systems](logging-using-cloudtrail.md#efs-encryption-cloudtrail)\.

 

### Amazon EFS Key Policies for AWS KMS<a name="EFSKMSPolicy"></a>

Key policies are the primary way to control access to CMKs\. For more information on key policies, see [Using Key Policies in AWS KMS](http://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide\. *The following list describes all the AWS KMS\-related permissions supported by Amazon EFS for encrypted file systems:

+ **kms:Encrypt** – \(Optional\) Encrypts plaintext into ciphertext\. This permission is included in the default key policy\.

+ **kms:Decrypt** – \(Required\) Decrypts ciphertext\. Ciphertext is plaintext that has been previously encrypted\. This permission is included in the default key policy\.

+ **kms:ReEncrypt** – \(Optional\) Encrypts data on the server side with a new customer master key \(CMK\), without exposing the plaintext of the data on the client side\. The data is first decrypted and then re\-encrypted\. This permission is included in the default key policy\.

+ **kms:GenerateDataKeyWithoutPlaintext** – \(Required\) Returns a data encryption key encrypted under a CMK\. This permission is included in the default key policy under **kms:GenerateDataKey\***\.

+ **kms:CreateGrant** – \(Required\) Adds a grant to a key to specify who can use the key and under what conditions\. Grants are alternate permission mechanisms to key policies\. For more information on grants, see [Using Grants](http://docs.aws.amazon.com/kms/latest/developerguide/grants.html) in the *AWS Key Management Service Developer Guide\.* This permission is included in the default key policy\.

+ **kms:DescribeKey** – \(Required\) Provides detailed information about the specified customer master key\. This permission is included in the default key policy\.

+ **kms:ListAliases** – \(Optional\) Lists all of the key aliases in the account\. When you use the console to create an encrypted file system, this permission populates the **Select KMS master key** list\. We recommend using this permission to provide the best user experience\. This permission is included in the default key policy\.

## Related Topics<a name="relatedencrypt"></a>

For more information on encryption with Amazon EFS, see these related topics:

+ [Creating Resources for Amazon EFS](creating-using.md)

+ [Managing Access to Encrypted File Systems](managing.md#managing-encrypt)

+ [Amazon EFS Performance Tips](performance.md#performance-tips)

+ [Amazon EFS API Permissions: Actions, Resources, and Conditions Reference](efs-api-permissions-ref.md)

+ [Amazon EFS Log File Entries for Encrypted File Systems](logging-using-cloudtrail.md#efs-encryption-cloudtrail)

+ [Troubleshooting Encrypted File Systems](troubleshooting.md#troubleshooting-efs-encryption)