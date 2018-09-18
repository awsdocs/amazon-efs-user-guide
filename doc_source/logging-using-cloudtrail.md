# Logging Amazon EFS API Calls with AWS CloudTrail<a name="logging-using-cloudtrail"></a>

Amazon EFS is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in Amazon EFS\. CloudTrail captures all API calls for Amazon EFS as events, including calls from the Amazon EFS console and from code calls to Amazon EFS API operations\. 

If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for Amazon EFS\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to Amazon EFS, the IP address from which the request was made, who made the request, when it was made, and additional details\. 

To learn more about CloudTrail, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## Amazon EFS Information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

CloudTrail is enabled on your AWS account when you create the account\. When activity occurs in Amazon EFS, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your AWS account, including events for Amazon EFS, create a trail\. A trail enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all AWS Regions\. The trail logs events from all regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see the following topics in the *AWS CloudTrail User Guide:* 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

All Amazon EFS [API calls](api-reference.md) are logged by CloudTrail\. For example, calls to the `CreateFileSystem`, `CreateMountTarget` and `CreateTags` operations generate entries in the CloudTrail log files\. 

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or AWS Identity and Access Management \(IAM\) user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html) in the *AWS CloudTrail User Guide\.*

## Understanding Amazon EFS Log File Entries<a name="understanding-service-name-entries"></a>

A *trail* is a configuration that enables delivery of events as log files to an Amazon S3 bucket that you specify\. CloudTrail log files contain one or more log entries\. An *event* represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files aren't an ordered stack trace of the public API calls, so they don't appear in any specific order\. 

The following example shows a CloudTrail log entry that demonstrates the `CreateTags` operation when a tag for a file system is created from the console\.

```
{
	"eventVersion": "1.06",
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
	"eventVersion": "1.06",
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
| Asia Pacific \(Singapore\) | 448676862907 | 
| Asia Pacific \(Tokyo\) | 620757817088 | 
| EU \(Frankfurt\) | 992038834663 | 
| EU \(Ireland\) | 805538244694 | 
| Asia Pacific \(Sydney\) | 288718191711 | 

### Amazon EFS Encryption Context for Encryption at Rest<a name="EFSKMSContext"></a>

Amazon EFS sends [encryption context](https://docs.aws.amazon.com/kms/latest/developerguide/encryption-context.html) when making AWS KMS API requests to generate data keys and decrypt Amazon EFS data\. The file system ID is the encryption context for all file systems that are encrypted at rest\. In the `requestParameters` field of a CloudTrail log entry, the encryption context looks similar to the following\.

```
"EncryptionContextEquals": {}
"aws:elasticfilesystem:filesystem:id" : "fs-4EXAMPLE"
```