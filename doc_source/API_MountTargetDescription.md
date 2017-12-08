# MountTargetDescription<a name="API_MountTargetDescription"></a>

Provides a description of a mount target\.

## Contents<a name="API_MountTargetDescription_Contents"></a>

 **FileSystemId**   
ID of the file system for which the mount target is intended\.  
Type: String  
Required: Yes

 **IpAddress**   
Address at which the file system may be mounted via the mount target\.  
Type: String  
Required: No

 **LifeCycleState**   
Lifecycle state of the mount target\.  
Type: String  
Valid Values:` creating | available | deleting | deleted`   
Required: Yes

 **MountTargetId**   
System\-assigned mount target ID\.  
Type: String  
Required: Yes

 **NetworkInterfaceId**   
ID of the network interface that Amazon EFS created when it created the mount target\.  
Type: String  
Required: No

 **OwnerId**   
AWS account ID that owns the resource\.  
Type: String  
Required: No

 **SubnetId**   
ID of the mount target's subnet\.  
Type: String  
Required: Yes

## See Also<a name="API_MountTargetDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/MountTargetDescription) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/MountTargetDescription) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/MountTargetDescription) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/elasticfilesystem-2015-02-01/MountTargetDescription) 