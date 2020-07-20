# DeleteMountTarget<a name="API_DeleteMountTarget"></a>

Deletes the specified mount target\.

This operation forcibly breaks any mounts of the file system by using the mount target that is being deleted, which might disrupt instances or applications using those mounts\. To avoid applications getting cut off abruptly, you might consider unmounting any mounts of the mount target, if feasible\. The operation also deletes the associated network interface\. Uncommitted writes might be lost, but breaking a mount target using this operation does not corrupt the file system itself\. The file system you created remains\. You can mount an EC2 instance in your VPC by using another mount target\.

This operation requires permissions for the following action on the file system:
+  `elasticfilesystem:DeleteMountTarget` 

**Note**  
The `DeleteMountTarget` call returns while the mount target state is still `deleting`\. You can check the mount target deletion by calling the [DescribeMountTargets](API_DescribeMountTargets.md) operation, which returns a list of mount target descriptions for the given file system\. 

The operation also requires permissions for the following Amazon EC2 action on the mount target's network interface:
+  `ec2:DeleteNetworkInterface` 

## Request Syntax<a name="API_DeleteMountTarget_RequestSyntax"></a>

```
DELETE /2015-02-01/mount-targets/MountTargetId HTTP/1.1
```

## URI Request Parameters<a name="API_DeleteMountTarget_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [MountTargetId](#API_DeleteMountTarget_RequestSyntax) **   <a name="efs-DeleteMountTarget-request-MountTargetId"></a>
The ID of the mount target to delete \(String\)\.  
Length Constraints: Minimum length of 13\. Maximum length of 45\.  
Pattern: `^fsmt-[0-9a-f]{8,40}$`   
Required: Yes

## Request Body<a name="API_DeleteMountTarget_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DeleteMountTarget_ResponseSyntax"></a>

```
HTTP/1.1 204
```

## Response Elements<a name="API_DeleteMountTarget_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 204 response with an empty HTTP body\.

## Errors<a name="API_DeleteMountTarget_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **DependencyTimeout**   
The service timed out trying to fulfill the request, and the client should try the call again\.  
HTTP Status Code: 504

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 **MountTargetNotFound**   
Returned if there is no mount target with the specified ID found in the caller's account\.  
HTTP Status Code: 404

## Example<a name="API_DeleteMountTarget_Examples"></a>

### Remove a file system's mount target<a name="API_DeleteMountTarget_Example_1"></a>

The following example sends a DELETE request to delete a specific mount target\. 

#### Sample Request<a name="API_DeleteMountTarget_Example_1_Request"></a>

```
DELETE /2015-02-01/mount-targets/fsmt-9a13661e HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140622T232908Z
Authorization: <...>
```

#### Sample Response<a name="API_DeleteMountTarget_Example_1_Response"></a>

```
HTTP/1.1 204 No Content
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
```

## See Also<a name="API_DeleteMountTarget_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DeleteMountTarget) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DeleteMountTarget) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DeleteMountTarget) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DeleteMountTarget) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DeleteMountTarget) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DeleteMountTarget) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DeleteMountTarget) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DeleteMountTarget) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DeleteMountTarget) 