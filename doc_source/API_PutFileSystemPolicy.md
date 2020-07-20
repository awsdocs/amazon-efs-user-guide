# PutFileSystemPolicy<a name="API_PutFileSystemPolicy"></a>

Applies an Amazon EFS `FileSystemPolicy` to an Amazon EFS file system\. A file system policy is an IAM resource\-based policy and can contain multiple policy statements\. A file system always has exactly one file system policy, which can be the default policy or an explicit policy set or updated using this API operation\. When an explicit policy is set, it overrides the default policy\. For more information about the default file system policy, see [Default EFS File System Policy](https://docs.aws.amazon.com/efs/latest/ug/iam-access-control-nfs-efs.html#default-filesystempolicy)\. 

This operation requires permissions for the `elasticfilesystem:PutFileSystemPolicy` action\.

## Request Syntax<a name="API_PutFileSystemPolicy_RequestSyntax"></a>

```
PUT /2015-02-01/file-systems/FileSystemId/policy HTTP/1.1
Content-type: application/json

{
   "BypassPolicyLockoutSafetyCheck": boolean,
   "Policy": "string"
}
```

## URI Request Parameters<a name="API_PutFileSystemPolicy_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_PutFileSystemPolicy_RequestSyntax) **   <a name="efs-PutFileSystemPolicy-request-FileSystemId"></a>
The ID of the EFS file system that you want to create or update the `FileSystemPolicy` for\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_PutFileSystemPolicy_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [BypassPolicyLockoutSafetyCheck](#API_PutFileSystemPolicy_RequestSyntax) **   <a name="efs-PutFileSystemPolicy-request-BypassPolicyLockoutSafetyCheck"></a>
\(Optional\) A flag to indicate whether to bypass the `FileSystemPolicy` lockout safety check\. The policy lockout safety check determines whether the policy in the request will prevent the principal making the request will be locked out from making future `PutFileSystemPolicy` requests on the file system\. Set `BypassPolicyLockoutSafetyCheck` to `True` only when you intend to prevent the principal that is making the request from making a subsequent `PutFileSystemPolicy` request on the file system\. The default value is False\.   
Type: Boolean  
Required: No

 ** [Policy](#API_PutFileSystemPolicy_RequestSyntax) **   <a name="efs-PutFileSystemPolicy-request-Policy"></a>
The `FileSystemPolicy` that you're creating\. Accepts a JSON formatted policy definition\. To find out more about the elements that make up a file system policy, see [EFS Resource\-based Policies](https://docs.aws.amazon.com/efs/latest/ug/access-control-overview.html#access-control-manage-access-intro-resource-policies)\.   
Type: String  
Required: Yes

## Response Syntax<a name="API_PutFileSystemPolicy_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "FileSystemId": "string",
   "Policy": "string"
}
```

## Response Elements<a name="API_PutFileSystemPolicy_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [FileSystemId](#API_PutFileSystemPolicy_ResponseSyntax) **   <a name="efs-PutFileSystemPolicy-response-FileSystemId"></a>
Specifies the EFS file system to which the `FileSystemPolicy` applies\.  
Type: String  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [Policy](#API_PutFileSystemPolicy_ResponseSyntax) **   <a name="efs-PutFileSystemPolicy-response-Policy"></a>
The JSON formatted `FileSystemPolicy` for the EFS file system\.  
Type: String

## Errors<a name="API_PutFileSystemPolicy_Errors"></a>

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **IncorrectFileSystemLifeCycleState**   
Returned if the file system's lifecycle state is not "available"\.  
HTTP Status Code: 409

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 **InvalidPolicyException**   
Returned if the `FileSystemPolicy` is is malformed or contains an error such as an invalid parameter value or a missing required parameter\. Returned in the case of a policy lockout safety check error\.  
HTTP Status Code: 400

## Example<a name="API_PutFileSystemPolicy_Examples"></a>

### Create an EFS FileSystemPolicy<a name="API_PutFileSystemPolicy_Example_1"></a>

The following request creates a `FileSystemPolicy` that allows all AWS principals to mount the specified EFS file system with read and write permissions\.

#### Sample Request<a name="API_PutFileSystemPolicy_Example_1_Request"></a>

```
PUT /2015-02-01/file-systems/fs-01234567/file-system-policy HTTP/1.1
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "elasticfilesystem:ClientMount",
                "elasticfilesystem:ClientWrite"
            ],
            "Principal": {
                "AWS": ["*"]
            },
        }
    ]
}
```

#### Sample Response<a name="API_PutFileSystemPolicy_Example_1_Response"></a>

```
{
    "Version": "2012-10-17",
    "Id": "1",
    "Statement": [
        {
            "Sid": "efs-statement-abcdef01-1111-bbbb-2222-111122224444",
            "Effect": "Allow",
            "Action": [
                "elasticfilesystem:ClientMount",
                "elasticfilesystem:ClientWrite"
            ],
            "Principal": {
                "AWS": ["*"]
            },
            "Resource":"arn:aws:elasticfilesystem:us-east-1:0123456789abc:file-system/fs-01234567"
        }
    ]
}
```

## See Also<a name="API_PutFileSystemPolicy_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/PutFileSystemPolicy) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/PutFileSystemPolicy) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/PutFileSystemPolicy) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/PutFileSystemPolicy) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/PutFileSystemPolicy) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/PutFileSystemPolicy) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/PutFileSystemPolicy) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/PutFileSystemPolicy) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/PutFileSystemPolicy) 