# Step 4: Clean Up<a name="wt1-clean-up"></a>

If you no longer need the resources you created, you should remove them\. You can do this with the CLI\.
+ Remove EC2 resources \(the EC2 instance and the two security groups\)\. Amazon EFS deletes the network interface when you delete the mount target\. 
+ Remove Amazon EFS resources \(file system, mount target\)\.

**To delete AWS resources created in this walkthrough**

1. Terminate the EC2 instance you created for this walkthrough\. 

   ```
   $ aws ec2 terminate-instances \
   --instance-ids instance-id \
   --profile adminuser
   ```

   You can also delete EC2 resources using the console\. For instructions, see [Terminating an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#terminating-instances-console) in the *Amazon EC2 User Guide for Linux Instances*\. 

1. Delete the mount target\.

   You must delete the mount targets created for the file system before deleting the file system\. You can get a list of mount targets by using the `describe-mount-targets` CLI command\.

   ```
   $  aws efs describe-mount-targets \
   --file-system-id file-system-ID \
   --profile adminuser \
   --region aws-region
   ```

   Then delete the mount target by using the `delete-mount-target` CLI command\.

   ```
   $ aws efs delete-mount-target \
   --mount-target-id ID-of-mount-target-to-delete \
   --profile adminuser \
   --region aws-region
   ```

1. \(Optional\) Delete the two security groups you created\. You don't pay for creating security groups\.

   You must delete the mount target's security group first, before deleting the EC2 instance's security group\. The mount target's security group has a rule that references the EC2 security group\. Therefore, you cannot first delete the EC2 instance's security group\.

   For instructions, see [Deleting a Security Group](https://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-sg.html#deleting-a-security-group) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Delete the file system by using the `delete-file-system` CLI command\. You can get a list of your file systems by using the `describe-file-systems` CLI command\. You can get the file system ID from the response\.

   ```
   aws efs describe-file-systems \
   --profile adminuser \
   --region aws-region
   ```

   Delete the file system by providing the file system ID\.

   ```
   $ aws efs delete-file-system \
   --file-system-id ID-of-file-system-to-delete \
   --region aws-region \
   --profile adminuser
   ```