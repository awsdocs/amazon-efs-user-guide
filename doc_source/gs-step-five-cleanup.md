# Step 4: Clean Up Resources and Protect Your AWS Account<a name="gs-step-five-cleanup"></a>

This guide includes walkthroughs that you can use to further explore Amazon EFS\. Before you perform this clean\-up step, you can use the resources you've created and connected to in this Getting Started exercise in those walkthroughs\. For more information, see [Walkthroughs](walkthroughs.md)\. After you finish the walkthroughs, or if you don't want to explore the walkthroughs, take the following steps to clean up your resources and protect your AWS account\.

**To clean up resources and protect your AWS account**

1. Connect to your Amazon EC2 instance\.

1. Unmount the Amazon EFS file system with the following command\.

   ```
   $ sudo umount efs
   ```

1. Open the Amazon EFS console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose the Amazon EFS file system that you want to delete from the list of file systems\.

1. For **Actions**, choose **Delete file system**\.

1. In the **Permanently delete file system** dialog box, type the file system ID for the Amazon EFS file system that you want to delete, and then choose **Delete File System**\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose the Amazon EC2 instance that you want to terminate from the list of instances\.

1. For **Actions**, choose **Instance State** and then choose **Terminate**\.

1. In **Terminate Instances**, choose **Yes, Terminate** to terminate the instance that you created for this Getting Started exercise\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the name of the security group that you created for this Getting Started exercise in [Step 2: Create Your EC2 Resources and Launch Your EC2 Instance](gs-step-one-create-ec2-resources.md) as a part of the Amazon EC2 instance Launch Wizard\.
**Warning**  
Don't delete the default security group for your VPC\.

1. For **Actions**, choose **Delete Security Group**\.

1. In **Delete Security Group**, choose **Yes, Delete** to delete the security group you created for this Getting Started exercise\.