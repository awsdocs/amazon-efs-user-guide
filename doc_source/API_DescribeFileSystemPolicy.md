# DescribeFileSystemPolicy<a name="API_DescribeFileSystemPolicy"></a>

Returns the `FileSystemPolicy` for the specified EFS file system\.

This operation requires permissions for the `elasticfilesystem:DescribeFileSystemPolicy` action\.

## Request Syntax<a name="API_DescribeFileSystemPolicy_RequestSyntax"></a>

```
GET /2015-02-01/file-systems/FileSystemId/policy HTTP/1.1
```

## URI Request Parameters<a name="API_DescribeFileSystemPolicy_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_DescribeFileSystemPolicy_RequestSyntax) **   <a name="efs-DescribeFileSystemPolicy-request-FileSystemId"></a>
Specifies which EFS file system to retrieve the `FileSystemPolicy` for\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_DescribeFileSystemPolicy_RequestBody"></a>

The request does not have a request body\.

## Response Syntax<a name="API_DescribeFileSystemPolicy_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "FileSystemId": "string",
   "Policy": "string"
}
```

## Response Elements<a name="API_DescribeFileSystemPolicy_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [FileSystemId](#API_DescribeFileSystemPolicy_ResponseSyntax) **   <a name="efs-DescribeFileSystemPolicy-response-FileSystemId"></a>
Specifies the EFS file system to which the `FileSystemPolicy` applies\.  
Type: String  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$` 

 ** [Policy](#API_DescribeFileSystemPolicy_ResponseSyntax) **   <a name="efs-DescribeFileSystemPolicy-response-Policy"></a>
The JSON formatted `FileSystemPolicy` for the EFS file system\.  
Type: String

## Errors<a name="API_DescribeFileSystemPolicy_Errors"></a>

 **FileSystemNotFound**   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 **InternalServerError**   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

 **PolicyNotFound**   
Returned if the default file system policy is in effect for the EFS file system specified\.  
HTTP Status Code: 404

## Example<a name="API_DescribeFileSystemPolicy_Examples"></a>

### <a name="API_DescribeFileSystemPolicy_Example_1"></a>

#### Sample Request<a name="API_DescribeFileSystemPolicy_Example_1_Request"></a>

```
GET /2015-02-01/file-systems/fs-01234567/policy HTTP/1.1
```

#### Sample Response<a name="API_DescribeFileSystemPolicy_Example_1_Response"></a>

```
{
    "FileSystemId": "fs-01234567",
    "Policy": "{
        "Version": "2012-10-17",
        "Id": "efs-policy-wizard-cdef0123-aaaa-6666-5555-444455556666",
        "Statement": [ 
            {
                "Sid": "efs-statement-abcdef01-1111-bbbb-2222-111122224444",
                "Effect" : "Deny",
                "Principal": {
                "AWS": "*"
                },
                "Action": "*",
                "Resource": "arn:aws:elasticfilesystem:us-east-2:111122223333:file-system/fs-01234567",
                "Condition": {
                "Bool": {
                    "aws:SecureTransport": "false"
                    } 
                }
            }, 
            {
                "Sid": "efs-statement-01234567-aaaa-3333-4444-111122223333",
                "Effect": "Allow",
                "Principal": {
                    "AWS": "*"
                },
                "Action": [
                    "elasticfilesystem:ClientMount", 
                    "elasticfilesystem:ClientWrite" 
                ],
                "Resource" : "arn:aws:elasticfilesystem:us-east-2:111122223333:file-system/fs-01234567"
            }
        ]
    }
}
```

## See Also<a name="API_DescribeFileSystemPolicy_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeFileSystemPolicy) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeFileSystemPolicy) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeFileSystemPolicy) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeFileSystemPolicy) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/DescribeFileSystemPolicy) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeFileSystemPolicy) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeFileSystemPolicy) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeFileSystemPolicy) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeFileSystemPolicy) 