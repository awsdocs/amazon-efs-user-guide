# LifecyclePolicy<a name="API_LifecyclePolicy"></a>

Describes a policy used by EFS lifecycle management to transition files to the Infrequent Access \(IA\) storage class\.

## Contents<a name="API_LifecyclePolicy_Contents"></a>

 **TransitionToIA**   <a name="efs-Type-LifecyclePolicy-TransitionToIA"></a>
 A value that describes the period of time that a file is not accessed, after which it transitions to the IA storage class\. Metadata operations such as listing the contents of a directory don't count as file access events\.  
Type: String  
Valid Values:` AFTER_7_DAYS | AFTER_14_DAYS | AFTER_30_DAYS | AFTER_60_DAYS | AFTER_90_DAYS`   
Required: No

## See Also<a name="API_LifecyclePolicy_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/LifecyclePolicy) 