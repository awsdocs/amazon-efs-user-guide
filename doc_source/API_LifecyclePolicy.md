# LifecyclePolicy<a name="API_LifecyclePolicy"></a>

Describes a policy used by EFS lifecycle management and EFS Intelligent\-Tiering that specifies when to transition files into and out of the file system's Infrequent Access \(IA\) storage class\. For more information, see [EFS Intelligent‐Tiering and EFS Lifecycle Management](https://docs.aws.amazon.com/efs/latest/ug/lifecycle-management-efs.html)\.

**Note**  
When using the `put-lifecycle-configuration` CLI command or the `PutLifecycleConfiguration` API action, Amazon EFS requires that each `LifecyclePolicy` object have only a single transition\. This means that in a request body, `LifecyclePolicies` must be structured as an array of `LifecyclePolicy` objects, one object for each transition, `TransitionToIA`, `TransitionToPrimaryStorageClass`\. For more information, see the request examples in [PutLifecycleConfiguration](API_PutLifecycleConfiguration.md)\.

## Contents<a name="API_LifecyclePolicy_Contents"></a>

 ** TransitionToIA **   <a name="efs-Type-LifecyclePolicy-TransitionToIA"></a>
 Describes the period of time that a file is not accessed, after which it transitions to IA storage\. Metadata operations such as listing the contents of a directory don't count as file access events\.  
Type: String  
Valid Values:` AFTER_7_DAYS | AFTER_14_DAYS | AFTER_30_DAYS | AFTER_60_DAYS | AFTER_90_DAYS`   
Required: No

 ** TransitionToPrimaryStorageClass **   <a name="efs-Type-LifecyclePolicy-TransitionToPrimaryStorageClass"></a>
Describes when to transition a file from IA storage to primary storage\. Metadata operations such as listing the contents of a directory don't count as file access events\.  
Type: String  
Valid Values:` AFTER_1_ACCESS`   
Required: No

## See Also<a name="API_LifecyclePolicy_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/LifecyclePolicy) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/LifecyclePolicy) 