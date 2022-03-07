# Destination<a name="API_Destination"></a>

Describes the destination file system in the replication configuration\.

## Contents<a name="API_Destination_Contents"></a>

 ** FileSystemId **   <a name="efs-Type-Destination-FileSystemId"></a>
The ID of the destination Amazon EFS file system\.  
Type: String  
Length Constraints: Maximum length of 128\.  
Pattern: `^(arn:aws[-a-z]*:elasticfilesystem:[0-9a-z-:]+:file-system/fs-[0-9a-f]{8,40}|fs-[0-9a-f]{8,40})$`   
Required: Yes

 ** LastReplicatedTimestamp **   <a name="efs-Type-Destination-LastReplicatedTimestamp"></a>
The time when the most recent sync was successfully completed on the destination file system\. Any changes to data on the source file system that occurred before this time have been successfully replicated to the destination file system\. Any changes that occurred after this time might not be fully replicated\.  
Type: Timestamp  
Required: No

 ** Region **   <a name="efs-Type-Destination-Region"></a>
The AWS Region in which the destination file system is located\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `^[a-z]{2}-((iso[a-z]{0,1}-)|(gov-)){0,1}[a-z]+-{0,1}[0-9]{0,1}$`   
Required: Yes

 ** Status **   <a name="efs-Type-Destination-Status"></a>
Describes the status of the destination Amazon EFS file system\. If the status is `ERROR`, the destination file system in the replication configuration is in a failed state and is unrecoverable\. To access the file system data, restore a backup of the failed file system to a new file system\.  
Type: String  
Valid Values:` ENABLED | ENABLING | DELETING | ERROR`   
Required: Yes

## See Also<a name="API_Destination_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/Destination) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/Destination) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/Destination) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/Destination) 