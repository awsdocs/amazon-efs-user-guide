# CreateMountTarget<a name="API_CreateMountTarget"></a>

Creates a mount target for a file system\. You can then mount the file system on EC2 instances by using the mount target\.

You can create one mount target in each Availability Zone in your VPC\. All EC2 instances in a VPC within a given Availability Zone share a single mount target for a given file system\. If you have multiple subnets in an Availability Zone, you create a mount target in one of the subnets\. EC2 instances do not need to be in the same subnet as the mount target in order to access their file system\. For more information, see [Amazon EFS: How it Works](https://docs.aws.amazon.com/efs/latest/ug/how-it-works.html)\. 

In the request, you also specify a file system ID for which you are creating the mount target and the file system's lifecycle state must be `available`\. For more information, see [DescribeFileSystems](API_DescribeFileSystems.md)\.

In the request, you also provide a subnet ID, which determines the following:
+ VPC in which Amazon EFS creates the mount target
+ Availability Zone in which Amazon EFS creates the mount target
+ IP address range from which Amazon EFS selects the IP address of the mount target \(if you don't specify an IP address in the request\)

After creating the mount target, Amazon EFS returns a response that includes, a `MountTargetId` and an `IpAddress`\. You use this IP address when mounting the file system in an EC2 instance\. You can also use the mount target's DNS name when mounting the file system\. The EC2 instance on which you mount the file system by using the mount target can resolve the mount target's DNS name to its IP address\. For more information, see [How it Works: Implementation Overview](https://docs.aws.amazon.com/efs/latest/ug/how-it-works.html#how-it-works-implementation)\. 

Note that you can create mount targets for a file system in only one VPC, and there can be only one mount target per Availability Zone\. That is, if the file system already has one or more mount targets created for it, the subnet specified in the request to add another mount target must meet the following requirements:
+ Must belong to the same VPC as the subnets of the existing mount targets
+ Must not be in the same Availability Zone as any of the subnets of the existing mount targets

If the request satisfies the requirements, Amazon EFS does the following:
+ Creates a new mount target in the specified subnet\.
+ Also creates a new network interface in the subnet as follows:
  + If the request provides an `IpAddress`, Amazon EFS assigns that IP address to the network interface\. Otherwise, Amazon EFS assigns a free address in the subnet \(in the same way that the Amazon EC2 `CreateNetworkInterface` call does when a request does not specify a primary private IP address\)\.
  + If the request provides `SecurityGroups`, this network interface is associated with those security groups\. Otherwise, it belongs to the default security group for the subnet's VPC\.
  + Assigns the description `Mount target fsmt-id for file system fs-id ` where ` fsmt-id ` is the mount target ID, and ` fs-id ` is the `FileSystemId`\.
  + Sets the `requesterManaged` property of the network interface to `true`, and the `requesterId` value to `EFS`\.

  Each Amazon EFS mount target has one corresponding requester\-managed EC2 network interface\. After the network interface is created, Amazon EFS sets the `NetworkInterfaceId` field in the mount target's description to the network interface ID, and the `IpAddress` field to its address\. If network interface creation fails, the entire `CreateMountTarget` operation fails\.

**Note**  
The `CreateMountTarget` call returns only after creating the network interface, but while the mount target state is still `creating`, you can check the mount target creation status by calling the [DescribeMountTargets](API_DescribeMountTargets.md) operation, which among other things returns the mount target state\.

We recommend that you create a mount target in each of the Availability Zones\. There are cost considerations for using a file system in an Availability Zone through a mount target created in another Availability Zone\. For more information, see [Amazon EFS](http://aws.amazon.com/efs/)\. In addition, by always using a mount target local to the instance's Availability Zone, you eliminate a partial failure scenario\. If the Availability Zone in which your mount target is created goes down, then you can't access your file system through that mount target\. 

This operation requires permissions for the following action on the file system:
+  `elasticfilesystem:CreateMountTarget` 

This operation also requires permissions for the following Amazon EC2 actions:
+  `ec2:DescribeSubnets` 
+  `ec2:DescribeNetworkInterfaces` 
+  `ec2:CreateNetworkInterface` 

## Request Syntax<a name="API_CreateMountTarget_RequestSyntax"></a>

```
POST /2015-02-01/mount-targets HTTP/1.1
Content-type: application/json

{
   "FileSystemId": "string",
   "IpAddress": "string",
   "SecurityGroups": [ "string" ],
   "SubnetId": "string"
}
```

## URI Request Parameters<a name="API_CreateMountTarget_RequestParameters"></a>

The request does not use any URI parameters\.

## Request Body<a name="API_CreateMountTarget_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [FileSystemId](#API_CreateMountTarget_RequestSyntax) **   <a name="efs-CreateMountTarget-request-FileSystemId"></a>
The ID of the file system for which to create the mount target\.  
Type: String  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

 ** [IpAddress](#API_CreateMountTarget_RequestSyntax) **   <a name="efs-CreateMountTarget-request-IpAddress"></a>
Valid IPv4 address within the address range of the specified subnet\.  
Type: String  
Length Constraints: Minimum length of 7\. Maximum length of 15\.  
Pattern: `^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$`   
Required: No

 ** [SecurityGroups](#API_CreateMountTarget_RequestSyntax) **   <a name="efs-CreateMountTarget-request-SecurityGroups"></a>
Up to five VPC security group IDs, of the form `sg-xxxxxxxx`\. These must be for the same VPC as subnet specified\.  
Type: Array of strings  
Array Members: Maximum number of 5 items\.  
Length Constraints: Minimum length of 11\. Maximum length of 43\.  
Pattern: `^sg-[0-9a-f]{8,40}`   
Required: No

 ** [SubnetId](#API_CreateMountTarget_RequestSyntax) **   <a name="efs-CreateMountTarget-request-SubnetId"></a>
The ID of the subnet to add the mount target in\.  
Type: String  
Length Constraints: Minimum length of 15\. Maximum length of 47\.  
Pattern: `^subnet-[0-9a-f]{8,40}$`   
Required: Yes

## Response Syntax<a name="API_CreateMountTarget_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "AvailabilityZoneId": "string",
   "AvailabilityZoneName": "string",
   "FileSystemId": "string",
   "IpAddress": "string",
   "LifeCycleState": "string",
   "MountTargetId": "string",
   "NetworkInterfaceId": "string",
   "OwnerId": "string",
   "SubnetId": "string",
   "VpcId": "string"
}
```

## Response Elements<a name="API_CreateMountTarget_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [AvailabilityZoneId](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-AvailabilityZoneId"></a>
The unique and consistent identifier of the Availability Zone \(AZ\) that the mount target resides in\. For example, `use1-az1` is an AZ ID for the us\-east\-1 Region and it has the same location in every AWS account\.  
Type: String

 ** [AvailabilityZoneName](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-AvailabilityZoneName"></a>
The name of the Availability Zone \(AZ\) that the mount target resides in\. AZs are independently mapped to names for each AWS account\. For example, the Availability Zone `us-east-1a` for your AWS account might not be the same location as `us-east-1a` for another AWS account\.  
Type: String

 ** [FileSystemId](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-FileSystemId"></a>
The ID of the file system for which the mount target is intended\.  
Type: String  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [IpAddress](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-IpAddress"></a>
Address at which the file system can be mounted by using the mount target\.  
Type: String  
Length Constraints: Minimum length of 7\. Maximum length of 15\.  
Pattern: `^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$` 

 ** [LifeCycleState](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-LifeCycleState"></a>
Lifecycle state of the mount target\.  
Type: String  
Valid Values:` creating | available | updating | deleting | deleted` 

 ** [MountTargetId](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-MountTargetId"></a>
System\-assigned mount target ID\.  
Type: String  
Length Constraints: Minimum length of 13\. Maximum length of 45\.  
Pattern: `^fsmt-[0-9a-f]{8,40}$` 

 ** [NetworkInterfaceId](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-NetworkInterfaceId"></a>
The ID of the network interface that Amazon EFS created when it created the mount target\.  
Type: String

 ** [OwnerId](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-OwnerId"></a>
AWS account ID that owns the resource\.  
Type: String  
Length Constraints: Maximum length of 14\.  
Pattern: `^(\d{12})|(\d{4}-\d{4}-\d{4})$` 

 ** [SubnetId](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-SubnetId"></a>
The ID of the mount target's subnet\.  
Type: String  
Length Constraints: Minimum length of 15\. Maximum length of 47\.  
Pattern: `^subnet-[0-9a-f]{8,40}$` 

 ** [VpcId](#API_CreateMountTarget_ResponseSyntax) **   <a name="efs-CreateMountTarget-response-VpcId"></a>
The Virtual Private Cloud \(VPC\) ID that the mount target is configured in\.  
Type: String

## Errors<a name="API_CreateMountTarget_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **IncorrectFileSystemLifeCycleState**   
Returned if the file system's lifecycle state is not "available"\.  
HTTP Status Code: 409

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 **IpAddressInUse**   
Returned if the request specified an `IpAddress` that is already in use in the subnet\.  
HTTP Status Code: 409

 **MountTargetConflict**   
Returned if the mount target would violate one of the specified restrictions based on the file system's existing mount targets\.  
HTTP Status Code: 409

 **NetworkInterfaceLimitExceeded**   
The calling account has reached the limit for elastic network interfaces for the specific AWS Region\. The client should try to delete some elastic network interfaces or get the account limit raised\. For more information, see [Amazon VPC Limits](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Appendix_Limits.html) in the *Amazon VPC User Guide * \(see the Network interfaces per VPC entry in the table\)\.   
HTTP Status Code: 409

 **NoFreeAddressesInSubnet**   
Returned if `IpAddress` was not specified in the request and there are no free IP addresses in the subnet\.  
HTTP Status Code: 409

 **SecurityGroupLimitExceeded**   
Returned if the size of `SecurityGroups` specified in the request is greater than five\.  
HTTP Status Code: 400

 **SecurityGroupNotFound**   
Returned if one of the specified security groups doesn't exist in the subnet's VPC\.  
HTTP Status Code: 400

 **SubnetNotFound**   
Returned if there is no subnet with ID `SubnetId` provided in the request\.  
HTTP Status Code: 400

 **UnsupportedAvailabilityZone**   
HTTP Status Code: 400

## Examples<a name="API_CreateMountTarget_Examples"></a>

### Add a Mount Target to a File System<a name="API_CreateMountTarget_Example_1"></a>

 The following request creates a mount target for a file system\. The request specifies values for only the required `FileSystemId` and `SubnetId` parameters\. The request does not provide the optional `IpAddress` and `SecurityGroups` parameters\. For `IpAddress`, the operation uses one of the available IP addresses in the specified subnet\. And, the operation uses the default security group associated with the VPC for the `SecurityGroups`\.

#### Sample Request<a name="API_CreateMountTarget_Example_1_Request"></a>

```
POST /2015-02-01/mount-targets HTTP/1.1 
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140620T221118Z
Authorization: <...>
Content-Type: application/json
Content-Length: 160

{"SubnetId": "subnet-748c5d03", "FileSystemId": "fs-01234567"}
```

#### Sample Response<a name="API_CreateMountTarget_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-Type: application/json
Content-Length: 252

{
    "MountTargetId": "fsmt-55a4413c",
    "NetworkInterfaceId": "eni-01234567",
    "FileSystemId": "fs-01234567",
    "LifeCycleState": "available",
    "SubnetId": "subnet-01234567",
    "OwnerId": "231243201240",
    "IpAddress": "172.31.22.183"
}
```

### Add a Mount Target to a File System<a name="API_CreateMountTarget_Example_2"></a>

 The following request specifies all the request parameters to create a mount target\.

#### Sample Request<a name="API_CreateMountTarget_Example_2_Request"></a>

```
POST /2015-02-01/mount-targets HTTP/1.1 
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140620T221118Z
Authorization: <...>
Content-Type: application/json
Content-Length: 160

{
   "FileSystemId":"fs-01234567",
   "SubnetId":"subnet-01234567",
   "IpAddress":"10.0.2.42",
   "SecurityGroups":[
      "sg-01234567"
   ]
}
```

#### Sample Response<a name="API_CreateMountTarget_Example_2_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-Type: application/json
Content-Length: 252

{
   "OwnerId":"251839141158",
   "MountTargetId":"fsmt-9a13661e",
   "FileSystemId":"fs-01234567",
   "SubnetId":"subnet-fd04ff94",
   "LifeCycleState":"available",
   "IpAddress":"10.0.2.42",
   "NetworkInterfaceId":"eni-1bcb7772"
}
```

## See Also<a name="API_CreateMountTarget_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/CreateMountTarget) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/CreateMountTarget) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/CreateMountTarget) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/CreateMountTarget) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/CreateMountTarget) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/CreateMountTarget) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/CreateMountTarget) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/CreateMountTarget) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/CreateMountTarget) 