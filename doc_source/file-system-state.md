# File system status<a name="file-system-state"></a>

You can view the status of Amazon EFS file systems using the Amazon EFS console or the AWS CLI\. An Amazon EFS file system can have one of the status values described in the following table\.


| File system state  | Description | 
| --- | --- | 
| AVAILABLE | The file system is in a healthy state, and is reachable and available for use\. | 
| CREATING | Amazon EFS is in the process of creating the new file system\. | 
| DELETING | Amazon EFS is deleting the file system in response to a user\-initiated delete request\. For more information, see [Deleting an Amazon EFS file system](delete-efs-fs.md)\.  | 
| DELETED | Amazon EFS has deleted the file system in response to a user\-initiated delete request\. For more information, see [Deleting an Amazon EFS file system](delete-efs-fs.md)\.  | 
| UPDATING | The file system is undergoing an update in response to a user\-initiated update request\. | 
| ERROR | Applicable for file systems using One Zone storage classes, including file systems in a replication configuration\. The file system is in a failed state and is unrecoverable\. To access the file system data, restore a backup of this file system to a new file system\. For more information, see: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/efs/latest/ug/file-system-state.html) | 