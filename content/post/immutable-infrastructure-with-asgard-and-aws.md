{
  "title": "Immutable Infrastructure with Asgard and AWS",
  "description": "Immutable Infrastructure with Asgard and AWS",
  "date": "1971-01-01",
  "url": "Immutable Infrastructure with Asgard and AWS/",
  "type": "post",
  "tags": ["immutable infrastructure","aws","packer","asgard","vagrant"],
  "draft": true
}


Asgard IAM profile:
under 2048 (autoscaling:* and ec2:* instead of specifics)
{
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "autoscaling:*",
                "cloudwatch:*",
                "dynamodb:*",
                "ec2:*",
                "elasticache:*",
                "elasticloadbalancing:*",
                "iam:PassRole",
                "route53:*",
                "rds:AuthorizeDBSecurityGroupIngress",
                "rds:CreateDBInstance",
                "rds:CreateDBSecurityGroup",
                "rds:CreateDBSnapshot",
                "rds:DeleteDBInstance",
                "rds:DeleteDBSecurityGroup",
                "rds:DeleteDBSnapshot",
                "rds:DescribeDBInstances",
                "rds:DescribeDBSecurityGroups",
                "rds:DescribeDBSnapshots",
                "rds:ModifyDBInstance",
                "rds:RestoreDBInstanceFromDBSnapshot",
                "rds:RevokeDBSecurityGroupIngress",
                "s3:AbortMultipartUpload",
                "s3:DeleteObject",
                "s3:DeleteObjectVersion",
                "s3:Get*",
                "s3:List*",
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:PutObjectVersionAcl",
                "s3:PutLifecycleConfiguration",
                "sdb:*",
                "sns:*",
                "sqs:*",
                "swf:*"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}

Proper (based on wiki):
{
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "autoscaling:CreateAutoScalingGroup",
                "autoscaling:CreateLaunchConfiguration",
                "autoscaling:CreateOrUpdateScalingTrigger",
                "autoscaling:CreateOrUpdateTags",
                "autoscaling:DeleteAutoScalingGroup",
                "autoscaling:DeleteLaunchConfiguration",
                "autoscaling:DeleteNotificationConfiguration",
                "autoscaling:DeletePolicy",
                "autoscaling:DeleteScheduledAction",
                "autoscaling:DeleteTags",
                "autoscaling:DeleteTrigger",
                "autoscaling:DescribeAdjustmentTypes",
                "autoscaling:DescribeAutoScalingGroups",
                "autoscaling:DescribeAutoScalingInstances",
                "autoscaling:DescribeAutoScalingNotificationTypes",
                "autoscaling:DescribeLaunchConfigurations",
                "autoscaling:DescribeMetricCollectionTypes",
                "autoscaling:DescribeNotificationConfigurations",
                "autoscaling:DescribePolicies",
                "autoscaling:DescribeScalingActivities",
                "autoscaling:DescribeScalingProcessTypes",
                "autoscaling:DescribeScheduledActions",
                "autoscaling:DescribeTags",
                "autoscaling:DescribeTerminationPolicyTypes",
                "autoscaling:DescribeTriggers",
                "autoscaling:DisableMetricsCollection",
                "autoscaling:EnableMetricsCollection",
                "autoscaling:ExecutePolicy",
                "autoscaling:PutNotificationConfiguration",
                "autoscaling:PutScalingPolicy",
                "autoscaling:PutScheduledUpdateGroupAction",
                "autoscaling:ResumeProcesses",
                "autoscaling:SetDesiredCapacity",
                "autoscaling:SetInstanceHealth",
                "autoscaling:SuspendProcesses",
                "autoscaling:TerminateInstanceInAutoScalingGroup",
                "autoscaling:UpdateAutoScalingGroup",
                "cloudwatch:*",
                "dynamodb:*",
                "ec2:AssociateAddress",
                "ec2:AttachVolume",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:CancelSpotInstanceRequests",
                "ec2:CopyImage",
                "ec2:CopySnapshot",
                "ec2:CreateImage",
                "ec2:CreateSecurityGroup",
                "ec2:CreateSnapshot",
                "ec2:CreateTags",
                "ec2:CreateVolume",
                "ec2:DeleteSecurityGroup",
                "ec2:DeleteSnapshot",
                "ec2:DeleteTags",
                "ec2:DeleteVolume",
                "ec2:DeregisterImage",
                "ec2:DescribeAccountAttributes",
                "ec2:DescribeAddresses",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeImageAttribute",
                "ec2:DescribeImages",
                "ec2:DescribeInstanceAttribute",
                "ec2:DescribeInstanceStatus",
                "ec2:DescribeInstances",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeRegions",
                "ec2:DescribeReservedInstances",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSnapshots",
                "ec2:DescribeSpotInstanceRequests",
                "ec2:DescribeSpotPriceHistory",
                "ec2:DescribeSubnets",
                "ec2:DescribeTags",
                "ec2:DescribeVolumes",
                "ec2:DescribeVpcs",
                "ec2:DetachVolume",
                "ec2:DisassociateAddress",
                "ec2:GetConsoleOutput",
                "ec2:ModifyImageAttribute",
                "ec2:RebootInstances",
                "ec2:RegisterImage",
                "ec2:RequestSpotInstances",
                "ec2:ResetImageAttribute",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:RevokeSecurityGroupIngress",
                "ec2:RunInstances",
                "ec2:StartInstances",
                "ec2:StopInstances",
                "ec2:TerminateInstances",
                "elasticache:*",
                "elasticloadbalancing:*",
                "iam:PassRole",
                "route53:*",
                "rds:AuthorizeDBSecurityGroupIngress",
                "rds:CreateDBInstance",
                "rds:CreateDBSecurityGroup",
                "rds:CreateDBSnapshot",
                "rds:DeleteDBInstance",
                "rds:DeleteDBSecurityGroup",
                "rds:DeleteDBSnapshot",
                "rds:DescribeDBInstances",
                "rds:DescribeDBSecurityGroups",
                "rds:DescribeDBSnapshots",
                "rds:ModifyDBInstance",
                "rds:RestoreDBInstanceFromDBSnapshot",
                "rds:RevokeDBSecurityGroupIngress",
                "s3:AbortMultipartUpload",
                "s3:DeleteObject",
                "s3:DeleteObjectVersion",
                "s3:Get*",
                "s3:List*",
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:PutObjectVersionAcl",
                "s3:PutLifecycleConfiguration",
                "sdb:*",
                "sns:*",
                "sqs:*",
                "swf:*"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}