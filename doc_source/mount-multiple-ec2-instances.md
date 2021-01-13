# Mounting EFS to multiple EC2 instances using AWS Systems Manager<a name="mount-multiple-ec2-instances"></a>

You can mount multiple EFS file systems to multiple Amazon EC2 instances remotely and securely without having to login to the instances by using AWS Systems Manager Run Command\. For more information about AWS Systems Manager Run Command, see [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html)\. The following pre\- requisites are required before mounting EFS file systems using this method:

1. The EC2 instances are launched with an instance profile that includes the `AmazonElasticFileSystemsUtils` permission policy\. For more information, see [Step 1: Configure an IAM instance profile with the required permissions](manage-efs-utils-with-aws-sys-manager.md#configure-sys-mgr-iam-instance-profile)\.

1. Version 1\.28\.1 or later of the Amazon EFS client \(amazon\-efs\-utils package\) is installed on the EC2 instances\. You can use AWS Systems Manager to automatically install the package on your instances\. For more information, see [Step 2: Configure an Association used by State Manager for installing or updating the Amazon EFS client](manage-efs-utils-with-aws-sys-manager.md#config-sys-mgr-association)\.

**To mount multiple EFS file systems to multiple EC2 instances using the console**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Run Command**\.

1. Choose **Run a command**\.

1. Enter **AWS\-RunShellScript** in the **Commands** search field\.

1. Select **AWS\-RunShellScript**\.

1. In **Command parameters** enter the mount command to use for each EFS file system that you want to mount\. For example:

   ```
   sudo mount -t efs -o tls fs-12345678:/ /mnt/efs
   sudo mount -t efs -o tls,accesspoint=fsap-12345678 fs-01233210 /mnt/efs
   ```

   For more information about EFS mount commands using the Amazon EFS client, see [Mounting on Amazon EC2 with the EFS mount helper](mounting-fs-mount-helper.md#mounting-fs-mount-helper-ec2)\.

1. Select the target EC2 instances that you want the command to run on\. The recommended setting is **Choose all instances** to register all new and existing EC2 instances as targets to automatically install or update **AmazonEFSUtils**\. Alternatively, you can specify instance tags, select instances manually, or choose a resource group to apply the association to a subset of instances\. If you specify instance tags, you must launch your EC2 instances with the tags to allows AWS Systems Manager to automatically install or update the Amazon EFS client\.

1. Make any other additional settings you would like\. Then choose **Run** to run the command and mount the EFS file systems specified in the command\.

   Once you run the command, you can see its status in the command history\.