# FileSystemSize<a name="API_FileSystemSize"></a>

The latest known metered size \(in bytes\) of data stored in the file system, in its `Value` field, and the time at which that size was determined in its `Timestamp` field\. The value doesn't represent the size of a consistent snapshot of the file system, but it is eventually consistent when there are no writes to the file system\. That is, the value represents the actual size only if the file system is not modified for a period longer than a couple of hours\. Otherwise, the value is not necessarily the exact size the file system was at any instant in time\.

## Contents<a name="API_FileSystemSize_Contents"></a>

 **Timestamp**   <a name="efs-Type-FileSystemSize-Timestamp"></a>
The time at which the size of data, returned in the `Value` field, was determined\. The value is the integer number of seconds since 1970\-01\-01T00:00:00Z\.  
Type: Timestamp  
Required: No

 **Value**   <a name="efs-Type-FileSystemSize-Value"></a>
The latest known metered size \(in bytes\) of data stored in the file system\.  
Type: Long  
Valid Range: Minimum value of 0\.  
Required: Yes

 **ValueInIA**   <a name="efs-Type-FileSystemSize-ValueInIA"></a>
The latest known metered size \(in bytes\) of data stored in the Infrequent Access storage class\.  
Type: Long  
Valid Range: Minimum value of 0\.  
Required: No

 **ValueInStandard**   <a name="efs-Type-FileSystemSize-ValueInStandard"></a>
The latest known metered size \(in bytes\) of data stored in the Standard storage class\.  
Type: Long  
Valid Range: Minimum value of 0\.  
Required: No

## See Also<a name="API_FileSystemSize_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/FileSystemSize) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/FileSystemSize) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/FileSystemSize) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/elasticfilesystem-2015-02-01/FileSystemSize) 