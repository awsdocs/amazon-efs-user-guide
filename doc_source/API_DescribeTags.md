# DescribeTags<a name="API_DescribeTags"></a>

Returns the tags associated with a file system\. The order of tags returned in the response of one `DescribeTags` call and the order of tags returned across the responses of a multiple\-call iteration \(when using pagination\) is unspecified\. 

 This operation requires permissions for the `elasticfilesystem:DescribeTags` action\. 

## Request Syntax<a name="API_DescribeTags_RequestSyntax"></a>

```
GET /2015-02-01/tags/FileSystemId/?Marker=Marker&MaxItems=MaxItems HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeTags_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_DescribeTags_RequestSyntax) **   <a name="efs-DescribeTags-request-FileSystemId"></a>
The ID of the file system whose tag set you want to retrieve\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

 ** [Marker](#API_DescribeTags_RequestSyntax) **   <a name="efs-DescribeTags-request-Marker"></a>
\(Optional\) An opaque pagination token returned from a previous `DescribeTags` operation \(String\)\. If present, it specifies to continue the list from where the previous call left off\.  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

 ** [MaxItems](#API_DescribeTags_RequestSyntax) **   <a name="efs-DescribeTags-request-MaxItems"></a>
\(Optional\) The maximum number of file system tags to return in the response\. Currently, this number is automatically set to 100, and other values are ignored\. The response is paginated at 100 per page if you have more than 100 tags\.  
Valid Range: Minimum value of 1\.

## Request Body<a name="API_DescribeTags_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeTags_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "Marker": "string",
   "NextMarker": "string",
   "Tags": [ 
      { 
         "Key": "string",
         "Value": "string"
      }
   ]
}
```

## Response Elements<a name="API_DescribeTags_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [Marker](#API_DescribeTags_ResponseSyntax) **   <a name="efs-DescribeTags-response-Marker"></a>
If the request included a `Marker`, the response returns that value in this field\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

 ** [NextMarker](#API_DescribeTags_ResponseSyntax) **   <a name="efs-DescribeTags-response-NextMarker"></a>
If a value is present, there are more tags to return\. In a subsequent request, you can provide the value of `NextMarker` as the value of the `Marker` parameter in your next request to retrieve the next set of tags\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

 ** [Tags](#API_DescribeTags_ResponseSyntax) **   <a name="efs-DescribeTags-response-Tags"></a>
Returns tags associated with the file system as an array of `Tag` objects\.   
Type: Array of [Tag](API_Tag.md) objects

## Errors<a name="API_DescribeTags_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## Example<a name="API_DescribeTags_Examples"></a>

### Retrieve Tags Associated with a File System<a name="API_DescribeTags_Example_1"></a>

 The following request retrieves tags \(key\-value pairs\) associated with the specified file system\. 

#### Sample Request<a name="API_DescribeTags_Example_1_Request"></a>

```
GET /2015-02-01/tags/fs-01234567/ HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140620T215404Z
Authorization: <...>
```

#### Sample Response<a name="API_DescribeTags_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-Type: application/json
Content-Length: 288

{
    "Tags":[
        {
            "Key":"Name",
            "Value":"my first file system"
        },
        {
            "Key":"Fleet",
            "Value":"Development"
        },
        {
            "Key":"Developer",
            "Value":"Alice"
        }
    ]
}
```

## See Also<a name="API_DescribeTags_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeTags) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeTags) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeTags) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeTags) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DescribeTags) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeTags) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeTags) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeTags) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeTags) 