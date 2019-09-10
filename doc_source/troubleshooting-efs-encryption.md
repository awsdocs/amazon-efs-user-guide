# Troubleshooting Encryption<a name="troubleshooting-efs-encryption"></a>

**Topics**
+ [Mounting with Encryption of Data in Transit Fails](#mounting-tls-fails)
+ [Mounting with Encryption of Data in Transit is Interrupted](#mounting-tls-interrupt)
+ [Encrypted\-at\-Rest File System Can't Be Created](#unable-to-encrypt)
+ [Unusable Encrypted File System](#unusable-encrypt)

## Mounting with Encryption of Data in Transit Fails<a name="mounting-tls-fails"></a>

By default, when you use the Amazon EFS mount helper with Transport Layer Security \(TLS\), it enforces hostname checking\. Some systems don't support this feature, such as when you use Red Hat Enterprise Linux or CentOS\. In these cases, mounting an EFS file system using TLS fails\.

**Action to Take**  
 We recommend that you upgrade the version of stunnel on your client to support hostname checking\. For more information, see [Upgrading Stunnel](using-amazon-efs-utils.md#upgrading-stunnel)\.

## Mounting with Encryption of Data in Transit is Interrupted<a name="mounting-tls-interrupt"></a>

It's possible, however unlikely, that your encrypted connection to your Amazon EFS file system can hang or be interrupted by client\-side events\.

**Action to Take**  
If your connection to your Amazon EFS file system with encryption of data in transit is interrupted, take the following steps:

1. Ensure that the stunnel service is running on the client\.

1. Confirm that the watchdog application `amazon-efs-mount-watchdog` is running on the client\. You can find out whether this application is running with the following command:

   ```
   ps aux | grep [a]mazon-efs-mount-watchdog
   ```

1. Check your support logs\. For more information, see [Getting Support Logs](efs-mount-helper.md#mount-helper-logs)\.

1. Optionally, you can enable your stunnel logs and check the information in those as well\. You can change the configuration of your logs in `/etc/amazon/efs/efs-utils.conf` to enable the stunnel logs\. However, doing so requires unmounting and then remounting the file system with the mount helper for the changes to take effect\.
**Important**  
Enabling the stunnel logs can use up a nontrivial amount of space on your file system\.

If the interruptions continue, contact AWS Support\.

## Encrypted\-at\-Rest File System Can't Be Created<a name="unable-to-encrypt"></a>

You've tried to create a new encrypted\-at\-rest file system\. However, you get an error message saying that AWS KMS is unavailable\.

**Action to Take**  
This error can occur in the rare case that AWS KMS becomes temporarily unavailable in your AWS Region\. If this happens, wait until AWS KMS returns to full availability, and then try again to create the file system\.

## Unusable Encrypted File System<a name="unusable-encrypt"></a>

An encrypted file system consistently returns NFS server errors\. These errors can occur when EFS can't retrieve your master key from AWS KMS for one of the following reasons:
+ The key was disabled\.
+ The key was deleted\.
+ Permission for Amazon EFS to use the key was revoked\.
+ AWS KMS is temporarily unavailable\.

**Action to Take**  
First, confirm that the AWS KMS key is enabled\. You can do so by viewing the keys in the console\. For more information, see [Viewing Keys](https://docs.aws.amazon.com/kms/latest/developerguide/viewing-keys.html) in the *AWS Key Management Service Developer Guide*\.

If the key is not enabled, enable it\. For more information, see [Enabling and Disabling Keys](https://docs.aws.amazon.com/kms/latest/developerguide/enabling-keys.html) in the *AWS Key Management Service Developer Guide*\.

If the key is pending deletion, then this status disables the key\. You can cancel the deletion, and re\-enable the key\. For more information, see [Scheduling and Canceling Key Deletion](https://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html#deleting-keys-scheduling-key-deletion) in the *AWS Key Management Service Developer Guide*\.

If the key is enabled, and you're still experiencing an issue, or if you encounter an issue re\-enabling your key, contact AWS Support\.