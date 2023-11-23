# RDS Aurora PostgreSQL Terraform Module testing

```bash
aws cloudformation describe-stacks --stack-name tf-aurora-pgsql-state --query 'Stacks[0].Outputs' | \
jq '{bucket: (.[] | select(.OutputKey == "StateBucketName").OutputValue), 
     dynamodb_table: (.[] | select(.OutputKey == "LockTableName").OutputValue),
     encrypt: "true",
     kms_key_id: (.[] | select(.OutputKey == "KmsKeyId").OutputValue),
     region: (.[] | select(.OutputKey == "Region").OutputValue),
     role_arn: (.[] | select(.OutputKey == "RoleArn").OutputValue)}' > terraform-init.tfvars.json

```

```bash
docker run --rm -it -v ${PWD}:/terraform -w=/terraform --user "$(id -u):$(id -g)" -e AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY -e AWS_SESSION_TOKEN hashicorp/terraform:latest init -backend-config=/terraform/terraform-init.tfvars.json
```

This should not trigger TF changes.