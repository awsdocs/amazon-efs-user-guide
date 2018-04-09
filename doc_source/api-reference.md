# Amazon EFS API<a name="api-reference"></a>

The Amazon EFS API is a network protocol based on [HTTP \(RFC 2616\)](https://www.ietf.org/rfc/rfc2616.txt)\. For each API call, you make an HTTP request to the region\-specific Amazon EFS API endpoint for the AWS Region where you want to manage file systems\. The API uses JSON \(RFC 4627\) documents for HTTP request/response bodies\.

The Amazon EFS API is an RPC model, in which there is a fixed set of operations and the syntax for each operation is known to clients without any prior interaction\. This section describes each API operation using an abstract RPC notation, with an operation name that does not appear on the wire\. For each operation, the topic specifies the mapping to HTTP request elements\. 

The specific Amazon EFS operation to which a given request maps is determined by a combination of the request's method \(GET, PUT, POST, or DELETE\) and which of the various patterns its Request\-URI matches\. If the operation is PUT or POST, Amazon EFS extracts call arguments from the Request\-URI path segment, query parameters, and the JSON object in the request body\.

**Note**  
Although the operation name, such as `CreateFileSystem`, does not appear on the wire these names are meaningful in IAM policies\. For more information, see [Authentication and Access Control for Amazon EFS](auth-and-access-control.md)\. The operation name is also used to name commands in command\-line tools and elements of the AWS SDK APIs\. For example, there is a CLI command `create-file-system` that maps to the `CreateFileSystem` operation\. It also appears in CloudTrail logs for Amazon EFS API calls\.

## API Endpoint<a name="api-reference-endpoint"></a>

The API endpoint is the DNS name used as a host in the HTTP URI for the API calls\. These API endpoints are region\-specific and take the following form:

```
elasticfilesystem.aws-region.amazonaws.com
```

For example, the Amazon EFS API endpoint for the US West \(Oregon\) Region is:

```
elasticfilesystem.us-west-2.amazonaws.com
```

For a list of AWS Regions that Amazon EFS supports \(where you can create and manage file systems\), see [Amazon Elastic File System ](http://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem-region) in the *AWS General Reference*\.

The region\-specific API endpoint defines the scope of the Amazon EFS resources that are accessible when you make an API call\. For example, when you call the `DescribeFileSystems` operation using the preceding endpoint, you get a list of file systems in the US West \(Oregon\) Region that have been created in your account\. 

## API Version<a name="api-reference-version"></a>

The version of the API being used for a call is identified by the first path segment of the request URI, and its form is a ISO 8601 date\. For example, see [CreateFileSystem](API_CreateFileSystem.md)\. 

The documentation describes API version 2015\-02\-01\.

## Related Topics<a name="api-reference-related-topics"></a>

The following sections provide descriptions of the API operations, how to create a signature for request authentication, and how to grant permissions for these API operations using the IAM policies\.
+ [Authentication and Access Control for Amazon EFS](auth-and-access-control.md)
+ [Actions](API_Operations.md)
+ [Data Types](API_Types.md)