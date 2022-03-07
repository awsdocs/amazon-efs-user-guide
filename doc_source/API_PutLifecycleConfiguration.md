# PutLifecycleConfiguration<a name="API_PutLifecycleConfiguration"></a>

Use this action to manage EFS lifecycle management and intelligent tiering\. A `LifecycleConfiguration` consists of one or more `LifecyclePolicy` objects that define the following:
+  **EFS Lifecycle management** \- When Amazon EFS automatically transitions files in a file system into the lower\-cost Infrequent Access \(IA\) storage class\.

  To enable EFS Lifecycle management, set the value of `TransitionToIA` to one of the available options\.
+  **EFS Intelligent tiering** \- When Amazon EFS automatically transitions files from IA back into the file system's primary storage class \(Standard or One Zone Standard\.

  To enable EFS Intelligent Tiering, set the value of `TransitionToPrimaryStorageClass` to `AFTER_1_ACCESS`\.

For more information, see [EFS Lifecycle Management](https://docs.aws.amazon.com/efs/latest/ug/lifecycle-management-efs.html)\.

Each Amazon EFS file system supports one lifecycle configuration, which applies to all files in the file system\. If a `LifecycleConfiguration` object already exists for the specified file system, a `PutLifecycleConfiguration` call modifies the existing configuration\. A `PutLifecycleConfiguration` call with an empty `LifecyclePolicies` array in the request body deletes any existing `LifecycleConfiguration` and turns off lifecycle management and intelligent tiering for the file system\.

In the request, specify the following: 
+ The ID for the file system for which you are enabling, disabling, or modifying lifecycle management and intelligent tiering\.
+ A `LifecyclePolicies` array of `LifecyclePolicy` objects that define when files are moved into IA storage, and when they are moved back to Standard storage\.
**Note**  
Amazon EFS requires that each `LifecyclePolicy` object have only have a single transition, so the `LifecyclePolicies` array needs to be structured with separate `LifecyclePolicy` objects\. See the example requests in the following section for more information\.

This operation requires permissions for the `elasticfilesystem:PutLifecycleConfiguration` operation\.

To apply a `LifecycleConfiguration` object to an encrypted file system, you need the same AWS Key Management Service permissions as when you created the encrypted file system\.

## Request Syntax<a name="API_PutLifecycleConfiguration_RequestSyntax"></a>

```
PUT /2015-02-01/file-systems/FileSystemId/lifecycle-configuration HTTP/1.1
Content-type: application/json

{
   "LifecyclePolicies": [ 
      { 
         "TransitionToIA": "string",
         "TransitionToPrimaryStorageClass": "string"
      }
   ]
}
```

## URI Request Parameters<a name="API_PutLifecycleConfiguration_RequestParameters"></a>

The request uses the following URI parameters\.

 ** [FileSystemId](#API_PutLifecycleConfiguration_RequestSyntax) **   <a name="efs-PutLifecycleConfiguration-request-FileSystemId"></a>
The ID of the file system for which you are creating the `LifecycleConfiguration` object \(String\)\.  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

## Request Body<a name="API_PutLifecycleConfiguration_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [LifecyclePolicies](#API_PutLifecycleConfiguration_RequestSyntax) **   <a name="efs-PutLifecycleConfiguration-request-LifecyclePolicies"></a>
An array of `LifecyclePolicy` objects that define the file system's `LifecycleConfiguration` object\. A `LifecycleConfiguration` object informs EFS lifecycle management and EFS Intelligent\-Tiering of the following:  
+ When to move files in the file system from primary storage to the IA storage class\.
+ When to move files that are in IA storage to primary storage\.
When using the `put-lifecycle-configuration` CLI command or the `PutLifecycleConfiguration` API action, Amazon EFS requires that each `LifecyclePolicy` object have only a single transition\. This means that in a request body, `LifecyclePolicies` must be structured as an array of `LifecyclePolicy` objects, one object for each transition, `TransitionToIA`, `TransitionToPrimaryStorageClass`\. See the example requests in the following section for more information\.
Type: Array of [LifecyclePolicy](API_LifecyclePolicy.md) objects  
Array Members: Maximum number of 2 items\.  
Required: Yes

## Response Syntax<a name="API_PutLifecycleConfiguration_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "LifecyclePolicies": [ 
      { 
         "TransitionToIA": "string",
         "TransitionToPrimaryStorageClass": "string"
      }
   ]
}
```

## Response Elements<a name="API_PutLifecycleConfiguration_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [LifecyclePolicies](#API_PutLifecycleConfiguration_ResponseSyntax) **   <a name="efs-PutLifecycleConfiguration-response-LifecyclePolicies"></a>
An array of lifecycle management policies\. EFS supports a maximum of one policy per file system\.  
Type: Array of [LifecyclePolicy](API_LifecyclePolicy.md) objects  
Array Members: Maximum number of 2 items\.

## Errors<a name="API_PutLifecycleConfiguration_Errors"></a>

 ** BadRequest **   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 ** FileSystemNotFound **   
Returned if the specified `FileSystemId` value doesn't exist in the requester's AWS account\.  
HTTP Status Code: 404

 ** IncorrectFileSystemLifeCycleState **   
Returned if the file system's lifecycle state is not "available"\.  
HTTP Status Code: 409

 ** InternalServerError **   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## Examples<a name="API_PutLifecycleConfiguration_Examples"></a>

### Create a lifecycle configuration<a name="API_PutLifecycleConfiguration_Example_1"></a>

The following example creates a `LifecyclePolicy` object using the `PutLifecycleConfiguration` action\. This example creates a lifecycle policy that instructs EFS lifecycle management to do the following:
+ Move all files in the file system that haven't been accessed in the last 30 days to the IA storage class\.
+ Move files that are in IA storage to primary storage \(Standard or One Zone Standard\) after they are first accessed\.

For more information, see [EFS storage classes](https://docs.aws.amazon.com/efs/latest/ug/storage-classes.html) and [EFS lifecycle management and intelligent tiering](https://docs.aws.amazon.com/efs/latest/ug/lifecycle-management-efs.html)\.

#### Sample Request<a name="API_PutLifecycleConfiguration_Example_1_Request"></a>

```
PUT /2015-02-01/file-systems/fs-0123456789abcdefb/lifecycle-configuration HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20181122T232908Z
Authorization: <...>
Content-type: application/json
Content-Length: 86

