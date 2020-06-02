# Amazon EFS API<a name="api-reference"></a>

The Amazon EFS API is a network protocol based on [HTTP \(RFC 2616\)](https://www.ietf.org/rfc/rfc2616.txt)\. For each API call, you make an HTTP request to the region\-specific Amazon EFS API endpoint for the AWS Region where you want to manage file systems\. The API uses JSON \(RFC 4627\) documents for HTTP request/response bodies\.

The Amazon EFS API is an RPC model\. In this model, there is a fixed set of operations and the syntax for each operation is known to clients without any prior interaction\. In the following section, you can find a description of each API operation using an abstract RPC notation\. Each has an operation name that doesn't appear on the wire\. For each operation, the topic specifies the mapping to HTTP request elements\. 

The specific Amazon EFS operation to which a given request maps is determined by a combination of the request's method \(GET, PUT, POST, or DELETE\) and which of the various patterns its Request\-URI matches\. If the operation is PUT or POST, Amazon EFS extracts call arguments from the Request\-URI path segment, query parameters, and the JSON object in the request body\.

**Note**  
Although operation names, such as `CreateFileSystem`, don't appear on the wire, these names are meaningful in AWS Identity and Access Management \(IAM\) policies\. For more information, see [Identity and Access Management for Amazon EFS](auth-and-access-control.md)\.   
The operation name is also used to name commands in command\-line tools and elements of the AWS SDK APIs\. For example, there is a AWS CLI command named `create-file-system` that maps to the `CreateFileSystem` operation\.   
The operation name also appears in AWS CloudTrail logs for Amazon EFS API calls\.

## API Endpoint<a name="api-reference-endpoint"></a>

The API endpoint is the DNS name used as a host in the HTTP URI for the API calls\. These API endpoints are specific to AWS Regions and take the following form\.

```
elasticfilesystem.aws-region.amazonaws.com
```

For example, the Amazon EFS API endpoint for the US West \(Oregon\) Region is the following\.

```
elasticfilesystem.us-west-2.amazonaws.com
```

For a list of AWS Regions that Amazon EFS supports \(where you can create and manage file systems\), see [Amazon Elastic File System ](https://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem-region) in the *AWS General Reference*\.

The region\-specific API endpoint defines the scope of the Amazon EFS resources that are accessible when you make an API call\. For example, when you call the `DescribeFileSystems` operation using the preceding endpoint, you get a list of file systems in the US West \(Oregon\) Region that have been created in your account\. 

## API Version<a name="api-reference-version"></a>

The version of the API being used for a call is identified by the first path segment of the request URI, and its form is an ISO 8601 date\. For example, see [CreateFileSystem](API_CreateFileSystem.md)\. 

The documentation describes API version 2015\-02\-01\.

## Related Topics<a name="api-reference-related-topics"></a>

The following sections provide descriptions of the API operations, how to create a signature for request authentication, and how to grant permissions for these API operations using the IAM policies\.
+ [Identity and Access Management for Amazon EFS](auth-and-access-control.md)
+ [Actions](API_Operations.md)
+ [Data Types](API_Types.md)

## Working with the Query API Request Rate for Amazon EFS<a name="api-throttling"></a>

Amazon EFS API requests are throttled for each AWS account on a per\-region basis to help service performance\. All Amazon EFS API calls together, whether they originate from an application, the AWS CLI, or the Amazon EFS console, must not exceed the maximum allowed API request rate\. The maximum API request rate can vary across AWS Regions\. API requests made by AWS Identity and Access Management \(IAM\) users are attributed to the underlying AWS account\.

If an API request exceeds the API request rate for its category, the request returns the `ThrottlingException` error code\. To prevent this error, ensure that your application doesn't retry API requests at a high rate\. You can do this by using care when polling and by using exponential backoff retries\.

### Polling<a name="efs-polling"></a>

Your application might need to call an API operation repeatedly to check for an update in status\. Before you start polling, give the request time to potentially complete\. When you begin polling, use an appropriate sleep interval between successive requests\. For best results, use an increasing sleep interval\.

### Retries or Batch Processing<a name="efs-retries"></a>

Your application might need to retry an API request after it fails, or to process multiple resources \(for example, all of your Amazon EFS file systems\)\. To lower the rate of API requests, use an appropriate sleep interval between successive requests\. For best results, use an increasing or variable sleep interval\.

### Calculating the Sleep Interval<a name="efs-sleep-interval-calculation"></a>

When you have to poll or retry an API request, we recommend using an exponential backoff algorithm to calculate the sleep interval between API calls\. The idea behind exponential backoff is to use progressively longer waits between retries for consecutive error responses\. For more information, and implementation examples of this algorithm, see [Error Retries and Exponential Backoff in AWS](https://docs.aws.amazon.com/general/latest/gr/api-retries.html) in the *Amazon Web Services General Reference\.*