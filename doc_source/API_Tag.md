# Tag<a name="API_Tag"></a>

A tag is a key\-value pair\. Allowed characters are letters, white space, and numbers that can be represented in UTF\-8, and the following characters:` + - = . _ : /` 

## Contents<a name="API_Tag_Contents"></a>

 **Key**   <a name="efs-Type-Tag-Key"></a>
The tag key \(String\)\. The key can't start with `aws:`\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `^(?![aA]{1}[wW]{1}[sS]{1}:)([\p{L}\p{Z}\p{N}_.:/=+\-@]+)$`   
Required: Yes

 **Value**   <a name="efs-Type-Tag-Value"></a>
The value of the tag key\.  
Type: String  
Length Constraints: Maximum length of 256\.  
Pattern: `^([\p{L}\p{Z}\p{N}_.:/=+\-@]*)$`   
Required: Yes

## See Also<a name="API_Tag_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/Tag) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/Tag) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/Tag) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/Tag) 