# DestinationToCreate<a name="API_DestinationToCreate"></a>

Describes the destination file system to create in the replication configuration\.

## Contents<a name="API_DestinationToCreate_Contents"></a>

 ** AvailabilityZoneName **   <a name="efs-Type-DestinationToCreate-AvailabilityZoneName"></a>
To create a file system that uses EFS One Zone storage, specify the name of the Availability Zone in which to create the destination file system\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `.+`   
Required: No

 ** KmsKeyId **   <a name="efs-Type-DestinationToCreate-KmsKeyId"></a>
Specifies the AWS Key Management Service \(AWS KMS\) key that you want to use to encrypt the destination file system\. If you do not specify a KMS key, Amazon EFS uses your default KMS key for Amazon EFS, `/aws/elasticfilesystem`\. This ID can be in one of the following formats:  
+ Key ID \- The unique identifier of the key, for example `1234abcd-12ab-34cd-56ef-1234567890ab`\.
+ ARN \- The Amazon Resource Name \(ARN\) for the key, for example `arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab`\.
+ Key alias \- A previously created display name for a key, for example `alias/projectKey1`\.
+ Key alias ARN \- The ARN for a key alias, for example `arn:aws:kms:us-west-2:444455556666:alias/projectKey1`\.
Type: String  
Length Constraints: Maximum length of 2048\.  
Pattern: `^([0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}|mrk-[0-9a-f]{32}|alias/[a-zA-Z0-9/_-]+|(arn:aws[-a-z]*:kms:[a-z0-9-]+:\d{12}:((key/[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12})|(key/mrk-[0-9a-f]{32})|(alias/[a-zA-Z0-9/_-]+))))$`   
Required: No

 ** Region **   <a name="efs-Type-DestinationToCreate-Region"></a>
To create a file system that uses Regional storage, specify the AWS Region in which to create the destination file system\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 64\.  
Pattern: `^[a-z]{2}-((iso[a-z]{0,1}-)|(gov-)){0,1}[a-z]+-{0,1}[0-9]{0,1}$`   
Required: No

## See Also<a name="API_DestinationToCreate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/DestinationToCreate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/DestinationToCreate) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/elasticfilesystem-2015-02-01/DestinationToCreate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/DestinationToCreate) 