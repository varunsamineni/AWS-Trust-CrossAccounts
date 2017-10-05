# AWS-Trust-CrossAccounts

Examples for establishing Cross Account Trust relationship on AWS


## Introduction

This documentation is aimed at allowing AWS account managers to setup new AWS accounts under AWS Organizations, using AWS CLI or Terraform.
Following best practices, we will create an IAM account to manage all user logins and permissions.
All other accounts will then have their Trust relationship be modified to include the IAM account id. 


### Account limit

Given that by default new Billing accounts have a soft limit of one subaccount, you will be required to create a ticket with AWS Support to increase account limit.

Visit https://console.aws.amazon.com/support/home?/case/create and submit a ticket for Organizations and increase the limit to the number you feel comfortable to use the future.


## Prepare Billing Account

Following best practices, we will create an IAM user in the  Billing account. This user will have an MFA token attached to their account, no permissions, and be part of a Group.

That Group will have two policies attached to it.

One policy (group_orgs_crossaccount) will be used to allow the users that are part of this group to Assume a Role, giving them specific privileges.

The other policy (group_create_aws_accounts) grants these users to Assume a Role in other accounts.


### Policy: group_orgs_crossaccount

#### Description
This policy Grants resources to access the Role 'OrganizationAccountAccessRole' in other accounts.
This policy is to be added to an IAM group.


For simplicity, we will be using the default cross account Role name 'OrganizationAccountAccessRole'.
This means, a web console user can login into another account by using the following URL and replacing 'IAM_ACCOUNTID' with the account id or its custom name. 
https://signin.aws.amazon.com/switchrole?roleName=OrganizationAccountAccessRole&account=IAM_ACCOUNTID


### Policy: group_create_aws_accounts

#### Description
This policy allows a resource to assume the Role 'assumerole-create_account'.
This policy is to be added to an IAM group.


### Role: assumerole-create_account

#### Description
This role is assumed by an user, grating privileges to create AWS Orgs accounts


Create an IAM Role using the Billing Account ID as the trust relationship

Give the following URL to users who can switch roles in the console, replacing 'IAM_ACCOUNTID' with the Billing account id or its custom name.
https://signin.aws.amazon.com/switchrole?roleName=assumerole-create_account&account=BILLING_ACCOUNTID


### Policy: role_create_accounts

#### Description
Allow user to create AWS Org accounts
This policy is to be added to an IAM Role that will grant privileges to Create AWS Organizations accounts under the Billing account.

