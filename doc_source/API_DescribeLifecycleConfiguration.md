# DescribeLifecycleConfiguration<a name="API_DescribeLifecycleConfiguration"></a>

Returns the current `LifecycleConfiguration` object for the specified Amazon EFS file system\. EFS lifecycle management uses the `LifecycleConfiguration` object to identify which files to move to the EFS Infrequent Access \(IA\) storage class\. For a file system without a `LifecycleConfiguration` object, the call returns an empty array in the response\.

This operation requires permissions for the `elasticfilesystem:DescribeLifecycleConfiguration` operation\.

## Request Syntax<a name="API_DescribeLifecycleConfiguration_RequestSyntax"></a>

```
GET /2015-02-01/file-systems/FileSystemId/lifecycle-configuration HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeLifecycleConfiguration_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_DescribeLifecycleConfiguration_RequestSyntax) **   <a name="efs-DescribeLifecycleConfiguration-request-FileSystemId"></a>
The ID of the file system whose `LifecycleConfiguration` object you want to retrieve \(String\)\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_DescribeLifecycleConfiguration_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeLifecycleConfiguration_ResponseSyntax"></a>

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

## Response Elements<a name="API_DescribeLifecycleConfiguration_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [LifecyclePolicies](#API_DescribeLifecycleConfiguration_ResponseSyntax) **   <a name="efs-DescribeLifecycleConfiguration-response-LifecyclePolicies"></a>
An array of lifecycle management policies\. Currently, EFS supports a maximum of one policy per file system\.  
Type: Array of [LifecyclePolicy](API_LifecyclePolicy.md) objects

## Errors<a name="API_DescribeLifecycleConfiguration_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## Example<a name="API_DescribeLifecycleConfiguration_Examples"></a>

### Retrieve a Lifecycle Configuration for a File System<a name="API_DescribeLifecycleConfiguration_Example_1"></a>

The following request retrieves the `LifecycleConfiguration` object for the specified file system\.

#### Sample Request<a name="API_DescribeLifecycleConfiguration_Example_1_Request"></a>

```
GET /2015-02-01/file-systems/fs-01234567/lifecycle-configuration HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20181120T221118Z
Authorization: <...>
```

#### Sample Response<a name="API_DescribeLifecycleConfiguration_Example_1_Response"></a>

```
HTTP/1.1 200 OK
        x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
        Content-Type: application/json
        Content-Length: 86
{
  "LifecyclePolicies": [
    {
        "TransitionToIA": "AFTER_14_DAYS"
    }
  ]
}
```

## See Also<a name="API_DescribeLifecycleConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeLifecycleConfiguration) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeLifecycleConfiguration) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeLifecycleConfiguration) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeLifecycleConfiguration) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DescribeLifecycleConfiguration) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeLifecycleConfiguration) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeLifecycleConfiguration) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeLifecycleConfiguration) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeLifecycleConfiguration) 