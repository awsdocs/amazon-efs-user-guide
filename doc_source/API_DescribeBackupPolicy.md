# DescribeBackupPolicy<a name="API_DescribeBackupPolicy"></a>

Returns the backup policy for the specified EFS file system\.

## Request Syntax<a name="API_DescribeBackupPolicy_RequestSyntax"></a>

```
GET /2015-02-01/file-systems/FileSystemId/backup-policy HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeBackupPolicy_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_DescribeBackupPolicy_RequestSyntax) **   <a name="efs-DescribeBackupPolicy-request-FileSystemId"></a>
Specifies which EFS file system to retrieve the `BackupPolicy` for\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_DescribeBackupPolicy_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeBackupPolicy_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "BackupPolicy": { 
      "Status": "string"
   }
}
```

## Response Elements<a name="API_DescribeBackupPolicy_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [BackupPolicy](#API_DescribeBackupPolicy_ResponseSyntax) **   <a name="efs-DescribeBackupPolicy-response-BackupPolicy"></a>
Describes the file system's backup policy, indicating whether automatic backups are turned on or off\.\.  
Type: [BackupPolicy](API_BackupPolicy.md) object

## Errors<a name="API_DescribeBackupPolicy_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 **PolicyNotFound**   
Returned if the default file system policy is in effect for the EFS file system specified\.  
HTTP Status Code: 404

 **ValidationException**   
Returned if the AWS Backup service is not available in the region that the request was made\.  
HTTP Status Code: 400

## See Also<a name="API_DescribeBackupPolicy_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeBackupPolicy) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeBackupPolicy) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeBackupPolicy) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeBackupPolicy) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DescribeBackupPolicy) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeBackupPolicy) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeBackupPolicy) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeBackupPolicy) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeBackupPolicy) 