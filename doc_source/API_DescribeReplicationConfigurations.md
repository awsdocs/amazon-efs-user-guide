# DescribeReplicationConfigurations<a name="API_DescribeReplicationConfigurations"></a>

Retrieves the replication configuration for a specific file system\. If a file system is not specified, all of the replication configurations for the AWS account in an AWS Region are retrieved\.

## Request Syntax<a name="API_DescribeReplicationConfigurations_RequestSyntax"></a>

```
GET /2015-02-01/file-systems/replication-configurations?FileSystemId=FileSystemId&MaxResults=MaxResults&NextToken=NextToken HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeReplicationConfigurations_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_DescribeReplicationConfigurations_RequestSyntax) **   <a name="efs-DescribeReplicationConfigurations-request-FileSystemId"></a>
You can retrieve the replication configuration for a specific file system by providing its file system ID\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [MaxResults](#API_DescribeReplicationConfigurations_RequestSyntax) **   <a name="efs-DescribeReplicationConfigurations-request-MaxResults"></a>
\(Optional\) To limit the number of objects returned in a response, you can specify the `MaxItems` parameter\. The default value is 100\.   
Valid Range: Minimum value of 1\.

 ** [NextToken](#API_DescribeReplicationConfigurations_RequestSyntax) **   <a name="efs-DescribeReplicationConfigurations-request-NextToken"></a>
 `NextToken` is present if the response is paginated\. You can use `NextToken` in a subsequent request to fetch the next page of output\.  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

## Request Body<a name="API_DescribeReplicationConfigurations_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeReplicationConfigurations_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "NextToken": "string",
   "Replications": [ 
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
   ]
}
```

## Response Elements<a name="API_DescribeReplicationConfigurations_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [NextToken](#API_DescribeReplicationConfigurations_ResponseSyntax) **   <a name="efs-DescribeReplicationConfigurations-response-NextToken"></a>
You can use the `NextToken` from the previous response in a subsequent request to fetch the additional descriptions\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

 ** [Replications](#API_DescribeReplicationConfigurations_ResponseSyntax) **   <a name="efs-DescribeReplicationConfigurations-response-Replications"></a>
The collection of replication configurations that is returned\.  
Type: Array of [ReplicationConfigurationDescription](API_ReplicationConfigurationDescription.md) objects

## Errors<a name="API_DescribeReplicationConfigurations_Errors"></a>

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

 ** ValidationException **   
Returned if the AWS Backup service is not available in the AWS Region in which the request was made\.  
HTTP Status Code: 400

## See Also<a name="API_DescribeReplicationConfigurations_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeReplicationConfigurations) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeReplicationConfigurations) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeReplicationConfigurations) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeReplicationConfigurations) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/DescribeReplicationConfigurations) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeReplicationConfigurations) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeReplicationConfigurations) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeReplicationConfigurations) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeReplicationConfigurations) 