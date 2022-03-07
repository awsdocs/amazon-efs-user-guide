# ResourceIdPreference<a name="API_ResourceIdPreference"></a>

Describes the resource type and its ID preference for the user's AWS account, in the current AWS Region\.

## Contents<a name="API_ResourceIdPreference_Contents"></a>

 ** ResourceIdType **   <a name="efs-Type-ResourceIdPreference-ResourceIdType"></a>
Identifies the EFS resource ID preference, either `LONG_ID` \(17 characters\) or `SHORT_ID` \(8 characters\)\.  
Type: String  
Valid Values:` LONG_ID | SHORT_ID`   
Required: No

 ** Resources **   <a name="efs-Type-ResourceIdPreference-Resources"></a>
Identifies the Amazon EFS resources to which the ID preference setting applies, `FILE_SYSTEM` and `MOUNT_TARGET`\.  
Type: Array of strings  
Valid Values:` FILE_SYSTEM | MOUNT_TARGET`   
Required: No

## See Also<a name="API_ResourceIdPreference_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/ResourceIdPreference) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/ResourceIdPreference) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/ResourceIdPreference) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/ResourceIdPreference) 