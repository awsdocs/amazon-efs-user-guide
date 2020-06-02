# CreationInfo<a name="API_CreationInfo"></a>

Required if the `RootDirectory` > `Path` specified does not exist\. Specifies the POSIX IDs and permissions to apply to the access point's `RootDirectory` > `Path`\. If the access point root directory does not exist, EFS creates it with these settings when a client connects to the access point\. When specifying `CreationInfo`, you must include values for all properties\. 

**Important**  
If you do not provide `CreationInfo` and the specified `RootDirectory` does not exist, attempts to mount the file system using the access point will fail\.

## Contents<a name="API_CreationInfo_Contents"></a>

 **OwnerGid**   <a name="efs-Type-CreationInfo-OwnerGid"></a>
Specifies the POSIX group ID to apply to the `RootDirectory`\. Accepts values from 0 to 2^32 \(4294967295\)\.  
Type: Long  
Valid Range: Minimum value of 0\. Maximum value of 4294967295\.  
Required: Yes

 **OwnerUid**   <a name="efs-Type-CreationInfo-OwnerUid"></a>
Specifies the POSIX user ID to apply to the `RootDirectory`\. Accepts values from 0 to 2^32 \(4294967295\)\.  
Type: Long  
Valid Range: Minimum value of 0\. Maximum value of 4294967295\.  
Required: Yes

 **Permissions**   <a name="efs-Type-CreationInfo-Permissions"></a>
Specifies the POSIX permissions to apply to the `RootDirectory`, in the format of an octal number representing the file's mode bits\.  
Type: String  
Pattern: `^[0-7]{3,4}$`   
Required: Yes

## See Also<a name="API_CreationInfo_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/CreationInfo) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/CreationInfo) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/CreationInfo) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/CreationInfo) 