# Encrypting Data at Rest<a name="encryption-at-rest"></a>

As with unencrypted file systems, you can create encrypted file systems through the AWS Management Console, the AWS CLI, or programmatically through the Amazon EFS API or one of the AWS SDKs\. Your organization might require the encryption of all data that meets a specific classification or is associated with a particular application, workload, or environment\. 

You can monitor whether encryption at rest is being used for Amazon EFS file systems by using Amazon CloudWatch and AWS CloudTrail to detect the creation of a file system and verify that encryption is enabled\. For more information, see [Walkthrough: Enforcing Encryption on an Amazon EFS File System at Rest](efs-enforce-encryption.md)\. You can enforce encryption in transit on file systems using a file system policy\. For more information, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\.

**Note**  
The AWS key management infrastructure uses Federal Information Processing Standards \(FIPS\) 140\-2 approved cryptographic algorithms\. The infrastructure is consistent with National Institute of Standards and Technology \(NIST\) 800\-57 recommendations\.

## Encrypting a File System at Rest Using the Console<a name="encrypt-console"></a>

You can choose to enable encryption at rest for a file system when you create it\. The following procedure describes how to enable encryption for a new file system when you create it from the console\.

**To encrypt a new file system on the console**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create file system** to open the file system creation wizard\.

1. For **Step 1: Configure network access**, choose your VPC, create your mount targets, and then choose **Next Step**\.

1. For **Step 2: Configure file system settings**, add any tags, enable Lifecycle management, choose your throughput and performance modes, and Enable encryption to encrypt your file system, and then choose **Next Step**\.

1. For **Step 3\. Configure client access**, you can choose a preconfigured **File system policy**, or use the JSON editor in the **\{\} JSON** tab to create a more advanced policy\. For more information, see [Using IAM to Control NFS Access to Amazon EFS](iam-access-control-nfs-efs.md)\. Choose **Next Step**\.

1. For **Step 4: Review and create**, review your settings, and choose **Create File System**\.

You now have a new encrypted\-at\-rest file system\.

## How Encryption at Rest Works<a name="howencrypt"></a>

In an encrypted file system, data and metadata are automatically encrypted before being written to the file system\. Similarly, as data and metadata are read, they are automatically decrypted before being presented to the application\. These processes are handled transparently by Amazon EFS, so you don't have to modify your applications\.

Amazon EFS uses industry\-standard AES\-256 encryption algorithm to encrypt EFS data and metadata at rest\. For more information, see [Cryptography Basics](https://docs.aws.amazon.com/kms/latest/developerguide/crypto-intro.html) in the *AWS Key Management Service Developer Guide*\.

### How Amazon EFS Uses AWS KMS<a name="EFSKMS"></a>

Amazon EFS integrates with AWS Key Management Service \(AWS KMS\) for key management\. Amazon EFS uses customer master keys \(CMKs\) to encrypt your file system in the following way:
+ **Encrypting metadata at rest** – Amazon EFS uses the AWS managed CMK for Amazon EFS, `aws/elasticfilesystem`, to encrypt and decrypt file system metadata \(that is, file names, directory names, and directory contents\)\.
+ **Encrypting file data at rest** – You choose the CMK used to encrypt and decrypt file data \(that is, the contents of your files\)\. You can enable, disable, or revoke grants on this CMK\. This CMK can be one of the two following types:
  + **AWS managed CMK for Amazon EFS** – This is the default CMK, `aws/elasticfilesystem`\. You're not charged to create and store a CMK, but there are usage charges\. To learn more, see [AWS Key Management Service pricing](https://aws.amazon.com/kms/pricing/)\.
  + **Customer\-managed CMK** – This is the most flexible master key to use, because you can configure its key policies and grants for multiple users or services\. For more information on creating CMKs, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the* AWS Key Management Service Developer Guide\.*

    If you use a customer\-managed CMK as your master key for file data encryption and decryption, you can enable key rotation\. When you enable key rotation, AWS KMS automatically rotates your key once per year\. Additionally, with a customer\-managed CMK, you can choose when to disable, re\-enable, delete, or revoke access to your CMK at any time\. For more information, see [Disabling, deleting, or revoking access to the CMK for a file system](managing-encrypt.md#disable-efs-cmk)\.

**Important**  
Amazon EFS accepts only symmetric CMKs\. You cannot use asymmetric CMKs with Amazon EFS\.

Data encryption and decryption at rest are handled transparently\. However, AWS account IDs specific to Amazon EFS appear in your AWS CloudTrail logs related to AWS KMS actions\. For more information, see [Amazon EFS Log File Entries for Encrypted\-at\-Rest File Systems](logging-using-cloudtrail.md#efs-encryption-cloudtrail)\.

### Amazon EFS Key Policies for AWS KMS<a name="EFSKMSPolicy"></a>

Key policies are the primary way to control access to CMKs\. For more information on key policies, see [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide\. *The following list describes all the AWS KMS–related permissions supported by Amazon EFS for encrypted at rest file systems:
+ **kms:Encrypt** – \(Optional\) Encrypts plaintext into ciphertext\. This permission is included in the default key policy\.
+ **kms:Decrypt** – \(Required\) Decrypts ciphertext\. Ciphertext is plaintext that has been previously encrypted\. This permission is included in the default key policy\.
+ **kms:ReEncrypt** – \(Optional\) Encrypts data on the server side with a new customer master key \(CMK\), without exposing the plaintext of the data on the client side\. The data is first decrypted and then re\-encrypted\. This permission is included in the default key policy\.
+ **kms:GenerateDataKeyWithoutPlaintext** – \(Required\) Returns a data encryption key encrypted under a CMK\. This permission is included in the default key policy under **kms:GenerateDataKey\***\.
+ **kms:CreateGrant** – \(Required\) Adds a grant to a key to specify who can use the key and under what conditions\. Grants are alternate permission mechanisms to key policies\. For more information on grants, see [Using Grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) in the *AWS Key Management Service Developer Guide\.* This permission is included in the default key policy\.
+ **kms:DescribeKey** – \(Required\) Provides detailed information about the specified customer master key\. This permission is included in the default key policy\.
+ **kms:ListAliases** – \(Optional\) Lists all of the key aliases in the account\. When you use the console to create an encrypted file system, this permission populates the **Select KMS master key** list\. We recommend using this permission to provide the best user experience\. This permission is included in the default key policy\.