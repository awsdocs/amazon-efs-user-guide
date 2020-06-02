# PosixUser<a name="API_PosixUser"></a>

The full POSIX identity, including the user ID, group ID, and any secondary group IDs, on the access point that is used for all file system operations performed by NFS clients using the access point\.

## Contents<a name="API_PosixUser_Contents"></a>

 **Gid**   <a name="efs-Type-PosixUser-Gid"></a>
The POSIX group ID used for all file system operations using this access point\.  
Type: Long  
Valid Range: Minimum value of 0\. Maximum value of 4294967295\.  
Required: Yes

 **SecondaryGids**   <a name="efs-Type-PosixUser-SecondaryGids"></a>
Secondary POSIX group IDs used for all file system operations using this access point\.  
Type: Array of longs  
Array Members: Minimum number of 0 items\. Maximum number of 16 items\.  
Valid Range: Minimum value of 0\. Maximum value of 4294967295\.  
Required: No

 **Uid**   <a name="efs-Type-PosixUser-Uid"></a>
The POSIX user ID used for all file system operations using this access point\.  
Type: Long  
Valid Range: Minimum value of 0\. Maximum value of 4294967295\.  
Required: Yes

## See Also<a name="API_PosixUser_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/PosixUser) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/PosixUser) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/PosixUser) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/PosixUser) 