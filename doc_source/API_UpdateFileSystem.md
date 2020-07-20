# UpdateFileSystem<a name="API_UpdateFileSystem"></a>

Updates the throughput mode or the amount of provisioned throughput of an existing file system\.

## Request Syntax<a name="API_UpdateFileSystem_RequestSyntax"></a>

```
PUT /2015-02-01/file-systems/FileSystemId HTTP/1.1
Content-type: application/json

{
   "ProvisionedThroughputInMibps": number,
   "ThroughputMode": "string"
}
```

## URI Request Parameters<a name="API_UpdateFileSystem_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_UpdateFileSystem_RequestSyntax) **   <a name="efs-UpdateFileSystem-request-FileSystemId"></a>
The ID of the file system that you want to update\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_UpdateFileSystem_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [ProvisionedThroughputInMibps](#API_UpdateFileSystem_RequestSyntax) **   <a name="efs-UpdateFileSystem-request-ProvisionedThroughputInMibps"></a>
\(Optional\) The amount of throughput, in MiB/s, that you want to provision for your file system\. Valid values are 1\-1024\. Required if `ThroughputMode` is changed to `provisioned` on update\. If you're not updating the amount of provisioned throughput for your file system, you don't need to provide this value in your request\.   
Type: Double  
Valid Range: Minimum value of 1\.0\.  
Required: No

 ** [ThroughputMode](#API_UpdateFileSystem_RequestSyntax) **   <a name="efs-UpdateFileSystem-request-ThroughputMode"></a>
\(Optional\) The throughput mode that you want your file system to use\. If you're not updating your throughput mode, you don't need to provide this value in your request\. If you are changing the `ThroughputMode` to `provisioned`, you must also set a value for `ProvisionedThroughputInMibps`\.  
Type: String  
Valid Values:` bursting | provisioned`   
Required: No

## Response Syntax<a name="API_UpdateFileSystem_ResponseSyntax"></a>

```
HTTP/1.1 202
Content-type: application/json

{
   "CreationTime": number,
   "CreationToken": "string",
   "Encrypted": boolean,
   "FileSystemArn": "string",
   "FileSystemId": "string",
   "KmsKeyId": "string",
   "LifeCycleState": "string",
   "Name": "string",
   "NumberOfMountTargets": number,
   "OwnerId": "string",
   "PerformanceMode": "string",
   "ProvisionedThroughputInMibps": number,
   "SizeInBytes": { 
      "Timestamp": number,
      "Value": number,
      "ValueInIA": number,
      "ValueInStandard": number
   },
   "Tags": [ 
      { 
         "Key": "string",
         "Value": "string"
      }
   ],
   "ThroughputMode": "string"
}
```

## Response Elements<a name="API_UpdateFileSystem_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 202 response\.

The following data is returned in JSON format by the service\.

 ** [CreationTime](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-CreationTime"></a>
The time that the file system was created, in seconds \(since 1970\-01\-01T00:00:00Z\)\.  
Type: Timestamp

 ** [CreationToken](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-CreationToken"></a>
The opaque string specified in the request\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `.+` 

 ** [Encrypted](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-Encrypted"></a>
A Boolean value that, if true, indicates that the file system is encrypted\.  
Type: Boolean

 ** [FileSystemArn](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-FileSystemArn"></a>
The Amazon Resource Name \(ARN\) for the EFS file system, in the format `arn:aws:elasticfilesystem:region:account-id:file-system/file-system-id `\. Example with sample data: `arn:aws:elasticfilesystem:us-west-2:1111333322228888:file-system/fs-01234567`   
Type: String

 ** [FileSystemId](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-FileSystemId"></a>
The ID of the file system, assigned by Amazon EFS\.  
Type: String  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [KmsKeyId](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-KmsKeyId"></a>
The ID of an AWS Key Management Service \(AWS KMS\) customer master key \(CMK\) that was used to protect the encrypted file system\.  
Type: String  
Length Constraints: Maximum length of 2048\.  
Pattern: `^([0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}|alias/[a-zA-Z0-9/_-]+|(arn:aws[-a-z]*:kms:[a-z0-9-]+:\d{12}:((key/[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12})|(alias/[a-zA-Z0-9/_-]+))))$` 

 ** [LifeCycleState](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-LifeCycleState"></a>
The lifecycle phase of the file system\.  
Type: String  
Valid Values:` creating | available | updating | deleting | deleted` 

 ** [Name](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-Name"></a>
You can add tags to a file system, including a `Name` tag\. For more information, see [CreateFileSystem](API_CreateFileSystem.md)\. If the file system has a `Name` tag, Amazon EFS returns the value in this field\.   
Type: String  
Length Constraints: Maximum length of 256\.  
Pattern: `^([\p{L}\p{Z}\p{N}_.:/=+\-@]*)$` 

 ** [NumberOfMountTargets](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-NumberOfMountTargets"></a>
The current number of mount targets that the file system has\. For more information, see [CreateMountTarget](API_CreateMountTarget.md)\.  
Type: Integer  
Valid Range: Minimum value of 0\.

 ** [OwnerId](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-OwnerId"></a>
The AWS account that created the file system\. If the file system was created by an IAM user, the parent account to which the user belongs is the owner\.  
Type: String  
Length Constraints: Maximum length of 14\.  
Pattern: `^(\d{12})|(\d{4}-\d{4}-\d{4})$` 

 ** [PerformanceMode](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-PerformanceMode"></a>
The performance mode of the file system\.  
Type: String  
Valid Values:` generalPurpose | maxIO` 

 ** [ProvisionedThroughputInMibps](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-ProvisionedThroughputInMibps"></a>
The throughput, measured in MiB/s, that you want to provision for a file system\. Valid values are 1\-1024\. Required if `ThroughputMode` is set to `provisioned`\. The limit on throughput is 1024 MiB/s\. You can get these limits increased by contacting AWS Support\. For more information, see [Amazon EFS Limits That You Can Increase](https://docs.aws.amazon.com/efs/latest/ug/limits.html#soft-limits) in the *Amazon EFS User Guide\.*   
Type: Double  
Valid Range: Minimum value of 1\.0\.

 ** [SizeInBytes](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-SizeInBytes"></a>
The latest known metered size \(in bytes\) of data stored in the file system, in its `Value` field, and the time at which that size was determined in its `Timestamp` field\. The `Timestamp` value is the integer number of seconds since 1970\-01\-01T00:00:00Z\. The `SizeInBytes` value doesn't represent the size of a consistent snapshot of the file system, but it is eventually consistent when there are no writes to the file system\. That is, `SizeInBytes` represents actual size only if the file system is not modified for a period longer than a couple of hours\. Otherwise, the value is not the exact size that the file system was at any point in time\.   
Type: [FileSystemSize](API_FileSystemSize.md) object

 ** [Tags](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-Tags"></a>
The tags associated with the file system, presented as an array of `Tag` objects\.  
Type: Array of [Tag](API_Tag.md) objects

 ** [ThroughputMode](#API_UpdateFileSystem_ResponseSyntax) **   <a name="efs-UpdateFileSystem-response-ThroughputMode"></a>
The throughput mode for a file system\. There are two throughput modes to choose from for your file system: `bursting` and `provisioned`\. If you set `ThroughputMode` to `provisioned`, you must also set a value for `ProvisionedThroughPutInMibps`\. You can decrease your file system's throughput in Provisioned Throughput mode or change between the throughput modes as long as it’s been more than 24 hours since the last decrease or throughput mode change\.   
Type: String  
Valid Values:` bursting | provisioned` 

## Errors<a name="API_UpdateFileSystem_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **IncorrectFileSystemLifeCycleState**   
Returned if the file system's lifecycle state is not "available"\.  
HTTP Status Code: 409

 **InsufficientThroughputCapacity**   
Returned if there's not enough capacity to provision additional throughput\. This value might be returned when you try to create a file system in provisioned throughput mode, when you attempt to increase the provisioned throughput of an existing file system, or when you attempt to change an existing file system from bursting to provisioned throughput mode\.  
HTTP Status Code: 503

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 **ThroughputLimitExceeded**   
Returned if the throughput mode or amount of provisioned throughput can't be changed because the throughput limit of 1024 MiB/s has been reached\.  
HTTP Status Code: 400

 **TooManyRequests**   
Returned if you don’t wait at least 24 hours before changing the throughput mode, or decreasing the Provisioned Throughput value\.  
HTTP Status Code: 429

## See Also<a name="API_UpdateFileSystem_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/UpdateFileSystem) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/UpdateFileSystem) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/UpdateFileSystem) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/UpdateFileSystem) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/UpdateFileSystem) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/UpdateFileSystem) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/UpdateFileSystem) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/UpdateFileSystem) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/UpdateFileSystem) 