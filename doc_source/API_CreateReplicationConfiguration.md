# CreateReplicationConfiguration<a name="API_CreateReplicationConfiguration"></a>

Creates a replication configuration that replicates an existing EFS file system to a new, read\-only file system\. For more information, see [Amazon EFS replication](https://docs.aws.amazon.com/efs/latest/ug/efs-replication.html) in the *Amazon EFS User Guide*\. The replication configuration specifies the following:
+  **Source file system** \- An existing EFS file system that you want replicated\. The source file system cannot be a destination file system in an existing replication configuration\.
+  **Destination file system configuration** \- The configuration of the destination file system to which the source file system will be replicated\. There can only be one destination file system in a replication configuration\. The destination file system configuration consists of the following properties:
  +  ** AWS Region ** \- The AWS Region in which the destination file system is created\. Amazon EFS replication is available in all AWS Regions that Amazon EFS is available in, except Africa \(Cape Town\), Asia Pacific \(Hong Kong\), Asia Pacific \(Jakarta\), Europe \(Milan\), and Middle East \(Bahrain\)\.
  +  **Availability Zone** \- If you want the destination file system to use EFS One Zone availability and durability, you must specify the Availability Zone to create the file system in\. For more information about EFS storage classes, see [ Amazon EFS storage classes](https://docs.aws.amazon.com/efs/latest/ug/storage-classes.html) in the *Amazon EFS User Guide*\.
  +  **Encryption** \- All destination file systems are created with encryption at rest enabled\. You can specify the AWS Key Management Service \(AWS KMS\) key that is used to encrypt the destination file system\. If you don't specify a KMS key, your service\-managed KMS key for Amazon EFS is used\. 
**Note**  
After the file system is created, you cannot change the KMS key\.

The following properties are set by default:
+  **Performance mode** \- The destination file system's performance mode matches that of the source file system, unless the destination file system uses EFS One Zone storage\. In that case, the General Purpose performance mode is used\. The performance mode cannot be changed\.
+  **Throughput mode** \- The destination file system uses the Bursting Throughput mode by default\. After the file system is created, you can modify the throughput mode\.

The following properties are turned off by default:
+  **Lifecycle management** \- EFS lifecycle management and EFS Intelligent\-Tiering are not enabled on the destination file system\. After the destination file system is created, you can enable EFS lifecycle management and EFS Intelligent\-Tiering\.
+  **Automatic backups** \- Automatic daily backups not enabled on the destination file system\. After the file system is created, you can change this setting\.

For more information, see [Amazon EFS replication](https://docs.aws.amazon.com/efs/latest/ug/efs-replication.html) in the *Amazon EFS User Guide*\.

## Request Syntax<a name="API_CreateReplicationConfiguration_RequestSyntax"></a>

```
POST /2015-02-01/file-systems/SourceFileSystemId/replication-configuration HTTP/1.1
Content-type: application/json

{
   "Destinations": [ 
      { 
         "AvailabilityZoneName": "string",
         "KmsKeyId": "string",
         "Region": "string"
      }
   ]
}
```

## URI Request Parameters<a name="API_CreateReplicationConfiguration_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [SourceFileSystemId](#API_CreateReplicationConfiguration_RequestSyntax) **   <a name="efs-CreateReplicationConfiguration-request-SourceFileSystemId"></a>
Specifies the Amazon EFS file system that you want to replicate\. This file system cannot already be a source or destination file system in another replication configuration\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_CreateReplicationConfiguration_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [Destinations](#API_CreateReplicationConfiguration_RequestSyntax) **   <a name="efs-CreateReplicationConfiguration-request-Destinations"></a>
An array of destination configuration objects\. Only one destination configuration object is supported\.  
Type: Array of [DestinationToCreate](API_DestinationToCreate.md) objects  
Required: Yes

## Response Syntax<a name="API_CreateReplicationConfiguration_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "CreationTime": number,
   "Destinations": [ 
      { 
         "FileSystemId": "string",
         "LastReplicatedTimestamp": number,
         "Region": "string",
         "Status": "string"
      }
   ],
   "OriginalSourceFileSystemArn": "string",
   "SourceFileSystemArn": "string",
   "SourceFileSystemId": "string",
   "SourceFileSystemRegion": "string"
}
```

## Response Elements<a name="API_CreateReplicationConfiguration_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [CreationTime](#API_CreateReplicationConfiguration_ResponseSyntax) **   <a name="efs-CreateReplicationConfiguration-response-CreationTime"></a>
Describes when the replication configuration was created\.  
Type: Timestamp

 ** [Destinations](#API_CreateReplicationConfiguration_ResponseSyntax) **   <a name="efs-CreateReplicationConfiguration-response-Destinations"></a>
An array of destination objects\. Only one destination object is supported\.  
Type: Array of [Destination](API_Destination.md) objects

 ** [OriginalSourceFileSystemArn](#API_CreateReplicationConfiguration_ResponseSyntax) **   <a name="efs-CreateReplicationConfiguration-response-OriginalSourceFileSystemArn"></a>
The Amazon Resource Name \(ARN\) of the original source Amazon EFS file system in the replication configuration\.  
Type: String

 ** [SourceFileSystemArn](#API_CreateReplicationConfiguration_ResponseSyntax) **   <a name="efs-CreateReplicationConfiguration-response-SourceFileSystemArn"></a>
The Amazon Resource Name \(ARN\) of the current source file system in the replication configuration\.  
Type: String

 ** [SourceFileSystemId](#API_CreateReplicationConfiguration_ResponseSyntax) **   <a name="efs-CreateReplicationConfiguration-response-SourceFileSystemId"></a>
The ID of the source Amazon EFS file system that is being replicated\.  
Type: String  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [SourceFileSystemRegion](#API_CreateReplicationConfiguration_ResponseSyntax) **   <a name="efs-CreateReplicationConfiguration-response-SourceFileSystemRegion"></a>
The AWS Region in which the source Amazon EFS file system is located\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `^[a-z]{2}-((iso[a-z]{0,1}-)|(gov-)){0,1}[a-z]+-{0,1}[0-9]{0,1}$` 

## Errors<a name="API_CreateReplicationConfiguration_Errors"></a>

 ** BadRequest **   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 ** FileSystemLimitExceeded **   
Returned if the AWS account has already created the maximum number of file systems allowed per account\.  
HTTP Status Code: 403

 ** FileSystemNotFound **   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 ** IncorrectFileSystemLifeCycleState **   
Returned if the file system's lifecycle state is not "available"\.  
HTTP Status Code: 409

 ** InsufficientThroughputCapacity **   
Returned if there's not enough capacity to provision additional throughput\. This value might be returned when you try to create a file system in provisioned throughput mode, when you attempt to increase the provisioned throughput of an existing file system, or when you attempt to change an existing file system from Bursting Throughput to Provisioned Throughput mode\. Try again later\.  
HTTP Status Code: 503

 ** InternalServerError **   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 ** ReplicationNotFound **   
Returned if the specified file system does not have a replication configuration\.  
HTTP Status Code: 404

 ** ThroughputLimitExceeded **   
Returned if the throughput mode or amount of provisioned throughput can't be changed because the throughput limit of 1024 MiB/s has been reached\.  
HTTP Status Code: 400

 ** UnsupportedAvailabilityZone **   
Returned if the requested Amazon EFS functionality is not available in the specified Availability Zone\.  
HTTP Status Code: 400

 ** ValidationException **   
Returned if the AWS Backup service is not available in the AWS Region in which the request was made\.  
HTTP Status Code: 400

## See Also<a name="API_CreateReplicationConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/CreateReplicationConfiguration) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/CreateReplicationConfiguration) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/CreateReplicationConfiguration) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/CreateReplicationConfiguration) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/CreateReplicationConfiguration) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/CreateReplicationConfiguration) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/CreateReplicationConfiguration) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/CreateReplicationConfiguration) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/CreateReplicationConfiguration) 