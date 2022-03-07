# CreateFileSystem<a name="API_CreateFileSystem"></a>

Creates a new, empty file system\. The operation requires a creation token in the request that Amazon EFS uses to ensure idempotent creation \(calling the operation with same creation token has no effect\)\. If a file system does not currently exist that is owned by the caller's AWS account with the specified creation token, this operation does the following:
+ Creates a new, empty file system\. The file system will have an Amazon EFS assigned ID, and an initial lifecycle state `creating`\.
+ Returns with the description of the created file system\.

Otherwise, this operation returns a `FileSystemAlreadyExists` error with the ID of the existing file system\.

**Note**  
For basic use cases, you can use a randomly generated UUID for the creation token\.

 The idempotent operation allows you to retry a `CreateFileSystem` call without risk of creating an extra file system\. This can happen when an initial call fails in a way that leaves it uncertain whether or not a file system was actually created\. An example might be that a transport level timeout occurred or your connection was reset\. As long as you use the same creation token, if the initial call had succeeded in creating a file system, the client can learn of its existence from the `FileSystemAlreadyExists` error\.

For more information, see [Creating a file system](https://docs.aws.amazon.com/efs/latest/ug/creating-using-create-fs.html#creating-using-create-fs-part1) in the *Amazon EFS User Guide*\.

**Note**  
The `CreateFileSystem` call returns while the file system's lifecycle state is still `creating`\. You can check the file system creation status by calling the [DescribeFileSystems](API_DescribeFileSystems.md) operation, which among other things returns the file system state\.

This operation accepts an optional `PerformanceMode` parameter that you choose for your file system\. We recommend `generalPurpose` performance mode for most file systems\. File systems using the `maxIO` performance mode can scale to higher levels of aggregate throughput and operations per second with a tradeoff of slightly higher latencies for most file operations\. The performance mode can't be changed after the file system has been created\. For more information, see [Amazon EFS performance modes](https://docs.aws.amazon.com/efs/latest/ug/performance.html#performancemodes.html)\.

You can set the throughput mode for the file system using the `ThroughputMode` parameter\.

After the file system is fully created, Amazon EFS sets its lifecycle state to `available`, at which point you can create one or more mount targets for the file system in your VPC\. For more information, see [CreateMountTarget](API_CreateMountTarget.md)\. You mount your Amazon EFS file system on an EC2 instances in your VPC by using the mount target\. For more information, see [Amazon EFS: How it Works](https://docs.aws.amazon.com/efs/latest/ug/how-it-works.html)\. 

 This operation requires permissions for the `elasticfilesystem:CreateFileSystem` action\. 

## Request Syntax<a name="API_CreateFileSystem_RequestSyntax"></a>

```
POST /2015-02-01/file-systems HTTP/1.1
Content-type: application/json

{
   "AvailabilityZoneName": "string",
   "Backup": boolean,
   "CreationToken": "string",
   "Encrypted": boolean,
   "KmsKeyId": "string",
   "PerformanceMode": "string",
   "ProvisionedThroughputInMibps": number,
   "Tags": [ 
      { 
         "Key": "string",
         "Value": "string"
      }
   ],
   "ThroughputMode": "string"
}
```

## URI Request Parameters<a name="API_CreateFileSystem_RequestParameters"></a>

The request does not use any URI parameters\.

## Request Body<a name="API_CreateFileSystem_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [AvailabilityZoneName](#API_CreateFileSystem_RequestSyntax) **   <a name="efs-CreateFileSystem-request-AvailabilityZoneName"></a>
Used to create a file system that uses One Zone storage classes\. It specifies the AWS Availability Zone in which to create the file system\. Use the format `us-east-1a` to specify the Availability Zone\. For more information about One Zone storage classes, see [Using EFS storage classes](https://docs.aws.amazon.com/efs/latest/ug/storage-classes.html) in the *Amazon EFS User Guide*\.  
One Zone storage classes are not available in all Availability Zones in AWS Regions where Amazon EFS is available\.
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `.+`   
Required: No

 ** [Backup](#API_CreateFileSystem_RequestSyntax) **   <a name="efs-CreateFileSystem-request-Backup"></a>
Specifies whether automatic backups are enabled on the file system that you are creating\. Set the value to `true` to enable automatic backups\. If you are creating a file system that uses One Zone storage classes, automatic backups are enabled by default\. For more information, see [Automatic backups](https://docs.aws.amazon.com/efs/latest/ug/awsbackup.html#automatic-backups) in the *Amazon EFS User Guide*\.  
Default is `false`\. However, if you specify an `AvailabilityZoneName`, the default is `true`\.  
 AWS Backup is not available in all AWS Regions where Amazon EFS is available\.
Type: Boolean  
Required: No

 ** [CreationToken](#API_CreateFileSystem_RequestSyntax) **   <a name="efs-CreateFileSystem-request-CreationToken"></a>
A string of up to 64 ASCII characters\. Amazon EFS uses this to ensure idempotent creation\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `.+`   
Required: Yes

 ** [Encrypted](#API_CreateFileSystem_RequestSyntax) **   <a name="efs-CreateFileSystem-request-Encrypted"></a>
A Boolean value that, if true, creates an encrypted file system\. When creating an encrypted file system, you have the option of specifying an existing AWS Key Management Service key \(KMS key\)\. If you don't specify a KMS key, then the default KMS key for Amazon EFS, `/aws/elasticfilesystem`, is used to protect the encrypted file system\.   
Type: Boolean  
Required: No

 ** [KmsKeyId](#API_CreateFileSystem_RequestSyntax) **   <a name="efs-CreateFileSystem-request-KmsKeyId"></a>
The ID of the KMS key that you want to use to protect the encrypted file system\. This parameter is required only if you want to use a non\-default KMS key\. If this parameter is not specified, the default KMS key for Amazon EFS is used\. You can specify a KMS key ID using the following formats:  
+ Key ID \- A unique identifier of the key, for example `1234abcd-12ab-34cd-56ef-1234567890ab`\.
+ ARN \- An Amazon Resource Name \(ARN\) for the key, for example `arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab`\.
+ Key alias \- A previously created display name for a key, for example `alias/projectKey1`\.
+ Key alias ARN \- An ARN for a key alias, for example `arn:aws:kms:us-west-2:444455556666:alias/projectKey1`\.
If you use `KmsKeyId`, you must set the [CreateFileSystem:Encrypted](#efs-CreateFileSystem-request-Encrypted) parameter to true\.  
EFS accepts only symmetric KMS keys\. You cannot use asymmetric KMS keys with Amazon EFS file systems\.
Type: String  
Length Constraints: Maximum length of 2048\.  
Pattern: `^([0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}|mrk-[0-9a-f]{32}|alias/[a-zA-Z0-9/_-]+|(arn:aws[-a-z]*:kms:[a-z0-9-]+:\d{12}:((key/[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12})|(key/mrk-[0-9a-f]{32})|(alias/[a-zA-Z0-9/_-]+))))$`   
Required: No

 ** [PerformanceMode](#API_CreateFileSystem_RequestSyntax) **   <a name="efs-CreateFileSystem-request-PerformanceMode"></a>
The performance mode of the file system\. We recommend `generalPurpose` performance mode for most file systems\. File systems using the `maxIO` performance mode can scale to higher levels of aggregate throughput and operations per second with a tradeoff of slightly higher latencies for most file operations\. The performance mode can't be changed after the file system has been created\.  
The `maxIO` mode is not supported on file systems using One Zone storage classes\.
Type: String  
Valid Values:` generalPurpose | maxIO`   
Required: No

 ** [ProvisionedThroughputInMibps](#API_CreateFileSystem_RequestSyntax) **   <a name="efs-CreateFileSystem-request-ProvisionedThroughputInMibps"></a>
The throughput, measured in MiB/s, that you want to provision for a file system that you're creating\. Valid values are 1\-1024\. Required if `ThroughputMode` is set to `provisioned`\. The upper limit for throughput is 1024 MiB/s\. To increase this limit, contact AWS Support\. For more information, see [Amazon EFS quotas that you can increase](https://docs.aws.amazon.com/efs/latest/ug/limits.html#soft-limits) in the *Amazon EFS User Guide*\.  
Type: Double  
Valid Range: Minimum value of 1\.0\.  
Required: No

 ** [Tags](#API_CreateFileSystem_RequestSyntax) **   <a name="efs-CreateFileSystem-request-Tags"></a>
Use to create one or more tags associated with the file system\. Each tag is a user\-defined key\-value pair\. Name your file system on creation by including a `"Key":"Name","Value":"{value}"` key\-value pair\. Each key must be unique\. For more information, see [Tagging AWS resources](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html) in the * AWS General Reference Guide*\.  
Type: Array of [Tag](API_Tag.md) objects  
Required: No

 ** [ThroughputMode](#API_CreateFileSystem_RequestSyntax) **   <a name="efs-CreateFileSystem-request-ThroughputMode"></a>
Specifies the throughput mode for the file system, either `bursting` or `provisioned`\. If you set `ThroughputMode` to `provisioned`, you must also set a value for `ProvisionedThroughputInMibps`\. After you create the file system, you can decrease your file system's throughput in Provisioned Throughput mode or change between the throughput modes, as long as it’s been more than 24 hours since the last decrease or throughput mode change\. For more information, see [Specifying throughput with provisioned mode](https://docs.aws.amazon.com/efs/latest/ug/performance.html#provisioned-throughput) in the *Amazon EFS User Guide*\.   
Default is `bursting`\.  
Type: String  
Valid Values:` bursting | provisioned`   
Required: No

## Response Syntax<a name="API_CreateFileSystem_ResponseSyntax"></a>

```
HTTP/1.1 201
Content-type: application/json

{
   "AvailabilityZoneId": "string",
   "AvailabilityZoneName": "string",
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

## Response Elements<a name="API_CreateFileSystem_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 201 response\.

The following data is returned in JSON format by the service\.

 ** [AvailabilityZoneId](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-AvailabilityZoneId"></a>
The unique and consistent identifier of the Availability Zone in which the file system's One Zone storage classes exist\. For example, `use1-az1` is an Availability Zone ID for the us\-east\-1 AWS Region, and it has the same location in every AWS account\.  
Type: String

 ** [AvailabilityZoneName](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-AvailabilityZoneName"></a>
Describes the AWS Availability Zone in which the file system is located, and is valid only for file systems using One Zone storage classes\. For more information, see [Using EFS storage classes](https://docs.aws.amazon.com/efs/latest/ug/storage-classes.html) in the *Amazon EFS User Guide*\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `.+` 

 ** [CreationTime](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-CreationTime"></a>
The time that the file system was created, in seconds \(since 1970\-01\-01T00:00:00Z\)\.  
Type: Timestamp

 ** [CreationToken](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-CreationToken"></a>
The opaque string specified in the request\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `.+` 

 ** [Encrypted](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-Encrypted"></a>
A Boolean value that, if true, indicates that the file system is encrypted\.  
Type: Boolean

 ** [FileSystemArn](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-FileSystemArn"></a>
The Amazon Resource Name \(ARN\) for the EFS file system, in the format `arn:aws:elasticfilesystem:region:account-id:file-system/file-system-id `\. Example with sample data: `arn:aws:elasticfilesystem:us-west-2:1111333322228888:file-system/fs-01234567`   
Type: String

 ** [FileSystemId](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-FileSystemId"></a>
The ID of the file system, assigned by Amazon EFS\.  
Type: String  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [KmsKeyId](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-KmsKeyId"></a>
The ID of an AWS KMS key used to protect the encrypted file system\.  
Type: String  
Length Constraints: Maximum length of 2048\.  
Pattern: `^([0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}|mrk-[0-9a-f]{32}|alias/[a-zA-Z0-9/_-]+|(arn:aws[-a-z]*:kms:[a-z0-9-]+:\d{12}:((key/[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12})|(key/mrk-[0-9a-f]{32})|(alias/[a-zA-Z0-9/_-]+))))$` 

 ** [LifeCycleState](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-LifeCycleState"></a>
The lifecycle phase of the file system\.  
Type: String  
Valid Values:` creating | available | updating | deleting | deleted | error` 

 ** [Name](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-Name"></a>
You can add tags to a file system, including a `Name` tag\. For more information, see [CreateFileSystem](#API_CreateFileSystem)\. If the file system has a `Name` tag, Amazon EFS returns the value in this field\.   
Type: String  
Length Constraints: Maximum length of 256\.  
Pattern: `^([\p{L}\p{Z}\p{N}_.:/=+\-@]*)$` 

 ** [NumberOfMountTargets](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-NumberOfMountTargets"></a>
The current number of mount targets that the file system has\. For more information, see [CreateMountTarget](API_CreateMountTarget.md)\.  
Type: Integer  
Valid Range: Minimum value of 0\.

 ** [OwnerId](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-OwnerId"></a>
The AWS account that created the file system\. If the file system was created by an IAM user, the parent account to which the user belongs is the owner\.  
Type: String  
Length Constraints: Maximum length of 14\.  
Pattern: `^(\d{12})|(\d{4}-\d{4}-\d{4})$` 

 ** [PerformanceMode](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-PerformanceMode"></a>
The performance mode of the file system\.  
Type: String  
Valid Values:` generalPurpose | maxIO` 

 ** [ProvisionedThroughputInMibps](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-ProvisionedThroughputInMibps"></a>
The amount of provisioned throughput, measured in MiB/s, for the file system\. Valid for file systems using `ThroughputMode` set to `provisioned`\.  
Type: Double  
Valid Range: Minimum value of 1\.0\.

 ** [SizeInBytes](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-SizeInBytes"></a>
The latest known metered size \(in bytes\) of data stored in the file system, in its `Value` field, and the time at which that size was determined in its `Timestamp` field\. The `Timestamp` value is the integer number of seconds since 1970\-01\-01T00:00:00Z\. The `SizeInBytes` value doesn't represent the size of a consistent snapshot of the file system, but it is eventually consistent when there are no writes to the file system\. That is, `SizeInBytes` represents actual size only if the file system is not modified for a period longer than a couple of hours\. Otherwise, the value is not the exact size that the file system was at any point in time\.   
Type: [FileSystemSize](API_FileSystemSize.md) object

 ** [Tags](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-Tags"></a>
The tags associated with the file system, presented as an array of `Tag` objects\.  
Type: Array of [Tag](API_Tag.md) objects

 ** [ThroughputMode](#API_CreateFileSystem_ResponseSyntax) **   <a name="efs-CreateFileSystem-response-ThroughputMode"></a>
Displays the file system's throughput mode\. For more information, see [Throughput modes](https://docs.aws.amazon.com/efs/latest/ug/performance.html#throughput-modes) in the *Amazon EFS User Guide*\.   
Type: String  
Valid Values:` bursting | provisioned` 

## Errors<a name="API_CreateFileSystem_Errors"></a>

 ** BadRequest **   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 ** FileSystemAlreadyExists **   
Returned if the file system you are trying to create already exists, with the creation token you provided\.  
HTTP Status Code: 409

 ** FileSystemLimitExceeded **   
Returned if the AWS account has already created the maximum number of file systems allowed per account\.  
HTTP Status Code: 403

 ** InsufficientThroughputCapacity **   
Returned if there's not enough capacity to provision additional throughput\. This value might be returned when you try to create a file system in provisioned throughput mode, when you attempt to increase the provisioned throughput of an existing file system, or when you attempt to change an existing file system from Bursting Throughput to Provisioned Throughput mode\. Try again later\.  
HTTP Status Code: 503

 ** InternalServerError **   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 ** ThroughputLimitExceeded **   
Returned if the throughput mode or amount of provisioned throughput can't be changed because the throughput limit of 1024 MiB/s has been reached\.  
HTTP Status Code: 400

 ** UnsupportedAvailabilityZone **   
Returned if the requested Amazon EFS functionality is not available in the specified Availability Zone\.  
HTTP Status Code: 400

## Examples<a name="API_CreateFileSystem_Examples"></a>

### Create an encrypted EFS file system<a name="API_CreateFileSystem_Example_1"></a>

 The following example sends a POST request to create a file system in the `us-west-2` Region with automatic backups enabled\. The request specifies `myFileSystem1` as the creation token for idempotency\.

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
  "PerformanceMode" : "generalPurpose",
  "Backup": true,
  "Encrypted": true,
  "Tags":[
      {
         "Key": "Name",
         "Value": "Test Group1"
      }
   ]
}
```

#### Sample Response<a name="API_CreateFileSystem_Example_1_Response"></a>

```
HTTP/1.1 201 Created
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-Type: application/json
Content-Length: 319

{
   "ownerId":"251839141158",
   "CreationToken":"myFileSystem1",
   "Encrypted": true,
   "PerformanceMode" : "generalPurpose",
   "fileSystemId":"fs-01234567",
   "CreationTime":"1403301078",
   "LifeCycleState":"creating",
   "numberOfMountTargets":0,
   "SizeInBytes":{
       "Timestamp": 1403301078,
       "Value": 29313417216,
       "ValueInIA": 675432,
       "ValueInStandard": 29312741784
   },
   "Tags":[
      {
        "Key": "Name",
        "Value": "Test Group1"
      }
   ],
   "ThroughputMode": "bursting"
}
```

### Create an encrypted EFS file system that uses One Zone storage classes<a name="API_CreateFileSystem_Example_2"></a>

 The following example sends a POST request to create a file system in the `us-west-2` Region with automatic backups enabled\. The file system will have One Zone storage redundancy in the `us-west-2b` Availability Zone\.

#### Sample Request<a name="API_CreateFileSystem_Example_2_Request"></a>

```
POST /2015-02-01/file-systems HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140620T215117Z
Authorization: <...>
Content-Type: application/json
Content-Length: 42

{
  "CreationToken" : "myFileSystem2",
  "PerformanceMode" : "generalPurpose",
  "Backup": true,
  "AvailabilityZoneName": "us-west-2b",
  "Encrypted": true,
  "ThroughputMode": "provisioned",
  "ProvisionedThroughputInMibps": 60,
  "Tags":[
      {
         "Key": "Name",
         "Value": "Test Group1"
      }
   ]
}
```

#### Sample Response<a name="API_CreateFileSystem_Example_2_Response"></a>

```
HTTP/1.1 201 Created
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-Type: application/json
Content-Length: 319

{
   "ownerId":"251839141158",
   "CreationToken":"myFileSystem1",
   "Encrypted": true,
   "AvailabilityZoneId": "usew2-az2", 
   "AvailabilityZoneName": "us-west-2b",
   "PerformanceMode" : "generalPurpose",
   "fileSystemId":"fs-01234567",
   "CreationTime":"1403301078",
   "LifeCycleState":"creating",
   "numberOfMountTargets":0,
   "ProvisionedThroughputInMibps": 60,
   "SizeInBytes":{
       "Timestamp": 1403301078,
       "Value": 29313417216,
       "ValueInIA": 675432,
       "ValueInStandard": 29312741784
   },
   "Tags":[
      {
        "Key": "Name",
        "Value": "Test Group1"
      }
   ],
   "ThroughputMode": "provisioned"
}
```

## See Also<a name="API_CreateFileSystem_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/CreateFileSystem) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/CreateFileSystem) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/CreateFileSystem) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/CreateFileSystem) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/CreateFileSystem) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/CreateFileSystem) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/CreateFileSystem) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/CreateFileSystem) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/CreateFileSystem) 