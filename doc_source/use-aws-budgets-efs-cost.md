# Managing Amazon EFS file system costs using AWS Budgets<a name="use-aws-budgets-efs-cost"></a>

You can plan and manage your Amazon EFS file system costs by using AWS Budgets\.

You can work with AWS Budgets from the AWS Billing and Cost Management console\. To use AWS Budgets, you create a monthly cost budget for your EFS file systems\. You can set up your budget to notify you if your costs are forecast to exceed your budgeted amount, and then make adjustments to maintain your budget as needed\. 

There are costs associated with using AWS Budgets\. For regular AWS accounts, your first two budgets are free\. For more information about AWS Budgets, including costs, see [Managing Your Costs with Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html) in the *AWS Billing and Cost Management User Guide*\.

You can set custom budgets for your Amazon EFS costs and usage at the account, AWS Region, service, or tag level by using budget parameters\. In the following section, you can find a high\-level description of how to set up a cost budget on an EFS file system with AWS Budgets\. You do so by using cost allocation tags\.

## Prerequisites<a name="prerequisites-efs-cost"></a>

To perform the procedures referenced in the following sections, make sure that you have the following:
+ An EFS file system
+ An AWS Identity and Access Management \(IAM\) policy with the following permissions:
  + Access to the Billing and Cost Management console\.
  + Ability to perform the `elasticfilesystem:CreateTags` and `elasticfilesystem:DescribeTags` actions\.

## Creating a monthly cost budget for an EFS file system<a name="create-cost-budget-efs"></a>

Creating a monthly cost budget for your Amazon EFS file system using tags is a three\-step process\.

**To create a monthly cost budget for your EFS file system using tags**

1.  Create a tag to use to identify the file system that you want to track costs for\. To learn how, see [Managing file system tags](manage-fs-tags.md)\. 

1.  In the Billing and Cost Management console, activate the tag as a cost allocation tag\. For a detailed procedure, see [Activating user\-defined cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/activating-tags.html) in the *AWS Billing and Cost Management User Guide*\. 

1.  In the Billing and Cost Management console, under **Budgets**, create a monthly cost budget in AWS Budgets\. For a detailed procedure, see [Creating a cost budget](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-create.html) in the *AWS Billing and Cost Management User Guide*\. 

After you create your EFS monthly cost budget, you can view it in the **Budgets** dashboard, which displays the following budget data:
+ Your current costs and usage incurred for a budget during the budget period\.
+ Your budgeted costs for the budget period\.
+ Your forecast costs for the budget period\.
+ A percentage that shows your costs compared to your budgeted amount\.
+ A percentage that shows your forecast costs compared to your budgeted amount\.

For more information about viewing your EFS cost budget, see [Viewing your budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-view.html) in the *AWS Billing and Cost Management User Guide*\.