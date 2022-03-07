# DeleteReplicationConfiguration<a name="API_DeleteReplicationConfiguration"></a>

Deletes an existing replication configuration\. To delete a replication configuration, you must make the request from the AWS Region in which the destination file system is located\. Deleting a replication configuration ends the replication process\. After a replication configuration is deleted, the destination file system is no longer read\-only\. You can write to the destination file system after its status becomes `Writeable`\.

## Request Syntax<a name="API_DeleteReplicationConfiguration_RequestSyntax"></a>

```
DELETE /2015-02-01/file-systems/SourceFileSystemId/replication-configuration HTTP/1.1
```

## URI Request Parameters<a name="API_DeleteReplicationConfiguration_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [SourceFileSystemId](#API_DeleteReplicationConfiguration_RequestSyntax) **   <a name="efs-DeleteReplicationConfiguration-request-SourceFileSystemId"></a>
The ID of the source file system in the replication configuration\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_DeleteReplicationConfiguration_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DeleteReplicationConfiguration_ResponseSyntax"></a>

```
HTTP/1.1 204
```

## Response Elements<a name="API_DeleteReplicationConfiguration_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 204 response with an empty HTTP body\.

## Errors<a name="API_DeleteReplicationConfiguration_Errors"></a>

 ** BadRequest **   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 ** FileSystemNotFound **   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 ** InternalServerError **   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 ** ReplicationNotFound **   
Returned if the specified file system does not have a replication configuration\.  
HTTP Status Code: 404

## See Also<a name="API_DeleteReplicationConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DeleteReplicationConfiguration) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DeleteReplicationConfiguration) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DeleteReplicationConfiguration) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DeleteReplicationConfiguration) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/DeleteReplicationConfiguration) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DeleteReplicationConfiguration) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DeleteReplicationConfiguration) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DeleteReplicationConfiguration) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DeleteReplicationConfiguration) 