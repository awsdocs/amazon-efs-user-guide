# ListTagsForResource<a name="API_ListTagsForResource"></a>

Lists all tags for a top\-level EFS resource\. You must provide the ID of the resource that you want to retrieve the tags for\.

This operation requires permissions for the `elasticfilesystem:DescribeAccessPoints` action\.

## Request Syntax<a name="API_ListTagsForResource_RequestSyntax"></a>

```
GET /2015-02-01/resource-tags/ResourceId?MaxResults=MaxResults&NextToken=NextToken HTTP/1.1
```

## URI Request Parameters<a name="API_ListTagsForResource_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [MaxResults](#API_ListTagsForResource_RequestSyntax) **   <a name="efs-ListTagsForResource-request-MaxResults"></a>
\(Optional\) Specifies the maximum number of tag objects to return in the response\. The default value is 100\.  
Valid Range: Minimum value of 1\.

 ** [NextToken](#API_ListTagsForResource_RequestSyntax) **   <a name="efs-ListTagsForResource-request-NextToken"></a>
You can use `NextToken` in a subsequent request to fetch the next page of access point descriptions if the response payload was paginated\.

 ** [ResourceId](#API_ListTagsForResource_RequestSyntax) **   <a name="efs-ListTagsForResource-request-ResourceId"></a>
Specifies the EFS resource you want to retrieve tags for\. You can retrieve tags for EFS file systems and access points using this API endpoint\.  
Required: Yes

## Request Body<a name="API_ListTagsForResource_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_ListTagsForResource_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "NextToken": "string",
   "Tags": [ 
      { 
         "Key": "string",
         "Value": "string"
      }
   ]
}
```

## Response Elements<a name="API_ListTagsForResource_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [NextToken](#API_ListTagsForResource_ResponseSyntax) **   <a name="efs-ListTagsForResource-response-NextToken"></a>
 `NextToken` is present if the response payload is paginated\. You can use `NextToken` in a subsequent request to fetch the next page of access point descriptions\.  
Type: String

 ** [Tags](#API_ListTagsForResource_ResponseSyntax) **   <a name="efs-ListTagsForResource-response-Tags"></a>
An array of the tags for the specified EFS resource\.  
Type: Array of [Tag](API_Tag.md) objects

## Errors<a name="API_ListTagsForResource_Errors"></a>

 **AccessPointNotFound**   
Returned if the specified `AccessPointId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## See Also<a name="API_ListTagsForResource_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/ListTagsForResource) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/ListTagsForResource) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/ListTagsForResource) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/ListTagsForResource) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/ListTagsForResource) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/ListTagsForResource) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/ListTagsForResource) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/ListTagsForResource) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/ListTagsForResource) 