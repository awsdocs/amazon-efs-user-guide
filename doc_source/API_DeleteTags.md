# DeleteTags<a name="API_DeleteTags"></a>

Deletes the specified tags from a file system\. If the `DeleteTags` request includes a tag key that does not exist, Amazon EFS ignores it and doesn't cause an error\. For more information about tags and related restrictions, see [Tag Restrictions](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\.

This operation requires permissions for the `elasticfilesystem:DeleteTags` action\.

## Request Syntax<a name="API_DeleteTags_RequestSyntax"></a>

```
POST /2015-02-01/delete-tags/FileSystemId HTTP/1.1
Content-type: application/json

{
   "[TagKeys](#efs-DeleteTags-request-TagKeys)": [ "string" ]
}
```

## URI Request Parameters<a name="API_DeleteTags_RequestParameters"></a>

The request requires the following URI parameters\.

 ** [FileSystemId](#API_DeleteTags_RequestSyntax) **   <a name="efs-DeleteTags-request-FileSystemId"></a>
ID of the file system whose tags you want to delete \(String\)\.

## Request Body<a name="API_DeleteTags_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [TagKeys](#API_DeleteTags_RequestSyntax) **   <a name="efs-DeleteTags-request-TagKeys"></a>
List of tag keys to delete\.  
Type: Array of strings  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Required: Yes

## Response Syntax<a name="API_DeleteTags_ResponseSyntax"></a>

```
HTTP/1.1 204
```

## Response Elements<a name="API_DeleteTags_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 204 response with an empty HTTP body\.

## Errors<a name="API_DeleteTags_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` does not exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## Example<a name="API_DeleteTags_Examples"></a>

### Delete tags from a file system<a name="API_DeleteTags_Example_1"></a>

 The following request deletes the tag `key2` from the tag set associated with the file system\. 

#### Sample Request<a name="API_DeleteTags_Example_1_Request"></a>

```
POST /2015-02-01/delete-tags/fs-e2a6438b HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140620T215123Z
Authorization: <...>
Content-Type: application/json
Content-Length: 223

{ 
    "TagKeys":[ 
        "key2"
    ]
}
```

#### Sample Response<a name="API_DeleteTags_Example_1_Response"></a>

```
HTTP/1.1 204 No Content
x-amzn-RequestId: ec08ae47-3409-49f3-9e90-64a5f981bb2b
```

## See Also<a name="API_DeleteTags_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DeleteTags) 
+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DeleteTags) 
+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DeleteTags) 
+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DeleteTags) 
+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DeleteTags) 
+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DeleteTags) 
+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DeleteTags) 
+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DeleteTags) 
+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/elasticfilesystem-2015-02-01/DeleteTags) 