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
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

 **IpAddress**   <a name="efs-Type-MountTargetDescription-IpAddress"></a>
Address at which the file system can be mounted by using the mount target\.  
Type: String  
Length Constraints: Minimum length of 7\. Maximum length of 15\.  
Pattern: `^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$`   
Required: No

 **LifeCycleState**   <a name="efs-Type-MountTargetDescription-LifeCycleState"></a>
Lifecycle state of the mount target\.  
Type: String  
Valid Values:` creating | available | updating | deleting | deleted`   
Required: Yes

 **MountTargetId**   <a name="efs-Type-MountTargetDescription-MountTargetId"></a>
System\-assigned mount target ID\.  
Type: String  
Length Constraints: Minimum length of 13\. Maximum length of 45\.  
Pattern: `^fsmt-[0-9a-f]{8,40}$`   
Required: Yes

 **NetworkInterfaceId**   <a name="efs-Type-MountTargetDescription-NetworkInterfaceId"></a>
The ID of the network interface that Amazon EFS created when it created the mount target\.  
Type: String  
Required: No

 **OwnerId**   <a name="efs-Type-MountTargetDescription-OwnerId"></a>
AWS account ID that owns the resource\.  
Type: String  
Length Constraints: Maximum length of 14\.  
Pattern: `^(\d{12})|(\d{4}-\d{4}-\d{4})$`   
Required: No

 **SubnetId**   <a name="efs-Type-MountTargetDescription-SubnetId"></a>
The ID of the mount target's subnet\.  
Type: String  
Length Constraints: Minimum length of 15\. Maximum length of 47\.  
Pattern: `^subnet-[0-9a-f]{8,40}$`   
Required: Yes

 **VpcId**   <a name="efs-Type-MountTargetDescription-VpcId"></a>
The Virtual Private Cloud \(VPC\) ID that the mount target is configured in\.  
Type: String  
Required: No

## See Also<a name="API_MountTargetDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/MountTargetDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/MountTargetDescription) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/MountTargetDescription) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/MountTargetDescription) 