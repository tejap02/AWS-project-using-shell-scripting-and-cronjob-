#!/bin/bash
###################
# AUTHOR : Tejaswi
# DATE   : 24-Sep-2024
# VERSION: V1
# This script will report the AWS Resource usage and save to file
####################################################

set -x # enable debug mode in shell scripts

# Report file with date
REPORT_FILE="/home/ubuntu/aws_report_$(date +%Y-%m-%d).txt"

echo "==== AWS Daily Resource Report ====" > $REPORT_FILE
echo "Date: $(date)" >> $REPORT_FILE
echo "===================================" >> $REPORT_FILE

# List S3 Buckets
echo -e "\n==== List of S3 Buckets ====" >> $REPORT_FILE
aws s3 ls >> $REPORT_FILE

# List EC2 Instances (InstanceId, State, Type)
echo -e "\n==== List of EC2 Instances ====" >> $REPORT_FILE
aws ec2 describe-instances \
  --query "Reservations[*].Instances[*].[InstanceId,State.Name,InstanceType]" \
  --output table >> $REPORT_FILE

# List Lambda Functions (Name + Runtime)
echo -e "\n==== List of Lambda Functions ====" >> $REPORT_FILE
aws lambda list-functions \
  --query "Functions[*].[FunctionName,Runtime]" \
  --output table >> $REPORT_FILE

# List IAM Users (UserName + UserId)
echo -e "\n==== List of IAM Users ====" >> $REPORT_FILE
aws iam list-users \
  --query "Users[*].[UserName,UserId]" \
  --output table >> $REPORT_FILE

echo -e "\nReport generated successfully: $REPORT_FILE"
