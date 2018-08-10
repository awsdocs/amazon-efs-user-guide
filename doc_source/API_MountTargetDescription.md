# MountTargetDescription<a name="API_MountTargetDescription"></a>

Provides a description of a mount target\.

## Contents<a name="API_MountTargetDescription_Contents"></a>

 **FileSystemId**   <a name="efs-Type-MountTargetDescription-FileSystemId"></a>
ID of the file system for which the mount target is intended\.  
Type: String  
Required: Yes

 **IpAddress**   <a name="efs-Type-MountTargetDescription-IpAddress"></a>
Address at which the file system can be mounted by using the mount target\.  
Type: String  
Required: No

 **LifeCycleState**   <a name="efs-Type-MountTargetDescription-LifeCycleState"></a>
Lifecycle state of the mount target\.  
Type: String  
Valid Values:` creating | available | updating | deleting | deleted`   
Required: Yes

 **MountTargetId**   <a name="efs-Type-MountTargetDescription-MountTargetId"></a>
System\-assigned mount target ID\.  
Type: String  
Required: Yes

 **NetworkInterfaceId**   <a name="efs-Type-MountTargetDescription-NetworkInterfaceId"></a>
ID of the network interface that Amazon EFS created when it created the mount target\.  
Type: String  
Required: No

 **OwnerId**   <a name="efs-Type-MountTargetDescription-OwnerId"></a>
AWS account ID that owns the resource\.  
Type: String  
Required: No

 **SubnetId**   <a name="efs-Type-MountTargetDescription-SubnetId"></a>
ID of the mount target's subnet\.  
Type: String  
Required: Yes

## See Also<a name="API_MountTargetDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/MountTargetDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/MountTargetDescription) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/MountTargetDescription) 
+  [AWS SDK for Ruby V2](https://docs.aws.amazon.com/goto/SdkForRubyV2/elasticfilesystem-2015-02-01/MountTargetDescription) 