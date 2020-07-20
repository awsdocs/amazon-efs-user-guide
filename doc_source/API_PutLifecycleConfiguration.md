# PutLifecycleConfiguration<a name="API_PutLifecycleConfiguration"></a>

Enables lifecycle management by creating a new `LifecycleConfiguration` object\. A `LifecycleConfiguration` object defines when files in an Amazon EFS file system are automatically transitioned to the lower\-cost EFS Infrequent Access \(IA\) storage class\. A `LifecycleConfiguration` applies to all files in a file system\.

Each Amazon EFS file system supports one lifecycle configuration, which applies to all files in the file system\. If a `LifecycleConfiguration` object already exists for the specified file system, a `PutLifecycleConfiguration` call modifies the existing configuration\. A `PutLifecycleConfiguration` call with an empty `LifecyclePolicies` array in the request body deletes any existing `LifecycleConfiguration` and disables lifecycle management\.

In the request, specify the following: 
+ The ID for the file system for which you are enabling, disabling, or modifying lifecycle management\.
+ A `LifecyclePolicies` array of `LifecyclePolicy` objects that define when files are moved to the IA storage class\. The array can contain only one `LifecyclePolicy` item\.

This operation requires permissions for the `elasticfilesystem:PutLifecycleConfiguration` operation\.

To apply a `LifecycleConfiguration` object to an encrypted file system, you need the same AWS Key Management Service \(AWS KMS\) permissions as when you created the encrypted file system\. 

## Request Syntax<a name="API_PutLifecycleConfiguration_RequestSyntax"></a>

```
PUT /2015-02-01/file-systems/FileSystemId/lifecycle-configuration HTTP/1.1
Content-type: application/json

{
   "LifecyclePolicies": [ 
      { 
         "TransitionToIA": "string"
      }
   ]
}
```

## URI Request Parameters<a name="API_PutLifecycleConfiguration_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_PutLifecycleConfiguration_RequestSyntax) **   <a name="efs-PutLifecycleConfiguration-request-FileSystemId"></a>
The ID of the file system for which you are creating the `LifecycleConfiguration` object \(String\)\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_PutLifecycleConfiguration_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [LifecyclePolicies](#API_PutLifecycleConfiguration_RequestSyntax) **   <a name="efs-PutLifecycleConfiguration-request-LifecyclePolicies"></a>
An array of `LifecyclePolicy` objects that define the file system's `LifecycleConfiguration` object\. A `LifecycleConfiguration` object tells lifecycle management when to transition files from the Standard storage class to the Infrequent Access storage class\.  
Type: Array of [LifecyclePolicy](API_LifecyclePolicy.md) objects  
Required: Yes

## Response Syntax<a name="API_PutLifecycleConfiguration_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "LifecyclePolicies": [ 
      { 
         "TransitionToIA": "string"
      }
   ]
}
```

## Response Elements<a name="API_PutLifecycleConfiguration_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [LifecyclePolicies](#API_PutLifecycleConfiguration_ResponseSyntax) **   <a name="efs-PutLifecycleConfiguration-response-LifecyclePolicies"></a>
An array of lifecycle management policies\. Currently, EFS supports a maximum of one policy per file system\.  
Type: Array of [LifecyclePolicy](API_LifecyclePolicy.md) objects

## Errors<a name="API_PutLifecycleConfiguration_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **IncorrectFileSystemLifeCycleState**   
Returned if the file system's lifecycle state is not "available"\.  
HTTP Status Code: 409

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## Examples<a name="API_PutLifecycleConfiguration_Examples"></a>

### Create a Lifecycle Configuration<a name="API_PutLifecycleConfiguration_Example_1"></a>

The following example creates a `LifecyclePolicy` object using the PutLifecycleConfiguration operation\. This object tells EFS lifecycle management to move all files in the file system that haven't been accessed in the last 14 days to the IA storage class\. This is the only lifecycle policy that is currently supported\. To learn more, see [EFS Lifecycle Management](https://docs.aws.amazon.com/efs/latest/ug/lifecycle-management-efs.html)\.

#### Sample Request<a name="API_PutLifecycleConfiguration_Example_1_Request"></a>

```
PUT /2015-02-01/file-systems/fs-01234567/lifecycle-configuration HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20181122T232908Z
Authorization: <...>
Content-type: application/json
Content-Length: 86

{
   "LifecyclePolicies": [
      {
         "TransitionToIA": "AFTER_14_DAYS"
      }
   ]
}
```

#### Sample Response<a name="API_PutLifecycleConfiguration_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-type: application/json
Content-Length: 86

{
   "LifecyclePolicies": [
      {
         "TransitionToIA": "AFTER_14_DAYS"
      }
   ]
}
```

### Disable Lifecycle Management<a name="API_PutLifecycleConfiguration_Example_2"></a>

The following example disables lifecycle management for the specified file system\.

#### Sample Request<a name="API_PutLifecycleConfiguration_Example_2_Request"></a>

```
PUT /2015-02-01/file-systems/fs-01234567/lifecycle-configuration HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20181122T232908Z
Authorization: <...>
Content-type: application/json
Content-Length: 86

{
   "LifecyclePolicies": [ ]
}
```

#### Sample Response<a name="API_PutLifecycleConfiguration_Example_2_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-type: application/json
Content-Length: 86

{
   "LifecyclePolicies": [ ]
}
```

## See Also<a name="API_PutLifecycleConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 