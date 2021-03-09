# Encrypting data at rest<a name="encryption-at-rest"></a>

As with unencrypted file systems, you can create encrypted file systems using the AWS Management Console, the AWS CLI, or programmatically through the Amazon EFS API or one of the AWS SDKs\. Your organization might require the encryption of all data that meets a specific classification or is associated with a particular application, workload, or environment\. 

**Note**  
The AWS key management infrastructure uses Federal Information Processing Standards \(FIPS\) 140\-2 approved cryptographic algorithms\. The infrastructure is consistent with National Institute of Standards and Technology \(NIST\) 800\-57 recommendations\.

## Enforcing the creation of Amazon EFS file systems encrypted at rest<a name="enforce-encryption-at-rest"></a>

You can use the `elasticfilesystem:Encrypted` IAM condition key in AWS Identity and Access Management \(IAM\) identity\-based policies to control whether users can create Amazon EFS file systems that are encrypted at rest\. For more information about using the condition key, see [Using IAM to Enforce Creating Encrypted File Systems](using-iam-to-enforce-encryption-at-rest.md)\. 

You can also define service control policies \(SCPs\) inside AWS Organizations to enforce EFS encryption for all AWS accounts in your organization\. For more information about service control policies in AWS Organizations, see [Service control policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html#orgs_manage_policies_scp) in the *AWS Organizations User Guide*\.

## Encrypting a file system at rest using the console<a name="encrypt-console"></a>

When you create a new file system using the Amazon EFS console, encryption at rest is enabled by default\. The following procedure describes how to enable encryption for a new file system when you create it from the console\.

**To encrypt a new file system using the EFS console**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create file system** to open the **Create file system** dialog box\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-create-file-system.png)

1. \(Optional\) Enter a **Name** for your file system\.

1. For **Virtual Private Cloud \(VPC\)**, choose your VPC, or keep it set to your default VPC\.

1. Choose **Create** to create a file system that uses the following service recommended settings:
   + Encryption of data at rest enabled using your default key for Amazon EFS \(`aws/elasticfilesystem`\)\.
   + Automatic backups turned on – For more information, see [Using AWS Backup with Amazon EFS](awsbackup.md)\.
   + Mount targets – Amazon EFS creates mount targets with the following settings:
     + Located in each Availability Zone in the Region where the file system is created\.
     + Located in the default subnets of the VPC that you selected\.
     + Use the VPC's default security group\. You can manage security groups after the file system is created\.

     For more information, see [Managing file system network accessibility](manage-fs-access.md)\.
   + General Purpose performance mode – For more information, see [Performance modes](performance.md#performancemodes)\.
   + Bursting throughput mode – For more information, see [Throughput modes](performance.md#throughput-modes)\.
   + Lifecycle management enabled with a 30\-day policy – For more information, see [Amazon EFS lifecycle management](lifecycle-management-efs.md)\.

1. The **File systems** page appears with a banner across the top showing the status of the file system you created\. A link to access the file system details page appears in the banner when the file system becomes available\.  
![\[File system details page showing the newly created file system and its configuration details.\]](http://docs.aws.amazon.com/efs/latest/ug/images/console2-filesystemdetails.png)

You now have a new encrypted\-at\-rest file system\.

## How encryption at rest works<a name="howencrypt"></a>

In an encrypted file system, data and metadata are automatically encrypted before being written to the file system\. Similarly, as data and metadata are read, they are automatically decrypted before being presented to the application\. These processes are handled transparently by Amazon EFS, so you don't have to modify your applications\.

Amazon EFS uses industry\-standard AES\-256 encryption algorithm to encrypt EFS data and metadata at rest\. For more information, see [Cryptography Basics](https://docs.aws.amazon.com/kms/latest/developerguide/crypto-intro.html) in the *AWS Key Management Service Developer Guide*\.

### How Amazon EFS uses AWS KMS<a name="EFSKMS"></a>

Amazon EFS integrates with AWS Key Management Service \(AWS KMS\) for key management\. Amazon EFS uses customer master keys \(CMKs\) to encrypt your file system in the following way:
+ **Encrypting metadata at rest** – Amazon EFS uses the AWS managed CMK for Amazon EFS, `aws/elasticfilesystem`, to encrypt and decrypt file system metadata \(that is, file names, directory names, and directory contents\)\.
+ **Encrypting file data at rest** – You choose the CMK used to encrypt and decrypt file data \(that is, the contents of your files\)\. You can enable, disable, or revoke grants on this CMK\. This CMK can be one of the two following types:
  + **AWS managed CMK for Amazon EFS** – This is the default CMK, `aws/elasticfilesystem`\. You're not charged to create and store a CMK, but there are usage charges\. To learn more, see [AWS Key Management Service pricing](https://aws.amazon.com/kms/pricing/)\.
  + **Customer\-managed CMK** – This is the most flexible master key to use, because you can configure its key policies and grants for multiple users or services\. For more information on creating CMKs, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the* AWS Key Management Service Developer Guide\.*

    If you use a customer\-managed CMK as your master key for file data encryption and decryption, you can enable key rotation\. When you enable key rotation, AWS KMS automatically rotates your key once per year\. Additionally, with a customer\-managed CMK, you can choose when to disable, re\-enable, delete, or revoke access to your CMK at any time\. For more information, see [Disabling, deleting, or revoking access to the CMK for a file system](managing-encrypt.md#disable-efs-cmk)\.

**Important**  
Amazon EFS accepts only symmetric CMKs\. You cannot use asymmetric CMKs with Amazon EFS\.

Data encryption and decryption at rest are handled transparently\. However, AWS account IDs specific to Amazon EFS appear in your AWS CloudTrail logs related to AWS KMS actions\. For more information, see [Amazon EFS Log File Entries for Encrypted\-at\-Rest File Systems](logging-using-cloudtrail.md#efs-encryption-cloudtrail)\.

### Amazon EFS key policies for AWS KMS<a name="EFSKMSPolicy"></a>

Key policies are the primary way to control access to CMKs\. For more information on key policies, see [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide\. *The following list describes all the AWS KMS–related permissions supported by Amazon EFS for encrypted at rest file systems:
+ **kms:Encrypt** – \(Optional\) Encrypts plaintext into ciphertext\. This permission is included in the default key policy\.
+ **kms:Decrypt** – \(Required\) Decrypts ciphertext\. Ciphertext is plaintext that has been previously encrypted\. This permission is included in the default key policy\.
+ **kms:ReEncrypt** – \(Optional\) Encrypts data on the server side with a new customer master key \(CMK\), without exposing the plaintext of the data on the client side\. The data is first decrypted and then re\-encrypted\. This permission is included in the default key policy\.
+ **kms:GenerateDataKeyWithoutPlaintext** – \(Required\) Returns a data encryption key encrypted under a CMK\. This permission is included in the default key policy under **kms:GenerateDataKey\***\.
+ **kms:CreateGrant** – \(Required\) Adds a grant to a key to specify who can use the key and under what conditions\. Grants are alternate permission mechanisms to key policies\. For more information on grants, see [Using Grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) in the *AWS Key Management Service Developer Guide\.* This permission is included in the default key policy\.
+ **kms:DescribeKey** – \(Required\) Provides detailed information about the specified customer master key\. This permission is included in the default key policy\.
+ **kms:ListAliases** – \(Optional\) Lists all of the key aliases in the account\. When you use the console to create an encrypted file system, this permission populates the **Select KMS master key** list\. We recommend using this permission to provide the best user experience\. This permission is included in the default key policy\.