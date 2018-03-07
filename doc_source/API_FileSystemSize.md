# FileSystemSize<a name="API_FileSystemSize"></a>

Latest known metered size \(in bytes\) of data stored in the file system, in its `Value` field, and the time at which that size was determined in its `Timestamp` field\. Note that the value does not represent the size of a consistent snapshot of the file system, but it is eventually consistent when there are no writes to the file system\. That is, the value will represent the actual size only if the file system is not modified for a period longer than a couple of hours\. Otherwise, the value is not necessarily the exact size the file system was at any instant in time\.

## Contents<a name="API_FileSystemSize_Contents"></a>

 **Timestamp**   <a name="efs-Type-FileSystemSize-Timestamp"></a>
Time at which the size of data, returned in the `Value` field, was determined\. The value is the integer number of seconds since 1970\-01\-01T00:00:00Z\.  
Type: Timestamp  
Required: No

 **Value**   <a name="efs-Type-FileSystemSize-Value"></a>
Latest known metered size \(in bytes\) of data stored in the file system\.  
Type: Long  
Valid Range: Minimum value of 0\.  
Required: Yes

## See Also<a name="API_FileSystemSize_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/elasticfilesystem-2015-02-01/FileSystemSize) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/elasticfilesystem-2015-02-01/FileSystemSize) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/elasticfilesystem-2015-02-01/FileSystemSize) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/elasticfilesystem-2015-02-01/FileSystemSize) 