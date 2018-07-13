# Logging Amazon EFS API Calls with AWS CloudTrail<a name="logging-using-cloudtrail"></a>

Amazon EFS is integrated with AWS CloudTrail, a service that captures AWS API calls and delivers the log files to an Amazon S3 bucket that you specify\. CloudTrail captures API calls from the Amazon EFS console, the AWS CLI, or one of the AWS SDKs to the Amazon EFS API operations\. Using the information collected by CloudTrail, you can determine the request that was made to Amazon EFS, the source IP address from which the request was made, who made the request, when it was made, and more\.

Once you've created a trail, it starts logging events automatically for that region\. It can take about 15 minutes for the logs to appear in the bucket\. To learn more about CloudTrail, including how to configure and enable it, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## Amazon EFS Information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

When CloudTrail logging is enabled in your AWS account, API calls made to Amazon EFS are tracked in CloudTrail log files, where they are written with other AWS service records\. CloudTrail determines when to create and write to a new log file based on a time period and file size\.

All Amazon EFS [API calls](api-reference.md) are logged by CloudTrail\. For example, calls to the `CreateFileSystem`, `CreateMountTarget` and `CreateTags` actions generate entries in the CloudTrail log files\. 

Each log file contains at least one API call\. Some Amazon EFS API calls will trigger other API calls for other services\. For example, the Amazon EFS `CreateMountTarget` API call will trigger a `CreateNetworkInterface` Amazon EC2 API call\. For more information on which Amazon EFS API actions will trigger API calls in other services, see the **Required Permissions \(API Actions\)** column of the table in [Amazon EFS API Permissions: Actions, Resources, and Conditions Reference](efs-api-permissions-ref.md)\.

Every log entry contains information about who generated the request\. The user identity information in the log entry helps you determine the following: 
+ Whether the request was made with root or IAM user credentials
+ Whether the request was made with temporary security credentials for a role or federated user
+ Whether the request was made by another AWS service

For more information, see the [CloudTrail userIdentity Element](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

You can store your log files in your Amazon S3 bucket for as long as you want, but you can also define Amazon S3 lifecycle rules to archive or delete log files automatically\. By default, your log files are encrypted with Amazon S3 server\-side encryption \(SSE\)\.

If you want to be notified upon log file delivery, you can configure CloudTrail to publish Amazon SNS notifications when new log files are delivered\. For more information, see [Configuring Amazon SNS Notifications for CloudTrail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)\.

You can also aggregate Amazon EFS log files from multiple AWS regions and multiple AWS accounts into a single Amazon S3 bucket\. 

For more information, see [Receiving CloudTrail Log Files from Multiple Regions](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html) and [Receiving CloudTrail Log Files from Multiple Accounts](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)\.

## Understanding Amazon EFS Log File Entries<a name="understanding-service-name-entries"></a>

CloudTrail log files can contain one or more log entries\. Each entry lists multiple JSON\-formatted events\. A log entry represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and more\. For information on what events are recorded, see the [CloudTrail Record Contents](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html) in the AWS CloudTrail User Guide\. Log entries are not an ordered stack trace of the public API calls, so they do not appear in any specific order\. 

The following example shows a CloudTrail log entry that demonstrates the `CreateTags` action when a tag for a file system is created from the console\.

```
{
	"eventVersion": "1.04",
	"userIdentity": {
		"type": "Root",
		"principalId": "111122223333",
		"arn": "arn:aws:iam::111122223333:root",
		"accountId": "111122223333",
		"accessKeyId": "AKIAIOSFODNN7EXAMPLE",
		"sessionContext": {
			"attributes": {
				"mfaAuthenticated": "false",
				"creationDate": "2017-03-01T18:02:37Z"
			}
		}
	},
	"eventTime": "2017-03-01T19:25:47Z",
	"eventSource": "elasticfilesystem.amazonaws.com",
	"eventName": "CreateTags",
	"awsRegion": "us-west-2",
	"sourceIPAddress": "192.0.2.0",
	"userAgent": "console.amazonaws.com",
	"requestParameters": {
		"fileSystemId": "fs-00112233",
		"tags": [{
				"key": "TagName",
				"value": "AnotherNewTag"
			}
		]
	},
	"responseElements": null,
	"requestID": "dEXAMPLE-feb4-11e6-85f0-736EXAMPLE75",
	"eventID": "eEXAMPLE-2d32-4619-bd00-657EXAMPLEe4",
	"eventType": "AwsApiCall",
	"apiVersion": "2015-02-01",
	"recipientAccountId": "111122223333"
}
```

The following example shows a CloudTrail log entry that demonstrates the `DeleteTags` action when a tag for a file system is deleted from the console\.

```
{
	"eventVersion": "1.04",
	"userIdentity": {
		"type": "Root",
		"principalId": "111122223333",
		"arn": "arn:aws:iam::111122223333:root",
		"accountId": "111122223333",
		"accessKeyId": "AKIAIOSFODNN7EXAMPLE",
		"sessionContext": {
			"attributes": {
				"mfaAuthenticated": "false",
				"creationDate": "2017-03-01T18:02:37Z"
			}
		}
	},
	"eventTime": "2017-03-01T19:25:47Z",
	"eventSource": "elasticfilesystem.amazonaws.com",
	"eventName": "DeleteTags",
	"awsRegion": "us-west-2",
	"sourceIPAddress": "192.0.2.0",
	"userAgent": "console.amazonaws.com",
	"requestParameters": {
		"fileSystemId": "fs-00112233",
		"tagKeys": []
	},
	"responseElements": null,
	"requestID": "dEXAMPLE-feb4-11e6-85f0-736EXAMPLE75",
	"eventID": "eEXAMPLE-2d32-4619-bd00-657EXAMPLEe4",
	"eventType": "AwsApiCall",
	"apiVersion": "2015-02-01",
	"recipientAccountId": "111122223333"
}
```

## Amazon EFS Log File Entries for Encrypted\-at\-Rest File Systems<a name="efs-encryption-cloudtrail"></a>

Amazon EFS gives you the option of using encryption at rest, encryption in transit, or both, for your file systems\. For more information, see [Encrypting Data and Metadata in EFS](encryption.md)\.

If you're using an encrypted\-at\-rest file system, the calls that Amazon EFS makes on your behalf appear in your AWS CloudTrail logs as coming from an AWS\-owned account\. If you see one of the following account IDs in your CloudTrail logs, depending on the AWS Region that your file system is created in, this ID is one owned by the Amazon EFS service\.


| AWS Region | Account ID | 
| --- | --- | 
| US East \(Ohio\) | 771736226457 | 
| US East \(N\. Virginia\) | 055650462987 | 
| US West \(N\. California\) | 208867197265 | 
| US West \(Oregon\) | 736298361104 | 
| Asia Pacific \(Seoul\) | 518632624599 | 
| Asia Pacific \(Tokyo\) | 620757817088 | 
| EU \(Frankfurt\) | 992038834663 | 
| EU \(Ireland\) | 805538244694 | 
| Asia Pacific \(Sydney\) | 288718191711 | 

### Amazon EFS Encryption Context for Encryption at Rest<a name="EFSKMSContext"></a>

Amazon EFS sends [encryption context](http://docs.aws.amazon.com/kms/latest/developerguide/encryption-context.html) when making AWS KMS API requests to generate data keys and decrypt Amazon EFS data\. The file system ID is the encryption context for all file systems that are encrypted at rest\. In the `requestParameters` field of a CloudTrail log entry, the encryption context looks similar to the following\.

```
"EncryptionContextEquals": {}
"aws:elasticfilesystem:filesystem:id" : "fs-4EXAMPLE"
```