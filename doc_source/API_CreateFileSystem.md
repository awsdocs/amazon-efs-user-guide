# CreateFileSystem<a name="API_CreateFileSystem"></a>

Creates a new, empty file system\. The operation requires a creation token in the request that Amazon EFS uses to ensure idempotent creation \(calling the operation with same creation token has no effect\)\. If a file system does not currently exist that is owned by the caller's AWS account with the specified creation token, this operation does the following:

+ Creates a new, empty file system\. The file system will have an Amazon EFS assigned ID, and an initial lifecycle state `creating`\.

+ Returns with the description of the created file system\.

Otherwise, this operation returns a `FileSystemAlreadyExists` error with the ID of the existing file system\.

**Note**  
For basic use cases, you can use a randomly generated UUID for the creation token\.

 The idempotent operation allows you to retry a `CreateFileSystem` call without risk of creating an extra file system\. This can happen when an initial call fails in a way that leaves it uncertain whether or not a file system was actually created\. An example might be that a transport level timeout occurred or your connection was reset\. As long as you use the same creation token, if the initial call had succeeded in creating a file system, the client can learn of its existence from the `FileSystemAlreadyExists` error\.

**Note**  
The `CreateFileSystem` call returns while the file system's lifecycle state is still `creating`\. You can check the file system creation status by calling the [DescribeFileSystems](API_DescribeFileSystems.md) operation, which among other things returns the file system state\.

This operation also takes an optional `PerformanceMode` parameter that you choose for your file system\. We recommend `generalPurpose` performance mode for most file systems\. File systems using the `maxIO` performance mode can scale to higher levels of aggregate throughput and operations per second with a tradeoff of slightly higher latencies for most file operations\. The performance mode can't be changed after the file system has been created\. For more information, see [Amazon EFS: Performance Modes](http://docs.aws.amazon.com/efs/latest/ug/performance.html#performancemodes.html)\.

