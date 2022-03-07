# PutAccountPreferences<a name="API_PutAccountPreferences"></a>

Use this operation to set the account preference in the current AWS Region to use long 17 character \(63 bit\) or short 8 character \(32 bit\) resource IDs for new EFS file system and mount target resources\. All existing resource IDs are not affected by any changes you make\. You can set the ID preference during the opt\-in period as EFS transitions to long resource IDs\. For more information, see [Managing Amazon EFS resource IDs](https://docs.aws.amazon.com/efs/latest/ug/manage-efs-resource-ids.html)\.

**Note**  
Starting in October, 2021, you will receive an error if you try to set the account preference to use the short 8 character format resource ID\. Contact AWS support if you receive an error and must use short IDs for file system and mount target resources\.

## Request Syntax<a name="API_PutAccountPreferences_RequestSyntax"></a>

```
PUT /2015-02-01/account-preferences HTTP/1.1
Content-type: application/json

{
   "ResourceIdType": "string"
}
```

## URI Request Parameters<a name="API_PutAccountPreferences_RequestParameters"></a>

The request does not use any URI parameters\.

## Request Body<a name="API_PutAccountPreferences_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [ResourceIdType](#API_PutAccountPreferences_RequestSyntax) **   <a name="efs-PutAccountPreferences-request-ResourceIdType"></a>
Specifies the EFS resource ID preference to set for the user's AWS account, in the current AWS Region, either `LONG_ID` \(17 characters\), or `SHORT_ID` \(8 characters\)\.  
Starting in October, 2021, you will receive an error when setting the account preference to `SHORT_ID`\. Contact AWS support if you receive an error and must use short IDs for file system and mount target resources\.
Type: String  
Valid Values:` LONG_ID | SHORT_ID`   
Required: Yes

## Response Syntax<a name="API_PutAccountPreferences_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "ResourceIdPreference": { 
      "ResourceIdType": "string",
      "Resources": [ "string" ]
   }
}
```

## Response Elements<a name="API_PutAccountPreferences_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [ResourceIdPreference](#API_PutAccountPreferences_ResponseSyntax) **   <a name="efs-PutAccountPreferences-response-ResourceIdPreference"></a>
Describes the resource type and its ID preference for the user's AWS account, in the current AWS Region\.  
Type: [ResourceIdPreference](API_ResourceIdPreference.md) object

## Errors<a name="API_PutAccountPreferences_Errors"></a>

 ** BadRequest **   
Returned if the request is malformed or contains an error such as an invalid parameter value or a missing required parameter\.  
HTTP Status Code: 400

 ** InternalServerError **   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## See Also<a name="API_PutAccountPreferences_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/PutAccountPreferences) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/PutAccountPreferences) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/PutAccountPreferences) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/PutAccountPreferences) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/PutAccountPreferences) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/PutAccountPreferences) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/PutAccountPreferences) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/PutAccountPreferences) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/PutAccountPreferences) 