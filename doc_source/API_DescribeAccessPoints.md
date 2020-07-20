# DescribeAccessPoints<a name="API_DescribeAccessPoints"></a>

Returns the description of a specific Amazon EFS access point if the `AccessPointId` is provided\. If you provide an EFS `FileSystemId`, it returns descriptions of all access points for that file system\. You can provide either an `AccessPointId` or a `FileSystemId` in the request, but not both\. 

This operation requires permissions for the `elasticfilesystem:DescribeAccessPoints` action\.

## Request Syntax<a name="API_DescribeAccessPoints_RequestSyntax"></a>

```
GET /2015-02-01/access-points?AccessPointId=AccessPointId&FileSystemId=FileSystemId&MaxResults=MaxResults&NextToken=NextToken HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeAccessPoints_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [AccessPointId](#API_DescribeAccessPoints_RequestSyntax) **   <a name="efs-DescribeAccessPoints-request-AccessPointId"></a>
\(Optional\) Specifies an EFS access point to describe in the response; mutually exclusive with `FileSystemId`\.

 ** [FileSystemId](#API_DescribeAccessPoints_RequestSyntax) **   <a name="efs-DescribeAccessPoints-request-FileSystemId"></a>
\(Optional\) If you provide a `FileSystemId`, EFS returns all access points for that file system; mutually exclusive with `AccessPointId`\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [MaxResults](#API_DescribeAccessPoints_RequestSyntax) **   <a name="efs-DescribeAccessPoints-request-MaxResults"></a>
\(Optional\) When retrieving all access points for a file system, you can optionally specify the `MaxItems` parameter to limit the number of objects returned in a response\. The default value is 100\.   
Valid Range: Minimum value of 1\.

 ** [NextToken](#API_DescribeAccessPoints_RequestSyntax) **   <a name="efs-DescribeAccessPoints-request-NextToken"></a>
 `NextToken` is present if the response is paginated\. You can use `NextMarker` in the subsequent request to fetch the next page of access point descriptions\.

## Request Body<a name="API_DescribeAccessPoints_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeAccessPoints_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "AccessPoints": [ 
      { 
         "AccessPointArn": "string",
         "AccessPointId": "string",
         "ClientToken": "string",
         "FileSystemId": "string",
         "LifeCycleState": "string",
         "Name": "string",
         "OwnerId": "string",
         "PosixUser": { 
            "Gid": number,
            "SecondaryGids": [ number ],
            "Uid": number
         },
         "RootDirectory": { 
            "CreationInfo": { 
               "OwnerGid": number,
               "OwnerUid": number,
               "Permissions": "string"
            },
            "Path": "string"
         },
         "Tags": [ 
            { 
               "Key": "string",
               "Value": "string"
            }
         ]
      }
   ],
   "NextToken": "string"
}
```

## Response Elements<a name="API_DescribeAccessPoints_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [AccessPoints](#API_DescribeAccessPoints_ResponseSyntax) **   <a name="efs-DescribeAccessPoints-response-AccessPoints"></a>
An array of access point descriptions\.  
Type: Array of [AccessPointDescription](API_AccessPointDescription.md) objects

 ** [NextToken](#API_DescribeAccessPoints_ResponseSyntax) **   <a name="efs-DescribeAccessPoints-response-NextToken"></a>
Present if there are more access points than returned in the response\. You can use the NextMarker in the subsequent request to fetch the additional descriptions\.  
Type: String

## Errors<a name="API_DescribeAccessPoints_Errors"></a>

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

## See Also<a name="API_DescribeAccessPoints_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeAccessPoints) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeAccessPoints) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeAccessPoints) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeAccessPoints) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DescribeAccessPoints) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeAccessPoints) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeAccessPoints) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeAccessPoints) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeAccessPoints) 