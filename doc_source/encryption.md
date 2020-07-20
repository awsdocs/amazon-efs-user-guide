# Data Encryption in EFS<a name="encryption"></a>

Amazon EFS supports two forms of encryption for file systems, encryption of data in transit and encryption at rest\. You can enable encryption of data at rest when creating an Amazon EFS file system\. You can enable encryption of data in transit when you mount the file system\.

## When to Use Encryption<a name="whenencrypt"></a>

If your organization is subject to corporate or regulatory policies that require encryption of data and metadata at rest, we recommend creating an encrypted file system mounting your file system using encryption of data in transit\.

### Related Topics<a name="relatedencrypt"></a>

For more information on encryption with Amazon EFS, see these related topics:
+ [Working with Amazon EFS resources](creating-using.md)
+ [Managing access to encrypted file systems](managing-encrypt.md)
+ [Amazon EFS Performance Tips](performance-tips.md)
+ [Amazon EFS API Permissions: Actions, Resources, and Conditions Reference](access-control-managing-permissions.md#efs-api-permissions-ref)
+ [Amazon EFS Log File Entries for Encrypted\-at\-Rest File Systems](logging-using-cloudtrail.md#efs-encryption-cloudtrail)
+ [Troubleshooting Encryption](troubleshooting-efs-encryption.md)

**Topics**
+ [When to Use Encryption](#whenencrypt)
+ [Encrypting Data at Rest](encryption-at-rest.md)
+ [Encrypting Data in Transit](encryption-in-transit.md)