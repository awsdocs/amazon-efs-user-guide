# FileSystemDescription<a name="API_FileSystemDescription"></a>

Description of the file system\.

## Contents<a name="API_FileSystemDescription_Contents"></a>

 **CreationTime**   <a name="efs-Type-FileSystemDescription-CreationTime"></a>
Time that the file system was created, in seconds \(since 1970\-01\-01T00:00:00Z\)\.  
Type: Timestamp  
Required: Yes

 **CreationToken**   <a name="efs-Type-FileSystemDescription-CreationToken"></a>
Opaque string specified in the request\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Required: Yes

 **Encrypted**   <a name="efs-Type-FileSystemDescription-Encrypted"></a>
A Boolean value that, if true, indicates that the file system is encrypted\.  
Type: Boolean  
Required: No

 **FileSystemId**   <a name="efs-Type-FileSystemDescription-FileSystemId"></a>
ID of the file system, assigned by Amazon EFS\.  
Type: String  
Required: Yes

 **KmsKeyId**   <a name="efs-Type-FileSystemDescription-KmsKeyId"></a>
The ID of an AWS Key Management Service \(AWS KMS\) customer master key \(CMK\) that was used to protect the encrypted file system\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Required: No

 **LifeCycleState**   <a name="efs-Type-FileSystemDescription-LifeCycleState"></a>
Lifecycle phase of the file system\.  
Type: String  
Valid Values:` creating | available | deleting | deleted`   
Required: Yes

 **Name**   <a name="efs-Type-FileSystemDescription-Name"></a>
You can add tags to a file system, including a `Name` tag\. For more information, see [CreateTags](API_CreateTags.md)\. If the file system has a `Name` tag, Amazon EFS returns the value in this field\.   
Type: String  
Length Constraints: Maximum length of 256\.  
Required: No

 **NumberOfMountTargets**   <a name="efs-Type-FileSystemDescription-NumberOfMountTargets"></a>
Current number of mount targets that the file system has\. For more information, see [CreateMountTarget](API_CreateMountTarget.md)\.  
Type: Integer  
Valid Range: Minimum value of 0\.  
Required: Yes

 **OwnerId**   <a name="efs-Type-FileSystemDescription-OwnerId"></a>
AWS account that created the file system\. If the file system was created by an IAM user, the parent account to which the user belongs is the owner\.  
Type: String  
Required: Yes

 **PerformanceMode**   <a name="efs-Type-FileSystemDescription-PerformanceMode"></a>
The `PerformanceMode` of the file system\.  
Type: String  
Valid Values:` generalPurpose | maxIO`   
Required: Yes

 **SizeInBytes**   <a name="efs-Type-FileSystemDescription-SizeInBytes"></a>
Latest known metered size \(in bytes\) of data stored in the file system, in bytes, in its `Value` field, and the time at which that size was determined in its `Timestamp` field\. The `Timestamp` value is the integer number of seconds since 1970\-01\-01T00:00:00Z\. Note that the value does not represent the size of a consistent snapshot of the file system, but it is eventually consistent when there are no writes to the file system\. That is, the value will represent actual size only if the file system is not modified for a period longer than a couple of hours\. Otherwise, the value is not the exact size the file system was at any instant in time\.   
Type: [FileSystemSize](API_FileSystemSize.md) object  
Required: Yes

## See Also<a name="API_FileSystemDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/FileSystemDescription) 
+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/FileSystemDescription) 
+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/FileSystemDescription) 
+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/elasticfilesystem-2015-02-01/FileSystemDescription) 