# LifecyclePolicy<a name="API_LifecyclePolicy"></a>

Describes a policy used by EFS lifecycle management to transition files to the Infrequent Access \(IA\) storage class\.

## Contents<a name="API_LifecyclePolicy_Contents"></a>

 **TransitionToIA**   <a name="efs-Type-LifecyclePolicy-TransitionToIA"></a>
A value that indicates how long it takes to transition files to the IA storage class\. Currently, the only valid value is `AFTER_30_DAYS`\.  
 `AFTER_30_DAYS` indicates files that have not been read from or written to for 30 days are transitioned from the Standard storage class to the IA storage class\. Metadata operations such as listing the contents of a directory don't count as a file access event\.  
Type: String  
Valid Values:` AFTER_30_DAYS`   
Required: No

## See Also<a name="API_LifecyclePolicy_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Go \- Pilot](https://docs.aws.amazon.com/goto/SdkForGoPilot/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Ruby V2](https://docs.aws.amazon.com/goto/SdkForRubyV2/elasticfilesystem-2015-02-01/LifecyclePolicy) 