After the file system is fully created, Amazon EFS sets its lifecycle state to `available`, at which point you can create one or more mount targets for the file system in your VPC\. For more information, see [CreateMountTarget](API_CreateMountTarget.md)\. You mount your Amazon EFS file system on an EC2 instances in your VPC via the mount target\. For more information, see [Amazon EFS: How it Works](http://docs.aws.amazon.com/efs/latest/ug/how-it-works.html)\. 

 This operation requires permissions for the `elasticfilesystem:CreateFileSystem` action\. 

## Request Syntax<a name="API_CreateFileSystem_RequestSyntax"></a>

```
POST /2015-02-01/file-systems HTTP/1.1
Content-type: application/json

{
   "CreationToken": "string",
   "Encrypted": boolean,
   "KmsKeyId": "string",
   "PerformanceMode": "string"
}
```

## URI Request Parameters<a name="API_CreateFileSystem_RequestParameters"></a>

The request does not use any URI parameters\.

## Request Body<a name="API_CreateFileSystem_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** CreationToken **   
String of up to 64 ASCII characters\. Amazon EFS uses this to ensure idempotent creation\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Required: Yes

 ** Encrypted **   
A Boolean value that, if true, creates an encrypted file system\. When creating an encrypted file system, you have the option of specifying a CreateFileSystem:KmsKeyId for an existing AWS Key Management Service \(AWS KMS\) customer master key \(CMK\)\. If you don't specify a CMK, then the default CMK for Amazon EFS, `/aws/elasticfilesystem`, is used to protect the encrypted file system\.   
Type: Boolean  
Required: No

 ** KmsKeyId **   
The ID of the AWS KMS CMK to be used to protect the encrypted file system\. This parameter is only required if you want to use a non\-default CMK\. If this parameter is not specified, the default CMK for Amazon EFS is used\. This ID can be in one of the following formats:  

+ Key ID \- A unique identifier of the key, for example, `1234abcd-12ab-34cd-56ef-1234567890ab`\.

+ ARN \- An Amazon Resource Name \(ARN\) for the key, for example, `arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab`\.

+ Key alias \- A previously created display name for a key\. For example, `alias/projectKey1`\.

+ Key alias ARN \- An ARN for a key alias, for example, `arn:aws:kms:us-west-2:444455556666:alias/projectKey1`\.
If KmsKeyId is specified, the CreateFileSystem:Encrypted parameter must be set to true\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Required: No

 ** PerformanceMode **   
The `PerformanceMode` of the file system\. We recommend `generalPurpose` performance mode for most file systems\. File systems using the `maxIO` performance mode can scale to higher levels of aggregate throughput and operations per second with a tradeoff of slightly higher latencies for most file operations\. This can't be changed after the file system has been created\.  
Type: String  
Valid Values:` generalPurpose | maxIO`   
Required: No

## Response Syntax<a name="API_CreateFileSystem_ResponseSyntax"></a>

```
HTTP/1.1 201
Content-type: application/json

{
   "CreationTime": number,
   "CreationToken": "string",
   "Encrypted": boolean,
   "FileSystemId": "string",
   "KmsKeyId": "string",
   "LifeCycleState": "string",
   "Name": "string",
   "NumberOfMountTargets": number,
   "OwnerId": "string",
   "PerformanceMode": "string",
   "SizeInBytes": { 
      "Timestamp": number,
      "Value": number
   }
}
```

## Response Elements<a name="API_CreateFileSystem_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 201 response\.

The following data is returned in JSON format by the service\.

 ** CreationTime **   
Time that the file system was created, in seconds \(since 1970\-01\-01T00:00:00Z\)\.  
Type: Timestamp

 ** CreationToken **   
Opaque string specified in the request\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.

 ** Encrypted **   
A Boolean value that, if true, indicates that the file system is encrypted\.  
Type: Boolean

 ** FileSystemId **   
ID of the file system, assigned by Amazon EFS\.  
Type: String

 ** KmsKeyId **   
The ID of an AWS Key Management Service \(AWS KMS\) customer master key \(CMK\) that was used to protect the encrypted file system\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.

 ** LifeCycleState **   
Lifecycle phase of the file system\.  
Type: String  
Valid Values:` creating | available | deleting | deleted` 

 ** Name **   
You can add tags to a file system, including a `Name` tag\. For more information, see [CreateTags](API_CreateTags.md)\. If the file system has a `Name` tag, Amazon EFS returns the value in this field\.   
Type: String  
Length Constraints: Maximum length of 256\.

 ** NumberOfMountTargets **   
Current number of mount targets that the file system has\. For more information, see [CreateMountTarget](API_CreateMountTarget.md)\.  
Type: Integer  
Valid Range: Minimum value of 0\.

 ** OwnerId **   
AWS account that created the file system\. If the file system was created by an IAM user, the parent account to which the user belongs is the owner\.  
Type: String

 ** PerformanceMode **   
The `PerformanceMode` of the file system\.  
Type: String  
Valid Values:` generalPurpose | maxIO` 

 ** SizeInBytes **   
Latest known metered size \(in bytes\) of data stored in the file system, in bytes, in its `Value` field, and the time at which that size was determined in its `Timestamp` field\. The `Timestamp` value is the integer number of seconds since 1970\-01\-01T00:00:00Z\. Note that the value does not represent the size of a consistent snapshot of the file system, but it is eventually consistent when there are no writes to the file system\. That is, the value will represent actual size only if the file system is not modified for a period longer than a couple of hours\. Otherwise, the value is not the exact size the file system was at any instant in time\.   
Type: [FileSystemSize](API_FileSystemSize.md) object

## Errors<a name="API_CreateFileSystem_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemAlreadyExists**   
Returned if the file system you are trying to create already exists, with the creation token you provided\.  
HTTP Status Code: 409

 **FileSystemLimitExceeded**   
Returned if the AWS account has already created maximum number of file systems allowed per account\.  
HTTP Status Code: 403

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## Example<a name="API_CreateFileSystem_Examples"></a>

### Create a file system<a name="API_CreateFileSystem_Example_1"></a>

 The following example sends a POST request to create a file system in the `us-west-2` region\. The request specifies `myFileSystem1` as the creation token\.

#### Sample Request<a name="API_CreateFileSystem_Example_1_Request"></a>

```
POST /2015-02-01/file-systems HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140620T215117Z
Authorization: <...>
Content-Type: application/json
Content-Length: 42

{
"CreationToken" : "myFileSystem1",
"PerformanceMode" : "generalPurpose"
}
```

#### Sample Response<a name="API_CreateFileSystem_Example_1_Response"></a>

```
HTTP/1.1 201 Created
x-amzn-RequestId: 7560489e-8bc7-4a56-a09a-757ce6f4832a
Content-Type: application/json
Content-Length: 319

{
   "ownerId":"251839141158",
   "creationToken":"myFileSystem1",
   "PerformanceMode" : "generalPurpose",
   "fileSystemId":"fs-47a2c22e",
   "CreationTime":"1403301078",
   "LifeCycleState":"creating",
   "numberOfMountTargets":0,
   "sizeInBytes":{
      "value":1024,
      "timestamp":"1403301078"
   }
}
```

## See Also<a name="API_CreateFileSystem_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/CreateFileSystem) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/CreateFileSystem) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/CreateFileSystem) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/CreateFileSystem) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/CreateFileSystem) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/CreateFileSystem) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/CreateFileSystem) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/CreateFileSystem) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/elasticfilesystem-2015-02-01/CreateFileSystem) 