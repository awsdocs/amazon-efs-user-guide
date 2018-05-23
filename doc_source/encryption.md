# Encrypting Data and Metadata in EFS<a name="encryption"></a>

Amazon EFS supports two forms of encryption for file systems, encryption of data in transit and encryption at rest\. You can enable encryption of data at rest when creating an Amazon EFS file system\. You can enable encryption of data in transit when you mount the file system\.

## When to Use Encryption<a name="whenencrypt"></a>

If your organization is subject to corporate or regulatory policies that require encryption of data and metadata at rest, we recommend creating an encrypted file system mounting your file system using encryption of data in transit\.

## Encrypting Data in Transit<a name="encryption-in-transit"></a>

You can encrypt data in transit using an Amazon EFS file system, without needing to modify your applications\.

### Encrypting Data in Transit with TLS<a name="encrypt-mount"></a>

Enabling encryption of data in transit for your Amazon EFS file system is done by enabling Transport Layer Security \(TLS\) when you mount your file system using the Amazon EFS mount helper\. For more information, see [Mounting with the EFS Mount Helper](mounting-fs.md#mounting-fs-mount-helper)\.

When encryption of data in transit is declared as a mount option for your Amazon EFS file system, the mount helper initializes a client stunnel process\. Stunnel is an open source multipurpose network relay\. The client stunnel process listens on a local port for inbound traffic, and the mount helper redirects NFS client traffic to this local port\. The mount helper uses a Transport Layer Security \(TLS\) version 1\.2 to communicate with your file system\.

**Mounting your Amazon EFS file system with the mount helper with encryption of data in transit enabled**

1. Access the terminal for your instance through Secure Shell \(SSH\), and log in with the appropriate user name\. For more information on how to do this, see [Connecting to Your Linux Instance Using SSH](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the Amazon EC2 User Guide for Linux Instances\.

1. Run the following command to mount your file system\.

   ```
   sudo mount -t efs  -o tls fs-12345678:/ /mnt/efs
   ```

### How Encrypting in Transit Works<a name="how-encrypt-transit"></a>

Encryption of data in transit is enabled by connecting to Amazon EFS using TLS\. We recommend using the mount helper because it's the simplest option\.

If you're not using the mount helper, you can still enable encryption of data in transit\. At a high level, the following are the steps to do so\.

**To enable encryption of data in transit without the mount helper**

1. Download and install stunnel, and note the port that the application is listening on\.

1. Run stunnel to connect to your Amazon EFS file system on port 2049 using TLS\.

1. Using the NFS client, mount `localhost:port`, where `port` is the port that you noted in the first step\.

Because encryption of data in transit is configured on a per\-connection basis, each configured mount has a dedicated stunnel process running on the instance\. By default, the stunnel process used by the mount helper listens on local ports 20049 and 20449, and it connects to Amazon EFS on port 2049\.

**Note**  
By default, when using the Amazon EFS mount helper with TLS, it enforces the use of the Online Certificate Status Protocol \(OCSP\) and certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Note that some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux versions, mounting an Amazon EFS file system using TLS fails\.  
After you've installed the amazon\-efs\-utils package, to upgrade your system's version of stunnel, see [Upgrading Stunnel](using-amazon-efs-utils.md#upgrading-stunnel)\.  
For issues with encryption, see [ Troubleshooting Encryption  Following, you can find information about troubleshooting encryption issues for Amazon EFS\.  Mounting with Encryption of Data in Transit Fails  By default, when using the Amazon EFS mount helper with TLS, it enforces use of the Online Certificate Status Protocol \(OCSP\) and certificate hostname checking\. If your system doesn't support either of these features \(for example, when using Red Hat Enterprise Linux or CentOS\), mounting an EFS file system using TLS fails\.  Action to Take  We recommend upgrading the version of stunnel on your client to support these features\. For more information, see [Upgrading Stunnel](using-amazon-efs-utils.md#upgrading-stunnel)\.    Mounting with Encryption of Data in Transit is Interrupted  It's possible, however unlikely, that your encrypted connection to your Amazon EFS file system can hang or be interrupted by client\-side events\.  Action to Take If your connection to your Amazon EFS file system with encryption of data in transit is interrupted, take the following steps:    Ensure that the stunnel service is running on the client\.   Confirm that the watchdog application `amazon-efs-mount-watchdog` is running on the client\. You can find out whether this application is running with the following command: 

   ```
   ps aux | grep [a]mazon-efs-mount-watchdog
   ```   Check your support logs\. For more information, see [Getting Support Logs](using-amazon-efs-utils.md#mount-helper-logs)\.   Optionally, you can enable your stunnel logs and check the information in those as well\. You can change the configuration of your logs in `/etc/amazon/efs/amazon-efs-utils.conf` to enable the stunnel logs\. However, doing so requires unmounting and then remounting the file system with the mount helper for the changes to take effect\.  Enabling the stunnel logs can use up a nontrivial amount of space on your file system\.    If the interruptions continue, contact AWS Support\.   Encrypted\-at\-Rest File System Can't Be Created  You've tried to create a new encrypted\-at\-rest file system\. However, you get an error message saying that AWS KMS is unavailable\.  Action to Take This error can occur in the rare case that AWS KMS becomes temporarily unavailable in your AWS Region\. If this happens, wait until AWS KMS returns to full availability, and then try again to create the file system\.     Unusable Encrypted File System  An encrypted file system consistently returns NFS server errors\. These errors can occur when EFS can't retrieve your master key from AWS KMS for one of the following reasons:   The key was disabled\.   The key was deleted\.   Permission for Amazon EFS to use the key was revoked\.   AWS KMS is temporarily unavailable\.    Action to Take First, confirm that the AWS KMS key is enabled\. You can do so by viewing the keys in the console\. For more information, see [Viewing Keys](http://docs.aws.amazon.com/kms/latest/developerguide/viewing-keys.html) in the *AWS Key Management Service Developer Guide*\.  If the key is not enabled, enable it\. For more information, see [Enabling and Disabling Keys](http://docs.aws.amazon.com/kms/latest/developerguide/enabling-keys.html) in the *AWS Key Management Service Developer Guide*\. If the key is pending deletion, then this status disables the key\. You can cancel the deletion, and re\-enable the key\. For more information, see [Scheduling and Canceling Key Deletion](http://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html#deleting-keys-scheduling-key-deletion) in the *AWS Key Management Service Developer Guide*\. If the key is enabled, and you're still experiencing an issue, or if you encounter an issue re\-enabling your key, contact AWS Support\.  ](troubleshooting-efs-encryption.md)\.

When using encryption of data in transit, your NFS client setup is changed\. When you inspect your actively mounted file systems, you see one mounted to 127\.0\.0\.1, or localhost, as in the following example\.

```
$ mount | column -t
127.0.0.1:/  on  /home/ec2-user/efs        type  nfs4         (rw,relatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,port=20127,timeo=600,retrans=2,sec=sys,clientaddr=127.0.0.1,local_lock=none,addr=127.0.0.1)
```

When mounting with TLS and the Amazon EFS mount helper, you are reconfiguring your NFS client to mount to a local port\. The mount helper starts a client stunnel process that is listening on this local port, and stunnel is opening an encrypted connection to EFS using TLS\. The EFS mount helper is responsible for setting up and maintaining this encrypted connection and associated configuration\.

To determine which Amazon EFS file system ID corresponds to which local mount point, you can use the following command\. Remember to replace *efs\-mount\-point* with the local path where you’ve mounted your file system\.

```
grep -E "Successfully mounted.*efs-mount-point" /var/log/amazon/efs/mount.log | tail -1
```

When you use the mount helper for encryption of data in transit, it also creates a process called `amazon-efs-mount-watchdog`\. This process ensures that each mount's stunnel process is running, and stops the stunnel when the Amazon EFS file system is unmounted\. If for some reason a stunnel process is terminated unexpectedly, the watchdog process restarts it\.

## Encrypting Data at Rest<a name="encryption-at-rest"></a>

As with unencrypted file systems, you can create encrypted file systems through the AWS Management Console, the AWS CLI, or programmatically through the Amazon EFS API or one of the AWS SDKs\. Your organization might require the encryption of all data that meets a specific classification or is associated with a particular application, workload, or environment\. 

You can enforce data encryption policies for Amazon EFS file systems by using Amazon CloudWatch and AWS CloudTrail to detect the creation of a file system and verify that encryption is enabled\. For more information, see [Walkthrough 6: Enforcing Encryption on an Amazon EFS File System at Rest](efs-enforce-encryption.md)\.

**Note**  
The AWS key management infrastructure uses Federal Information Processing Standards \(FIPS\) 140\-2 approved cryptographic algorithms\. The infrastructure is consistent with National Institute of Standards and Technology \(NIST\) 800\-57 recommendations\.

### Encrypting a File System at Rest Using the Console<a name="encrypt-console"></a>

You can choose to enable encryption at rest for a file system when you create it\. The following procedure describes how to enable encryption for a new file system when you create it from the console\.

**To encrypt a new file system on the console**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **Create file system** to open the file system creation wizard\.

1. For **Step 1: Configure file system access**, choose your VPC, create your mount targets, and then choose **Next Step**\.

1. For **Step 2: Configure optional settings**, add any tags, choose your performance mode, check the box to encrypt your file system, and then choose **Next Step**\.

1. For **Step 3: Review and create**, review your settings, and choose **Create File System**\.

You now have a new encrypted\-at\-rest file system\.

### How Encryption at Rest Works<a name="howencrypt"></a>

In an encrypted\-at\-rest file system, data and metadata are automatically encrypted before being written to the file system\. Similarly, as data and metadata are read, they are automatically decrypted before being presented to the application\. These processes are handled transparently by Amazon EFS, so you don’t have to modify your applications\.

Amazon EFS uses an industry\-standard AES\-256 encryption algorithm to encrypt EFS data and metadata at rest\. For more information, see [Cryptography Basics](http://docs.aws.amazon.com/kms/latest/developerguide/crypto-intro.html) in the *AWS Key Management Service Developer Guide*\.

#### How Amazon EFS Uses AWS KMS<a name="EFSKMS"></a>

Amazon EFS integrates with AWS Key Management Service \(AWS KMS\) for key management\. Amazon EFS uses customer master keys \(CMKs\) to encrypt your file system in the following way:
+ **Encrypting metadata at rest** – An EFS\-managed key is used to encrypt and decrypt file system metadata \(that is, file names, directory names, and directory contents\)\.
+ **Encrypting file data at rest** – You choose the CMK used to encrypt and decrypt file data \(that is, the contents of your files\)\. You can enable, disable, or revoke grants on this CMK\. This CMK can be one of the two following types:
  + **AWS\-managed CMK** – This is the default CMK, and it's free to use\.
  + **Customer\-managed CMK** – This is the most flexible master key to use, because you can configure its key policies and grants for multiple users or services\. For more information on creating CMKs, see [Creating Keys](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the* AWS Key Management Service Developer Guide\.*

    If you use a customer\-managed CMK as your master key for file data encryption and decryption, you can enable key rotation\. When you enable key rotation, AWS KMS automatically rotates your key once per year\. Additionally, with a customer\-managed CMK, you can choose when to disable, re\-enable, delete, or revoke access to your CMK at any time\. For more information, see [Disabling, Deleting, or Revoking Access to the CMK for a File System](managing.md#disable-efs-cmk)\.

Data encryption and decryption at rest are handled transparently\. However, AWS account IDs specific to Amazon EFS appear in your AWS CloudTrail logs related to AWS KMS actions\. For more information, see [Amazon EFS Log File Entries for Encrypted\-at\-Rest File Systems](logging-using-cloudtrail.md#efs-encryption-cloudtrail)\.

#### Amazon EFS Key Policies for AWS KMS<a name="EFSKMSPolicy"></a>

Key policies are the primary way to control access to CMKs\. For more information on key policies, see [Using Key Policies in AWS KMS](http://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide\. *The following list describes all the AWS KMS\-related permissions supported by Amazon EFS for encrypted at rest file systems:
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
+ [Amazon EFS Log File Entries for Encrypted\-at\-Rest File Systems](logging-using-cloudtrail.md#efs-encryption-cloudtrail)
+ [Troubleshooting Encryption](troubleshooting-efs-encryption.md)