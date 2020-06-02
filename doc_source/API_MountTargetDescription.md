# MountTargetDescription<a name="API_MountTargetDescription"></a>

Provides a description of a mount target\.

## Contents<a name="API_MountTargetDescription_Contents"></a>

 **AvailabilityZoneId**   <a name="efs-Type-MountTargetDescription-AvailabilityZoneId"></a>
The unique and consistent identifier of the Availability Zone \(AZ\) that the mount target resides in\. For example, `use1-az1` is an AZ ID for the us\-east\-1 Region and it has the same location in every AWS account\.  
Type: String  
Required: No

 **AvailabilityZoneName**   <a name="efs-Type-MountTargetDescription-AvailabilityZoneName"></a>
The name of the Availability Zone \(AZ\) that the mount target resides in\. AZs are independently mapped to names for each AWS account\. For example, the Availability Zone `us-east-1a` for your AWS account might not be the same location as `us-east-1a` for another AWS account\.  
Type: String  
Required: No

 **FileSystemId**   <a name="efs-Type-MountTargetDescription-FileSystemId"></a>
The ID of the file system for which the mount target is intended\.  
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
The ID of the network interface that Amazon EFS created when it created the mount target\.  
Type: String  
Required: No

 **OwnerId**   <a name="efs-Type-MountTargetDescription-OwnerId"></a>
AWS account ID that owns the resource\.  
Type: String  
Required: No

 **SubnetId**   <a name="efs-Type-MountTargetDescription-SubnetId"></a>
The ID of the mount target's subnet\.  
Type: String  
Required: Yes

## See Also<a name="API_MountTargetDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/MountTargetDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/MountTargetDescription) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/MountTargetDescription) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/MountTargetDescription) 