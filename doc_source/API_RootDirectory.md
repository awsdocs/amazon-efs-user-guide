# RootDirectory<a name="API_RootDirectory"></a>

Specifies the directory on the Amazon EFS file system that the access point provides access to\. The access point exposes the specified file system path as the root directory of your file system to applications using the access point\. NFS clients using the access point can only access data in the access point's `RootDirectory` and it's subdirectories\.

## Contents<a name="API_RootDirectory_Contents"></a>

 **CreationInfo**   <a name="efs-Type-RootDirectory-CreationInfo"></a>
\(Optional\) Specifies the POSIX IDs and permissions to apply to the access point's `RootDirectory`\. If the `RootDirectory` > `Path` specified does not exist, EFS creates the root directory using the `CreationInfo` settings when a client connects to an access point\. When specifying the `CreationInfo`, you must provide values for all properties\.   
If you do not provide `CreationInfo` and the specified `RootDirectory` > `Path` does not exist, attempts to mount the file system using the access point will fail\.
Type: [CreationInfo](API_CreationInfo.md) object  
Required: No

 **Path**   <a name="efs-Type-RootDirectory-Path"></a>
Specifies the path on the EFS file system to expose as the root directory to NFS clients using the access point to access the EFS file system\. A path can have up to four subdirectories\. If the specified path does not exist, you are required to provide the `CreationInfo`\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 100\.  
Required: No

## See Also<a name="API_RootDirectory_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/RootDirectory) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/RootDirectory) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/RootDirectory) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/RootDirectory) 