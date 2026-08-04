---
title: "Các bước chuẩn bị"
date: 2026-08-03
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---
## Tài khoản & công cụ

| **Yêu cầu**   | **Chi tiết**                                                       |
| --------------------- | ------------------------------------------------------------------------- |
| Tài khoản AWS       | Tài khoản cá nhân; Region`ap-southeast-1` (Singapore), MFA bật.    |
| AWS CLI               | AWS CLI v2, đã cấu hình profile/IAM role phù hợp.                   |
| Python                | Python 3.12 +`pip` cho Django backend.                                  |
| Node.js               | Node.js 20+ để build React/Vite frontend.                               |
| Docker                | Build/test image Djangovà React để push ECR.                           |
| Git / GitHub          | Repo`HaoWasabi/NeonFoodmap`; GitHub Actions dùng OIDC cho CI/CD.       |
| Google Text-to-Speech | API key tạo nội dung thuyết minh audio; lưu trong GitHub Secrets.     |
| PayPal Sandbox        | Thông tin sandbox để kiểm thử payment, không dùng dữ liệu thật. |
| Tệp hạ tầng        | `SourceProject/neonfoodmap-iam-setup.yaml`;                             |

## IAM permissions

Gắn IAM permission policy sau vào tài khoản AWS user của bạn để triển khai và dọn dẹp tài nguyên trong workshop này.

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

## Khởi tạo IAM bằng CloudFormation

Từ CloudFormation, chọn **Create stack** → **Upload a template file** → tải `neonfoodmap-iam-setup.yaml`. Khi nhập parameters, cung cấp tên dự án, mật khẩu cho các thành viên, ngân sách tháng và email nhận cảnh báo. Xác nhận quyền tạo IAM resources rồi tạo stack.

Template CloudFormation tạo ba nhóm (`NeonFoodmap-DevOps-Admins`, `NeonFoodmap-Backend-Devs`, `NeonFoodmap-Frontend-Devs`), và policy tự quản lý/MFA. Mọi thành viên phải bật MFA để có thể sử dụng các dịch vụ được cho phép. Việc này được cưỡng chế bởi policy **Force MFA**.

{{< event-image src="images/5-Workshop/5.2-Prerequisite/picCloudformation.jpg" alt="Cloudformation Template" >}}

{{< event-image src="images/5-Workshop/5.2-Prerequisite/picCompleteStatus.jpg" alt="Complete Status Cloudformation" >}}
