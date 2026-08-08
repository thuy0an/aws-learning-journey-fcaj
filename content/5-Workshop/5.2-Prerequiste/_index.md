---
title: "Prerequisites"
date: 2026-08-03
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---
## Account and tools

| Requirement           | Details                                                                 |
| --------------------- | ----------------------------------------------------------------------- |
| AWS account           | Personal account in`ap-southeast-1` (Singapore), with MFA enabled     |
| AWS CLI               | AWS CLI v2 with a suitable profile or IAM role                          |
| Python                | Python 3.12 and`pip` for the Django backend                           |
| Node.js               | Node.js 20+ for building the React/Vite frontend                        |
| Docker                | Builds and tests Django and React images before pushing to ECR          |
| Git / GitHub          | Repository`HaoWasabi/NeonFoodmap`; GitHub Actions uses OIDC for CI/CD |
| Google Text-to-Speech | API key for guide audio; keep it in GitHub Secrets                      |
| PayPal Sandbox        | Sandbox details for payment testing; never use real data                |
| Infrastructure file   | `SourceProject/neonfoodmap-iam-setup.yaml`                            |

## IAM permissions

Attach the following IAM permission policy to your AWS user account to deploy and clean up resources in this workshop.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "CloudFormationStackManagement",
      "Effect": "Allow",
      "Action": [
        "cloudformation:CreateStack",
        "cloudformation:CreateChangeSet",
        "cloudformation:UpdateStack",
        "cloudformation:DeleteStack",
        "cloudformation:DeleteChangeSet",
        "cloudformation:DescribeChangeSet",
        "cloudformation:DescribeStacks",
        "cloudformation:DescribeStackEvents",
        "cloudformation:DescribeStackResources",
        "cloudformation:DescribeStackResource",
        "cloudformation:ExecuteChangeSet",
        "cloudformation:GetTemplate",
        "cloudformation:ListStacks",
        "cloudformation:ListStackResources",
        "cloudformation:ValidateTemplate"
      ],
      "Resource": "*"
    },
    {
      "Sid": "IAMResourcesForNeonFoodmap",
      "Effect": "Allow",
      "Action": [
        "iam:AddUserToGroup",
        "iam:AttachGroupPolicy",
        "iam:AttachRolePolicy",
        "iam:CreateGroup",
        "iam:CreateInstanceProfile",
        "iam:CreateLoginProfile",
        "iam:CreateOpenIDConnectProvider",
        "iam:CreatePolicy",
        "iam:CreatePolicyVersion",
        "iam:CreateRole",
        "iam:CreateUser",
        "iam:DeleteGroup",
        "iam:DeleteInstanceProfile",
        "iam:DeleteLoginProfile",
        "iam:DeleteOpenIDConnectProvider",
        "iam:DeletePolicy",
        "iam:DeletePolicyVersion",
        "iam:DeleteRole",
        "iam:DeleteUser",
        "iam:DetachGroupPolicy",
        "iam:DetachRolePolicy",
        "iam:GetGroup",
        "iam:GetInstanceProfile",
        "iam:GetOpenIDConnectProvider",
        "iam:GetPolicy",
        "iam:GetPolicyVersion",
        "iam:GetRole",
        "iam:GetUser",
        "iam:ListAttachedGroupPolicies",
        "iam:ListAttachedRolePolicies",
        "iam:ListGroups",
        "iam:ListGroupsForUser",
        "iam:ListInstanceProfilesForRole",
        "iam:ListOpenIDConnectProviders",
        "iam:ListPolicies",
        "iam:ListPolicyTags",
        "iam:ListPolicyVersions",
        "iam:ListRoleTags",
        "iam:ListRoles",
        "iam:ListUserTags",
        "iam:ListUsers",
        "iam:PassRole",
        "iam:PutGroupPolicy",
        "iam:PutRolePolicy",
        "iam:RemoveRoleFromInstanceProfile",
        "iam:RemoveUserFromGroup",
        "iam:SetDefaultPolicyVersion",
        "iam:TagGroup",
        "iam:TagOpenIDConnectProvider",
        "iam:TagPolicy",
        "iam:TagRole",
        "iam:TagUser",
        "iam:UntagGroup",
        "iam:UntagOpenIDConnectProvider",
        "iam:UntagPolicy",
        "iam:UntagRole",
        "iam:UntagUser",
        "iam:UpdateAssumeRolePolicy",
        "iam:UpdateLoginProfile",
        "iam:UpdateOpenIDConnectProviderThumbprint",
        "iam:UpdateRole",
        "iam:UpdateUser"
      ],
      "Resource": "*"
    },
    {
      "Sid": "SNSBudgetAndCostAnomalyResources",
      "Effect": "Allow",
      "Action": [
        "sns:CreateTopic",
        "sns:DeleteTopic",
        "sns:GetTopicAttributes",
        "sns:ListSubscriptionsByTopic",
        "sns:ListTagsForResource",
        "sns:ListTopics",
        "sns:SetTopicAttributes",
        "sns:Subscribe",
        "sns:TagResource",
        "sns:Unsubscribe",
        "sns:UntagResource",
        "budgets:CreateBudget",
        "budgets:ModifyBudget",
        "budgets:DeleteBudget",
        "budgets:DescribeBudget",
        "budgets:DescribeBudgets",
        "budgets:CreateNotification",
        "budgets:DeleteNotification",
        "budgets:DescribeNotificationsForBudget",
        "budgets:CreateSubscriber",
        "budgets:DeleteSubscriber",
        "budgets:DescribeSubscribersForNotification",
        "ce:CreateAnomalyMonitor",
        "ce:CreateAnomalySubscription",
        "ce:DeleteAnomalyMonitor",
        "ce:DeleteAnomalySubscription",
        "ce:GetAnomalyMonitors",
        "ce:GetAnomalySubscriptions",
        "ce:UpdateAnomalyMonitor",
        "ce:UpdateAnomalySubscription"
      ],
      "Resource": "*"
    }
  ]
}
```

## Set up IAM with CloudFormation

In CloudFormation, choose **Create stack** → **Upload a template file**, then upload `neonfoodmap-iam-setup.yaml`. When entering parameters, provide the project name, member passwords, monthly budget, and alert email. Confirm permission to create IAM resources and create the stack.

The CloudFormation template creates three groups: `NeonFoodmap-DevOps-Admins`, `NeonFoodmap-Backend-Devs`, and `NeonFoodmap-Frontend-Devs`, as well as self-management and MFA policies. Every member must enable MFA before using allowed services. The **Force MFA** policy enforces this requirement.

{{< event-image src="images/5-Workshop/5.2-Prerequisite/picCloudformation.jpg" alt="CloudFormation template" >}}

{{< event-image src="images/5-Workshop/5.2-Prerequisite/picCompleteStatus.jpg" alt="Completed CloudFormation stack" >}}

## Configuration and secret management during deployment

Use the following configuration convention for the GitHub Environment named production. Do not commit values into the repository, do not include credentials in screenshots, and do not use long-lived AWS_ACCESS_KEY_ID or AWS_SECRET_ACCESS_KEY on GitHub.

| Name / secret                                                                                                                                                                                                                                               | Storage location                                                                  | Purpose                                                                                                                                                                                                                                                   |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **AWS_REGION**<br />**AWS_ROLE_ARN**<br />**ECS_CLUSTER**<br />**ECS_BACKEND_SERVICE**<br />**ECS_FRONTEND_SERVICE**<br />**ECR_BACKEND_REPOSITORY**<br />**ECR_FRONTEND_REPOSITORY**<br />**VITE_API_URL** | GitHub Environment variables (production)                                         | Deployment metadata rather than secrets.**AWS_ROLE_ARN** specifies the role that GitHub Actions assumes through OIDC; this is not a credential.                                                                                                     |
| **No AWS credentials** should be stored<br />Only add third-party release tokens when truly needed                                                                                                                                                         | GitHub Environment secrets (production)                                           | Isolate secrets for the Production pipeline only. The standard pipeline uses OIDC, so do not store AWS access keys or application/database secrets on GitHub.                                                                                             |
| **DJANGO_SECRET_KEY**<br />**DB_HOST**<br />**DB_NAME**<br />**DB_USER**<br />**DB_PASSWORD**<br />**PAYPAL_CLIENT_SECRET**<br />**GOOGLE_TTS_API_KEY**                                                           | AWS Secrets Manager, for example the secret`neonfoodmap/production/application` | Runtime configuration for the application. ECS task definitions reference the secret through`valueFrom`; GitHub Actions do not read these values.                                                                                                       |
| **GITHUB_TOKEN**                                                                                                                                                                                                                                      | Automatically created by GitHub Actions for each run                              | This is not a deployment secret created by the user and should only have the workflow permissions required. The secret ARN, rotation date, and owner should be recorded in the internal operations log and not included in the public workshop materials. |
