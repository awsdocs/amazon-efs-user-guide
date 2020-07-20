# AccessPointDescription<a name="API_AccessPointDescription"></a>

Provides a description of an EFS file system access point\.

## Contents<a name="API_AccessPointDescription_Contents"></a>

 **AccessPointArn**   <a name="efs-Type-AccessPointDescription-AccessPointArn"></a>
The unique Amazon Resource Name \(ARN\) associated with the access point\.  
Type: String  
Required: No

 **AccessPointId**   <a name="efs-Type-AccessPointDescription-AccessPointId"></a>
The ID of the access point, assigned by Amazon EFS\.  
Type: String  
Required: No

 **ClientToken**   <a name="efs-Type-AccessPointDescription-ClientToken"></a>
The opaque string specified in the request to ensure idempotent creation\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Required: No

 **FileSystemId**   <a name="efs-Type-AccessPointDescription-FileSystemId"></a>
The ID of the EFS file system that the access point applies to\.  
Type: String  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: No

 **LifeCycleState**   <a name="efs-Type-AccessPointDescription-LifeCycleState"></a>
Identifies the lifecycle phase of the access point\.  
Type: String  
Valid Values:` creating | available | updating | deleting | deleted`   
Required: No

 **Name**   <a name="efs-Type-AccessPointDescription-Name"></a>
The name of the access point\. This is the value of the `Name` tag\.  
Type: String  
Required: No

 **OwnerId**   <a name="efs-Type-AccessPointDescription-OwnerId"></a>
Identified the AWS account that owns the access point resource\.  
Type: String  
Length Constraints: Maximum length of 14\.  
Pattern: `^(\d{12})|(\d{4}-\d{4}-\d{4})$`   
Required: No

 **PosixUser**   <a name="efs-Type-AccessPointDescription-PosixUser"></a>
The full POSIX identity, including the user ID, group ID, and secondary group IDs on the access point that is used for all file operations by NFS clients using the access point\.  
Type: [PosixUser](API_PosixUser.md) object  
Required: No

 **RootDirectory**   <a name="efs-Type-AccessPointDescription-RootDirectory"></a>
The directory on the Amazon EFS file system that the access point exposes as the root directory to NFS clients using the access point\.  
Type: [RootDirectory](API_RootDirectory.md) object  
Required: No

 **Tags**   <a name="efs-Type-AccessPointDescription-Tags"></a>
The tags associated with the access point, presented as an array of Tag objects\.  
Type: Array of [Tag](API_Tag.md) objects  
Required: No

## See Also<a name="API_AccessPointDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/AccessPointDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/AccessPointDescription) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/AccessPointDescription) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/AccessPointDescription) 