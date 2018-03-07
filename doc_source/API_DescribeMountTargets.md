# DescribeMountTargets<a name="API_DescribeMountTargets"></a>

Returns the descriptions of all the current mount targets, or a specific mount target, for a file system\. When requesting all of the current mount targets, the order of mount targets returned in the response is unspecified\.

This operation requires permissions for the `elasticfilesystem:DescribeMountTargets` action, on either the file system ID that you specify in `FileSystemId`, or on the file system of the mount target that you specify in `MountTargetId`\.

## Request Syntax<a name="API_DescribeMountTargets_RequestSyntax"></a>

```
GET /2015-02-01/mount-targets?FileSystemId=FileSystemId&Marker=Marker&MaxItems=MaxItems&MountTargetId=MountTargetId HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeMountTargets_RequestParameters"></a>

The request requires the following URI parameters\.

 ** [FileSystemId](#API_DescribeMountTargets_RequestSyntax) **   <a name="efs-DescribeMountTargets-request-FileSystemId"></a>
\(Optional\) ID of the file system whose mount targets you want to list \(String\)\. It must be included in your request if `MountTargetId` is not included\.

 ** [Marker](#API_DescribeMountTargets_RequestSyntax) **   <a name="efs-DescribeMountTargets-request-Marker"></a>
\(Optional\) Opaque pagination token returned from a previous `DescribeMountTargets` operation \(String\)\. If present, it specifies to continue the list from where the previous returning call left off\.

 ** [MaxItems](#API_DescribeMountTargets_RequestSyntax) **   <a name="efs-DescribeMountTargets-request-MaxItems"></a>
\(Optional\) Maximum number of mount targets to return in the response\. It must be an integer with a value greater than zero\.  
Valid Range: Minimum value of 1\.

 ** [MountTargetId](#API_DescribeMountTargets_RequestSyntax) **   <a name="efs-DescribeMountTargets-request-MountTargetId"></a>
\(Optional\) ID of the mount target that you want to have described \(String\)\. It must be included in your request if `FileSystemId` is not included\.

## Request Body<a name="API_DescribeMountTargets_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeMountTargets_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "[Marker](#efs-DescribeMountTargets-response-Marker)": "string",
   "[MountTargets](#efs-DescribeMountTargets-response-MountTargets)": [ 
      { 
         "[FileSystemId](API_MountTargetDescription.md#efs-Type-MountTargetDescription-FileSystemId)": "string",
         "[IpAddress](API_MountTargetDescription.md#efs-Type-MountTargetDescription-IpAddress)": "string",
         "[LifeCycleState](API_MountTargetDescription.md#efs-Type-MountTargetDescription-LifeCycleState)": "string",
         "[MountTargetId](API_MountTargetDescription.md#efs-Type-MountTargetDescription-MountTargetId)": "string",
         "[NetworkInterfaceId](API_MountTargetDescription.md#efs-Type-MountTargetDescription-NetworkInterfaceId)": "string",
         "[OwnerId](API_MountTargetDescription.md#efs-Type-MountTargetDescription-OwnerId)": "string",
         "[SubnetId](API_MountTargetDescription.md#efs-Type-MountTargetDescription-SubnetId)": "string"
      }
   ],
   "[NextMarker](#efs-DescribeMountTargets-response-NextMarker)": "string"
}
```

## Response Elements<a name="API_DescribeMountTargets_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [Marker](#API_DescribeMountTargets_ResponseSyntax) **   <a name="efs-DescribeMountTargets-response-Marker"></a>
If the request included the `Marker`, the response returns that value in this field\.  
Type: String

 ** [MountTargets](#API_DescribeMountTargets_ResponseSyntax) **   <a name="efs-DescribeMountTargets-response-MountTargets"></a>
Returns the file system's mount targets as an array of `MountTargetDescription` objects\.  
Type: Array of [MountTargetDescription](API_MountTargetDescription.md) objects

 ** [NextMarker](#API_DescribeMountTargets_ResponseSyntax) **   <a name="efs-DescribeMountTargets-response-NextMarker"></a>
If a value is present, there are more mount targets to return\. In a subsequent request, you can provide `Marker` in your request with this value to retrieve the next set of mount targets\.  
Type: String

## Errors<a name="API_DescribeMountTargets_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` does not exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 **MountTargetNotFound**   
Returned if there is no mount target with the specified ID found in the caller's account\.  
HTTP Status Code: 404

## Example<a name="API_DescribeMountTargets_Examples"></a>

### Retrieve descriptions mount targets created for a file system<a name="API_DescribeMountTargets_Example_1"></a>

The following request retrieves descriptions of mount targets created for the specified file system\. 

#### Sample Request<a name="API_DescribeMountTargets_Example_1_Request"></a>

```
GET /2015-02-01/mount-targets?FileSystemId=fs-47a2c22e HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140622T191252Z
Authorization: <...>
```

#### Sample Response<a name="API_DescribeMountTargets_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: ab5f2427-3ab3-4002-868e-30a77a88f739
Content-Type: application/json
Content-Length: 357

{
   "MountTargets":[
      {
         "OwnerId":"251839141158",
         "MountTargetId":"fsmt-9a13661e",
         "FileSystemId":"fs-47a2c22e",
         "SubnetId":"subnet-fd04ff94",
         "LifeCycleState":"added",
         "IpAddress":"10.0.2.42",
         "NetworkInterfaceId":"eni-1bcb7772"
      }
   ]
}
```

## See Also<a name="API_DescribeMountTargets_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeMountTargets) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeMountTargets) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeMountTargets) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeMountTargets) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DescribeMountTargets) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeMountTargets) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeMountTargets) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeMountTargets) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/elasticfilesystem-2015-02-01/DescribeMountTargets) 