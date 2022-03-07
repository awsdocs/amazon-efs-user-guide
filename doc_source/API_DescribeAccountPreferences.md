# DescribeAccountPreferences<a name="API_DescribeAccountPreferences"></a>

Returns the account preferences settings for the AWS account associated with the user making the request, in the current AWS Region\. For more information, see [Managing Amazon EFS resource IDs](efs/latest/ug/manage-efs-resource-ids.html)\.

## Request Syntax<a name="API_DescribeAccountPreferences_RequestSyntax"></a>

```
GET /2015-02-01/account-preferences HTTP/1.1
Content-type: application/json

{
   "MaxResults": number,
   "NextToken": "string"
}
```

## URI Request Parameters<a name="API_DescribeAccountPreferences_RequestParameters"></a>

The request does not use any URI parameters\.

## Request Body<a name="API_DescribeAccountPreferences_RequestBody"></a>

The request accepts the following data in JSON format\.

 ** [MaxResults](#API_DescribeAccountPreferences_RequestSyntax) **   <a name="efs-DescribeAccountPreferences-request-MaxResults"></a>
\(Optional\) When retrieving account preferences, you can optionally specify the `MaxItems` parameter to limit the number of objects returned in a response\. The default value is 100\.   
Type: Integer  
Valid Range: Minimum value of 1\.  
Required: No

 ** [NextToken](#API_DescribeAccountPreferences_RequestSyntax) **   <a name="efs-DescribeAccountPreferences-request-NextToken"></a>
\(Optional\) You can use `NextToken` in a subsequent request to fetch the next page of AWS account preferences if the response payload was paginated\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+`   
Required: No

## Response Syntax<a name="API_DescribeAccountPreferences_ResponseSyntax"></a>

```
HTTP/1.1 200
Content-type: application/json

{
   "NextToken": "string",
   "ResourceIdPreference": { 
      "ResourceIdType": "string",
      "Resources": [ "string" ]
   }
}
```

## Response Elements<a name="API_DescribeAccountPreferences_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [NextToken](#API_DescribeAccountPreferences_ResponseSyntax) **   <a name="efs-DescribeAccountPreferences-response-NextToken"></a>
Present if there are more records than returned in the response\. You can use the `NextToken` in the subsequent request to fetch the additional descriptions\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `.+` 

 ** [ResourceIdPreference](#API_DescribeAccountPreferences_ResponseSyntax) **   <a name="efs-DescribeAccountPreferences-response-ResourceIdPreference"></a>
Describes the resource ID preference setting for the AWS account associated with the user making the request, in the current AWS Region\.  
Type: [ResourceIdPreference](API_ResourceIdPreference.md) object

## Errors<a name="API_DescribeAccountPreferences_Errors"></a>

 ** InternalServerError **   
Returned if an error occurred on the server side\.  
HTTP Status Code: 500

## See Also<a name="API_DescribeAccountPreferences_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/elasticfilesystem-2015-02-01/DescribeAccountPreferences) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/elasticfilesystem-2015-02-01/DescribeAccountPreferences) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DescribeAccountPreferences) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DescribeAccountPreferences) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/DescribeAccountPreferences) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/elasticfilesystem-2015-02-01/DescribeAccountPreferences) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/elasticfilesystem-2015-02-01/DescribeAccountPreferences) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/elasticfilesystem-2015-02-01/DescribeAccountPreferences) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DescribeAccountPreferences) 