# DescribeFileSystems<a name="API_DescribeFileSystems"></a>

Returns the description of a specific Amazon EFS file system if either the file system `CreationToken` or the `FileSystemId` is provided\. Otherwise, it returns descriptions of all file systems owned by the caller's AWS account in the AWS Region of the endpoint that you're calling\.

When retrieving all file system descriptions, you can optionally specify the `MaxItems` parameter to limit the number of descriptions in a response\. Currently, this number is automatically set to 10\. If more file system descriptions remain, Amazon EFS returns a `NextMarker`, an opaque token, in the response\. In this case, you should send a subsequent request with the `Marker` request parameter set to the value of `NextMarker`\. 

To retrieve a list of your file system descriptions, this operation is used in an iterative process, where `DescribeFileSystems` is called first without the `Marker` and then the operation continues to call it with the `Marker` parameter set to the value of the `NextMarker` from the previous response until the response has no `NextMarker`\. 

 The order of file systems returned in the response of one `DescribeFileSystems` call and the order of file systems returned across the responses of a multi\-call iteration is unspecified\. 

 This operation requires permissions for the `elasticfilesystem:DescribeFileSystems` action\. 

## Request Syntax<a name="API_DescribeFileSystems_RequestSyntax"></a>

```
GET /2015-02-01/file-systems?CreationToken=CreationToken&FileSystemId=FileSystemId&Marker=Marker&MaxItems=MaxItems HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeFileSystems_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [CreationToken](#API_DescribeFileSystems_RequestSyntax) **   <a name="efs-DescribeFileSystems-request-CreationToken"></a>
\(Optional\) Restricts the list to the file system with this creation token \(String\)\. You specify a creation token when you create an Amazon EFS file system\.  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `.+` 

 ** [FileSystemId](#API_DescribeFileSystems_RequestSyntax) **   <a name="efs-DescribeFileSystems-request-FileSystemId"></a>
\(Optional\) ID of the file system whose description you want to retrieve \(String\)\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [Marker](#API_DescribeFileSystems_RequestSyntax) **   <a name="efs-DescribeFileSystems-request-Marker"></a>
\(Optional\) Opaque pagination token returned from a previous `DescribeFileSystems` operation \(String\)\. If present, specifies to continue the list from where the returning call had left off\.   
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

 ** [MaxItems](#API_DescribeFileSystems_RequestSyntax) **   <a name="efs-DescribeFileSystems-request-MaxItems"></a>
\(Optional\) Specifies the maximum number of file systems to return in the response \(integer\)\. This number is automatically set to 100\. The response is paginated at 100 per page if you have more than 100 file systems\.   
Valid Range: Minimum value of 1\.

## Request Body<a name="API_DescribeFileSystems_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeFileSystems_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "FileSystems": [ 
      { 
         "CreationTime": number,
         "CreationToken": "string",
         "Encrypted": boolean,
         "FileSystemArn": "string",
         "FileSystemId": "string",
         "KmsKeyId": "string",
         "LifeCycleState": "string",
         "Name": "string",
         "NumberOfMountTargets": number,
         "OwnerId": "string",
         "PerformanceMode": "string",
         "ProvisionedThroughputInMibps": number,
         "SizeInBytes": { 
            "Timestamp": number,
            "Value": number,
            "ValueInIA": number,
            "ValueInStandard": number
         },
         "Tags": [ 
            { 
               "Key": "string",
               "Value": "string"
            }
         ],
         "ThroughputMode": "string"
      }
   ],
   "Marker": "string",
   "NextMarker": "string"
}
```

## Response Elements<a name="API_DescribeFileSystems_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [FileSystems](#API_DescribeFileSystems_ResponseSyntax) **   <a name="efs-DescribeFileSystems-response-FileSystems"></a>
An array of file system descriptions\.  
Type: Array of [FileSystemDescription](API_FileSystemDescription.md) objects

 ** [Marker](#API_DescribeFileSystems_ResponseSyntax) **   <a name="efs-DescribeFileSystems-response-Marker"></a>
Present if provided by caller in the request \(String\)\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

 ** [NextMarker](#API_DescribeFileSystems_ResponseSyntax) **   <a name="efs-DescribeFileSystems-response-NextMarker"></a>
Present if there are more file systems than returned in the response \(String\)\. You can use the `NextMarker` in the subsequent request to fetch the descriptions\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

## Errors<a name="API_DescribeFileSystems_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## Example<a name="API_DescribeFileSystems_Examples"></a>

### Retrieve a List of 10 File Systems<a name="API_DescribeFileSystems_Example_1"></a>

 The following example sends a GET request to the `file-systems` endpoint \(`elasticfilesystem.us-west-2.amazonaws.com/2015-02-01/file-systems`\)\. The request specifies a `MaxItems` query parameter to limit the number of file system descriptions to 10\.

#### Sample Request<a name="API_DescribeFileSystems_Example_1_Request"></a>

```
GET /2015-02-01/file-systems?MaxItems=10 HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140622T191208Z
Authorization: <...>
```

#### Sample Response<a name="API_DescribeFileSystems_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-Type: application/json
Content-Length: 499
{
   "FileSystems":[
      {
         "OwnerId":"251839141158",
         "CreationToken":"MyFileSystem1",
         "FileSystemId":"fs-01234567",
         "PerformanceMode" : "generalPurpose",
         "CreationTime":"1403301078",
         "LifeCycleState":"created",
         "Name":"my first file system",
         "NumberOfMountTargets":1,
         "SizeInBytes":{
            "Timestamp": 1403301078,
            "Value": 29313417216,
            "ValueInIA": 675432,
            "ValueInStandard": 29312741784
         }
      }
   ]
}
```

## See Also<a name="API_DescribeFileSystems_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeFileSystems) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeFileSystems) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeFileSystems) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeFileSystems) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DescribeFileSystems) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeFileSystems) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeFileSystems) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeFileSystems) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeFileSystems) 