{
   "LifecyclePolicies": [
      {
         "TransitionToIA": "AFTER_60_DAYS"
      },
      {
         "TransitionToPrimaryStorage": "AFTER_1_ACCESS"
      }
   ]
}
```

#### Sample Response<a name="API_PutLifecycleConfiguration_Example_1_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-type: application/json
Content-Length: 86

{
    "LifecyclePolicies": [
        {
         "TransitionToIA": "AFTER_60_DAYS"
      },
      {
         "TransitionToPrimaryStorage": "AFTER_1_ACCESS"
      }
    ]
}
```

### Example put\-lifecycle\-configuration CLI request<a name="API_PutLifecycleConfiguration_Example_2"></a>

This example illustrates one usage of PutLifecycleConfiguration\.

#### Sample Request<a name="API_PutLifecycleConfiguration_Example_2_Request"></a>

```
aws efs put-lifecycle-configuration \
   --file-system-id fs-0123456789abcdefb \
   --lifecycle-policies "[{"TransitionToIA":"AFTER_30_DAYS"},
     {"TransitionToPrimaryStorageClass":"AFTER_1_ACCESS"}]"
   --region us-west-2 \
   --profile adminuser
```

#### Sample Response<a name="API_PutLifecycleConfiguration_Example_2_Response"></a>

```
{
   "LifecyclePolicies": [
       {
           "TransitionToIA": "AFTER_30_DAYS"
       },
       {
           "TransitionToPrimaryStorageClass": "AFTER_1_ACCESS"
       }
   ]
}
```

### Disable lifecycle management<a name="API_PutLifecycleConfiguration_Example_3"></a>

The following example disables lifecycle management for the specified file system\.

#### Sample Request<a name="API_PutLifecycleConfiguration_Example_3_Request"></a>

```
PUT /2015-02-01/file-systems/fs-01234567/lifecycle-configuration HTTP/1.1
Host: elasticfilesystem.us-west-2.amazonaws.com
x-amz-date: 20181122T232908Z
Authorization: <...>
Content-type: application/json
Content-Length: 86

{
   "LifecyclePolicies": [ ]
}
```

#### Sample Response<a name="API_PutLifecycleConfiguration_Example_3_Response"></a>

```
HTTP/1.1 200 OK
x-amzn-RequestId: 01234567-89ab-cdef-0123-456789abcdef
Content-type: application/json
Content-Length: 86

{
   "LifecyclePolicies": [ ]
}
```

## See Also<a name="API_PutLifecycleConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/PutLifecycleConfiguration) 