# DeleteFileSystemPolicy<a name="API_DeleteFileSystemPolicy"></a>

Deletes the `FileSystemPolicy` for the specified file system\. The default `FileSystemPolicy` goes into effect once the existing policy is deleted\. For more information about the default file system policy, see [Using Resource\-based Policies with EFS](https://docs.aws.amazon.com/efs/latest/ug/res-based-policies-efs.html)\.

This operation requires permissions for the `elasticfilesystem:DeleteFileSystemPolicy` action\.

## Request Syntax<a name="API_DeleteFileSystemPolicy_RequestSyntax"></a>

```
DELETE /2015-02-01/file-systems/FileSystemId/policy HTTP/1.1
```

## URI Request Parameters<a name="API_DeleteFileSystemPolicy_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_DeleteFileSystemPolicy_RequestSyntax) **   <a name="efs-DeleteFileSystemPolicy-request-FileSystemId"></a>
Specifies the EFS file system for which to delete the `FileSystemPolicy`\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_DeleteFileSystemPolicy_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DeleteFileSystemPolicy_ResponseSyntax"></a>

```
HTTP/1.1 200
```

## Response Elements<a name="API_DeleteFileSystemPolicy_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_DeleteFileSystemPolicy_Errors"></a>

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **IncorrectFileSystemLifeCycleState**   
Returned if the file system's lifecycle state is not "available"\.  
HTTP Status Code: 409

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## See Also<a name="API_DeleteFileSystemPolicy_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DeleteFileSystemPolicy) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DeleteFileSystemPolicy) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DeleteFileSystemPolicy) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DeleteFileSystemPolicy) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DeleteFileSystemPolicy) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DeleteFileSystemPolicy) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DeleteFileSystemPolicy) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DeleteFileSystemPolicy) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DeleteFileSystemPolicy) 