# DeleteFileSystem<a name="API_DeleteFileSystem"></a>

Deletes a file system, permanently severing access to its contents\. Upon return, the file system no longer exists and you can't access any contents of the deleted file system\.

 You can't delete a file system that is in use\. That is, if the file system has any mount targets, you must first delete them\. For more information, see [DescribeMountTargets](API_DescribeMountTargets.md) and [DeleteMountTarget](API_DeleteMountTarget.md)\. 

**Note**  
The `DeleteFileSystem` call returns while the file system state is still `deleting`\. You can check the file system deletion status by calling the [DescribeFileSystems](API_DescribeFileSystems.md) operation, which returns a list of file systems in your account\. If you pass file system ID or creation token for the deleted file system, the [DescribeFileSystems](API_DescribeFileSystems.md) returns a `404 FileSystemNotFound` error\.

This operation requires permissions for the `elasticfilesystem:DeleteFileSystem` action\.

## Request Syntax<a name="API_DeleteFileSystem_RequestSyntax"></a>

```
DELETE /2015-02-01/file-systems/FileSystemId HTTP/1.1
```

## URI Request Parameters<a name="API_DeleteFileSystem_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_DeleteFileSystem_RequestSyntax) **   <a name="efs-DeleteFileSystem-request-FileSystemId"></a>
The ID of the file system you want to delete\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_DeleteFileSystem_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DeleteFileSystem_ResponseSyntax"></a>

```
HTTP/1.1 204
```

## Response Elements<a name="API_DeleteFileSystem_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 204 response with an empty HTTP body\.

## Errors<a name="API_DeleteFileSystem_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemInUse**   
Returned if a file system has mount targets\.  
HTTP Status Code: 409

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## Example<a name="API_DeleteFileSystem_Examples"></a>

### Delete a File System<a name="API_DeleteFileSystem_Example_1"></a>

 The following example sends a DELETE request to the `file-systems` endpoint \(`elasticfilesystem.us-west-2.amazonaws.com/2015-02-01/file-systems/fs-01234567`\) to delete a file system whose ID is `fs-01234567`\. 

#### Sample Request<a name="API_DeleteFileSystem_Example_1_Request"></a>

```
DELETE /2015-02-01/file-systems/fs-01234567 HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140622T233021Z
Authorization: <...>
```

#### Sample Response<a name="API_DeleteFileSystem_Example_1_Response"></a>

```
HTTP/1.1 204 No Content
x-amzn-RequestId: a2d125b3-7ebd-4d6a-ab3d-5548630bff33
Content-Length: 0
```

## See Also<a name="API_DeleteFileSystem_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DeleteFileSystem) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DeleteFileSystem) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DeleteFileSystem) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DeleteFileSystem) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DeleteFileSystem) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DeleteFileSystem) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DeleteFileSystem) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DeleteFileSystem) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DeleteFileSystem) 