# CreateTags<a name="API_CreateTags"></a>

Creates or overwrites tags associated with a file system\. Each tag is a key\-value pair\. If a tag key specified in the request already exists on the file system, this operation overwrites its value with the value provided in the request\. If you add the `Name` tag to your file system, Amazon EFS returns it in the response to the [DescribeFileSystems](API_DescribeFileSystems.md) operation\. 

This operation requires permission for the `elasticfilesystem:CreateTags` action\.

## Request Syntax<a name="API_CreateTags_RequestSyntax"></a>

```
POST /2015-02-01/create-tags/FileSystemId HTTP/1.1
Content-type: application/json

{
   "Tags": [ 
      { 
         "Key": "string",
         "Value": "string"
      }
   ]
}
```

## URI Request Parameters<a name="API_CreateTags_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_CreateTags_RequestSyntax) **   <a name="efs-CreateTags-request-FileSystemId"></a>
The ID of the file system whose tags you want to modify \(String\)\. This operation modifies the tags only, not the file system\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_CreateTags_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [Tags](#API_CreateTags_RequestSyntax) **   <a name="efs-CreateTags-request-Tags"></a>
An array of `Tag` objects to add\. Each `Tag` object is a key\-value pair\.   
Type: Array of [Tag](API_Tag.md) objects  
Required: Yes

## Response Syntax<a name="API_CreateTags_ResponseSyntax"></a>

```
HTTP/1.1 204
```

## Response Elements<a name="API_CreateTags_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 204 response with an empty HTTP body\.

## Errors<a name="API_CreateTags_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## Example<a name="API_CreateTags_Examples"></a>

### Create Tags on a File System<a name="API_CreateTags_Example_1"></a>

The following request creates three tags \(`"key1"`, `"key2"`, and `"key3"`\) on the specified file system\.

#### Sample Request<a name="API_CreateTags_Example_1_Request"></a>

```
POST /2015-02-01/create-tags/fs-01234567 HTTP/1.1 
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140620T221118Z
Authorization: <...>
Content-Type: application/json
Content-Length: 160

{
    "Tags": [
        {
            "Value": "value1",
            "Key": "key1"
        },
        {
            "Value": "value2",
            "Key": "key2"
        },
        {
            "Value": "value3",
            "Key": "key3"
        }
    ]
}
```

#### Sample Response<a name="API_CreateTags_Example_1_Response"></a>

```
HTTP/1.1 204 no content
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
```

## See Also<a name="API_CreateTags_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/CreateTags) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/CreateTags) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/CreateTags) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/CreateTags) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/CreateTags) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/CreateTags) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/CreateTags) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/CreateTags) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/CreateTags) 