# Using IAM to Enforce Creating Encrypted File Systems<a name="using-iam-to-enforce-encryption-at-rest"></a>

You can create an AWS Identity and Access Management \(IAM\) identity\-based policy to control whether users can create Amazon EFS file systems that are encrypted at rest\. The Boolean condition key `elasticfilesystem:Encrypted` specifies the type of file system, encrypted or unencrypted, that the policy applies to\. You use the condition key with the `elasticfilesystem:CreateFileSystem` action and the policy effect, allow or deny, to create a policy for creating encrypted or unencrypted file systems\. 

For example, an IAM policy that explicitly *allows* users to create only encrypted EFS file systems uses the following combination of effect, action, and condition:
+ The `Effect` is `Allow`\.
+ The `Action` is `elasticfilesystem:CreateFileSystem`\.
+ The `Condition` `elasticfilesystem:Encrypted` is `True`\.

The following example illustrates an IAM identity\-based policy that authorizes principals to create only encrypted file systems\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "elasticfilesystem:CreateFileSystem",
            "Condition": {
                "Bool": {
                    "elasticfilesystem:Encrypted": "true"
                }
            },
            "Resource": "*"
        }
    ]
}
```

If this policy is attached to a user who tries to create an unencrypted file system, the request fails\. The user sees a message similar to the following, whether they are using the AWS Management Console, the AWS CLI, or the AWS API or SDK:

```
User: arn:aws:iam::111122223333:user/username is not authorized to
      perform: elasticfilesystem:CreateFileSystem on the specified resource.
```

An IAM policy that explicitly *allows* creating only unencrypted EFS file systems can use the following combination of effect, action, and condition:
+ The `Effect` is `Allow`\.
+ The `Action` is `elasticfilesystem:CreateFileSystem`\.
+ The `Condition` `elasticfilesystem:Encrypted` is `False`\.

The following example illustrates an IAM identity\-based policy that authorizes principals to create only unencrypted file systems\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "elasticfilesystem:CreateFileSystem",
            "Condition": {
                "Bool": {
                    "elasticfilesystem:Encrypted": "false"
                }
            },
            "Resource": "*"
        }
    ]
}
```



If this policy is attached to a user who tries to create an encrypted file system, the request fails\. The user sees a message similar to the following, whether they are using the AWS Management Console, the AWS CLI, or the AWS API or SDK:

```
User: arn:aws:iam::111122223333:user/username is not authorized to 
      perform: elasticfilesystem:CreateFileSystem on the specified resource.
```



You can also use policies like the previous examples in an AWS Organizations service control policy \(SCP\) to enforce the creation of encrypted Amazon EFS file systems for all AWS accounts in your organization\. For more information about service control policies in AWS Organizations, see [Service control policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html#orgs_manage_policies_scp) in the *AWS Organizations User Guide*\.

## Create an IAM Policy That Authorizes Users to Create Only Encrypted EFS File Systems<a name="create-IAM-policy-enforce-encryption"></a>

You can create an IAM identity\-based policy that authorizes users to create only encrypted Amazon EFS file systems using the console, the AWS CLI, and the API\. The following procedure describes how to create such a policy using the IAM console, and then apply the policy to a user in your account\.

**To create an IAM policy to enforce creating encrypted EFS file systems**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, under **Access management**, choose **Policies**\.

1. Choose **Create policy** to display the **Create policy** page\.

1. Enter the following information using the **Visual editor**:
   + For **Service**, choose **EFS**\.
   + For **Actions**, enter **create** in the search field, and then choose **CreateFileSystem**\.
   + For **Request conditions**, choose **Encrypted**\.

1. Choose **Review policy** to display the **Review policy** page\.

1. Provide a **Name** and a **Description** for the policy\. Verify the policy summary, including the **Encrypted** request condition\. Choose **Create policy** to create the policy\.

**To apply the policy to a user in your account**

1. In the IAM console, under **Access management**, choose **Users**\.

1. Select the user that you want to apply the policy to\.

1. Choose **Add permissions** to display the **Add permissions** page\.

1. Choose **Attach existing policies directly**\.

1. Enter the name of the EFS policy that you created in the previous procedure\.

1. Select and expand the policy\. Then choose **\{\}JSON** to verify the policy content\. It should look like the following JSON policy\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "VisualEditor0",
               "Effect": "Allow",
               "Action": "elasticfilesystem:CreateFileSystem",
               "Condition":{
                   "Bool": {
                       "elasticfilesystem:Encrypted": "true"
                   }
               },
               "Resource": "*"
           }
       ]
   }
   ```

1. Choose **Next: Review**\.

1. Choose **Add permissions** to apply the policy to the user\. This user can now only create Amazon EFS file systems that are encrypted at rest\.