# DescribeMountTargets<a name="API_DescribeMountTargets"></a>

Returns the descriptions of all the current mount targets, or a specific mount target, for a file system\. When requesting all of the current mount targets, the order of mount targets returned in the response is unspecified\.

This operation requires permissions for the `elasticfilesystem:DescribeMountTargets` action, on either the file system ID that you specify in `FileSystemId`, or on the file system of the mount target that you specify in `MountTargetId`\.

## Request Syntax<a name="API_DescribeMountTargets_RequestSyntax"></a>

```
GET /2015-02-01/mount-targets?AccessPointId=AccessPointId&FileSystemId=FileSystemId&Marker=Marker&MaxItems=MaxItems&MountTargetId=MountTargetId HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeMountTargets_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [AccessPointId](#API_DescribeMountTargets_RequestSyntax) **   <a name="efs-DescribeMountTargets-request-AccessPointId"></a>
\(Optional\) The ID of the access point whose mount targets that you want to list\. It must be included in your request if a `FileSystemId` or `MountTargetId` is not included in your request\. Accepts either an access point ID or ARN as input\.

 ** [FileSystemId](#API_DescribeMountTargets_RequestSyntax) **   <a name="efs-DescribeMountTargets-request-FileSystemId"></a>
\(Optional\) ID of the file system whose mount targets you want to list \(String\)\. It must be included in your request if an `AccessPointId` or `MountTargetId` is not included\. Accepts either a file system ID or ARN as input\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [Marker](#API_DescribeMountTargets_RequestSyntax) **   <a name="efs-DescribeMountTargets-request-Marker"></a>
\(Optional\) Opaque pagination token returned from a previous `DescribeMountTargets` operation \(String\)\. If present, it specifies to continue the list from where the previous returning call left off\.  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

 ** [MaxItems](#API_DescribeMountTargets_RequestSyntax) **   <a name="efs-DescribeMountTargets-request-MaxItems"></a>
\(Optional\) Maximum number of mount targets to return in the response\. Currently, this number is automatically set to 10, and other values are ignored\. The response is paginated at 100 per page if you have more than 100 mount targets\.  
Valid Range: Minimum value of 1\.

 ** [MountTargetId](#API_DescribeMountTargets_RequestSyntax) **   <a name="efs-DescribeMountTargets-request-MountTargetId"></a>
\(Optional\) ID of the mount target that you want to have described \(String\)\. It must be included in your request if `FileSystemId` is not included\. Accepts either a mount target ID or ARN as input\.  
Length Constraints: Minimum length of 13\. Maximum length of 45\.  
Pattern: `^fsmt-[0-9a-f]{8,40}$` 

## Request Body<a name="API_DescribeMountTargets_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeMountTargets_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "Marker": "string",
   "MountTargets": [ 
      { 
         "AvailabilityZoneId": "string",
         "AvailabilityZoneName": "string",
         "FileSystemId": "string",
         "IpAddress": "string",
         "LifeCycleState": "string",
         "MountTargetId": "string",
         "NetworkInterfaceId": "string",
         "OwnerId": "string",
         "SubnetId": "string",
         "VpcId": "string"
      }
   ],
   "NextMarker": "string"
}
```

## Response Elements<a name="API_DescribeMountTargets_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [Marker](#API_DescribeMountTargets_ResponseSyntax) **   <a name="efs-DescribeMountTargets-response-Marker"></a>
If the request included the `Marker`, the response returns that value in this field\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

 ** [MountTargets](#API_DescribeMountTargets_ResponseSyntax) **   <a name="efs-DescribeMountTargets-response-MountTargets"></a>
Returns the file system's mount targets as an array of `MountTargetDescription` objects\.  
Type: Array of [MountTargetDescription](API_MountTargetDescription.md) objects

 ** [NextMarker](#API_DescribeMountTargets_ResponseSyntax) **   <a name="efs-DescribeMountTargets-response-NextMarker"></a>
If a value is present, there are more mount targets to return\. In a subsequent request, you can provide `Marker` in your request with this value to retrieve the next set of mount targets\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

## Errors<a name="API_DescribeMountTargets_Errors"></a>

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

 **MountTargetNotFound**   
Returned if there is no mount target with the specified ID found in the caller's account\.  
HTTP Status Code: 404

## Example<a name="API_DescribeMountTargets_Examples"></a>

### Retrieve Descriptions of Mount Targets Created for a File System<a name="API_DescribeMountTargets_Example_1"></a>

The following request retrieves descriptions of mount targets created for the specified file system\. 

#### Sample Request<a name="API_DescribeMountTargets_Example_1_Request"></a>

```
GET /2015-02-01/mount-targets?FileSystemId=fs-01234567 HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140622T191252Z
Authorization: <...>
```

#### Sample Response<a name="API_DescribeMountTargets_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-Type: application/json
Content-Length: 357

{
   "MountTargets":[
      {
         "OwnerId":"251839141158",
         "MountTargetId":"fsmt-01234567",
         "FileSystemId":"fs-01234567",
         "SubnetId":"subnet-01234567",
         "LifeCycleState":"added",
         "IpAddress":"10.0.2.42",
         "NetworkInterfaceId":"eni-1bcb7772"
      }
   ]
}
```

## See Also<a name="API_DescribeMountTargets_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeMountTargets) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeMountTargets) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeMountTargets) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeMountTargets) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DescribeMountTargets) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeMountTargets) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeMountTargets) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeMountTargets) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeMountTargets) 