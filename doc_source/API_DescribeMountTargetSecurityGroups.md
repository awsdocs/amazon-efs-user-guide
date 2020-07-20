# DescribeMountTargetSecurityGroups<a name="API_DescribeMountTargetSecurityGroups"></a>

Returns the security groups currently in effect for a mount target\. This operation requires that the network interface of the mount target has been created and the lifecycle state of the mount target is not `deleted`\.

This operation requires permissions for the following actions:
+  `elasticfilesystem:DescribeMountTargetSecurityGroups` action on the mount target's file system\. 
+  `ec2:DescribeNetworkInterfaceAttribute` action on the mount target's network interface\. 

## Request Syntax<a name="API_DescribeMountTargetSecurityGroups_RequestSyntax"></a>

```
GET /2015-02-01/mount-targets/MountTargetId/security-groups HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeMountTargetSecurityGroups_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [MountTargetId](#API_DescribeMountTargetSecurityGroups_RequestSyntax) **   <a name="efs-DescribeMountTargetSecurityGroups-request-MountTargetId"></a>
The ID of the mount target whose security groups you want to retrieve\.  
Length Constraints: Minimum length of 13\. Maximum length of 45\.  
Pattern: `^fsmt-[0-9a-f]{8,40}$`   
Required: Yes

## Request Body<a name="API_DescribeMountTargetSecurityGroups_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeMountTargetSecurityGroups_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "SecurityGroups": [ "string" ]
}
```

## Response Elements<a name="API_DescribeMountTargetSecurityGroups_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [SecurityGroups](#API_DescribeMountTargetSecurityGroups_ResponseSyntax) **   <a name="efs-DescribeMountTargetSecurityGroups-response-SecurityGroups"></a>
An array of security groups\.  
Type: Array of strings  
Array Members: Maximum number of 5 items\.  
Length Constraints: Minimum length of 11\. Maximum length of 43\.  
Pattern: `^sg-[0-9a-f]{8,40}` 

## Errors<a name="API_DescribeMountTargetSecurityGroups_Errors"></a>

 **BadRequest**   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 **IncorrectMountTargetState**   
Returned if the mount target is not in the correct state for the operation\.  
HTTP Status Code: 409

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 **MountTargetNotFound**   
Returned if there is no mount target with the specified ID found in the caller's account\.  
HTTP Status Code: 404

## Example<a name="API_DescribeMountTargetSecurityGroups_Examples"></a>

### Retrieve Security Groups in Effect for a File System<a name="API_DescribeMountTargetSecurityGroups_Example_1"></a>

 The following example retrieves the security groups that are in effect for the network interface associated with a mount target\. 

#### Sample Request<a name="API_DescribeMountTargetSecurityGroups_Example_1_Request"></a>

```
GET /2015-02-01/mount-targets/fsmt-9a13661e/security-groups HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20140620T223513Z
Authorization: <...>
```

#### Sample Response<a name="API_DescribeMountTargetSecurityGroups_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-Length: 57

{
"SecurityGroups" : [
"sg-188d9f74"
]
}
```

## See Also<a name="API_DescribeMountTargetSecurityGroups_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeMountTargetSecurityGroups) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeMountTargetSecurityGroups) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeMountTargetSecurityGroups) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeMountTargetSecurityGroups) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DescribeMountTargetSecurityGroups) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeMountTargetSecurityGroups) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeMountTargetSecurityGroups) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeMountTargetSecurityGroups) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeMountTargetSecurityGroups) 