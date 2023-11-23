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

Did manually change to IO.

```bash
[cloudshell-user@ip-10-136-42-158 ~]$ aws rds modify-db-cluster --db-cluster-identifier ex-terraform-aurora-postgresql --storage-type aurora-iopt1
{
    "DBCluster": {
        "AllocatedStorage": 1,
        "AvailabilityZones": [
            "us-east-1b",
            "us-east-1a",
            "us-east-1c"
        ],
        "BackupRetentionPeriod": 7,
        "DBClusterIdentifier": "ex-terraform-aurora-postgresql",
        "DBClusterParameterGroup": "ex-terraform-aurora-postgresql-20231123101016167400000002",
        "DBSubnetGroup": "ex-terraform-aurora-postgresql",
        "Status": "available",
        "EarliestRestorableTime": "2023-11-23T10:11:44.849000+00:00",
        "Endpoint": "",
        "ReaderEndpoint": "",
        "MultiAZ": false,
        "Engine": "aurora-postgresql",
        "EngineVersion": "14.7",
        "LatestRestorableTime": "2023-11-23T11:36:57.694000+00:00",
        "Port": 5432,
        "MasterUsername": "",
        "PreferredBackupWindow": "02:00-03:00",
        "PreferredMaintenanceWindow": "sun:05:00-sun:06:00",
        "ReadReplicaIdentifiers": [],
        "DBClusterMembers": [
            {
                "DBInstanceIdentifier": "ex-terraform-aurora-postgresql-1",
                "IsClusterWriter": true,
                "DBClusterParameterGroupStatus": "in-sync",
                "PromotionTier": 0
            }
        ],
        "VpcSecurityGroups": [
            {
                "VpcSecurityGroupId": "",
                "Status": "active"
            }
        ],
        "HostedZoneId": "",
        "StorageEncrypted": true,
        "KmsKeyId": "arn:aws:kms:us-east-1::key/",
        "DbClusterResourceId": "cluster-",
        "DBClusterArn": "arn:aws:rds:us-east-1::cluster:ex-terraform-aurora-postgresql",
        "AssociatedRoles": [],
        "IAMDatabaseAuthenticationEnabled": false,
        "ClusterCreateTime": "2023-11-23T10:10:29.886000+00:00",
        "EnabledCloudwatchLogsExports": [
            "postgresql"
        ],
        "EngineMode": "provisioned",
        "DeletionProtection": false,
        "HttpEndpointEnabled": false,
        "CopyTagsToSnapshot": false,
        "CrossAccountClone": false,
        "DomainMemberships": [],
        "TagList": [
            {
                "Key": "GithubRepo",
                "Value": "terraform-aws-rds-aurora"
            },
            {
                "Key": "GithubOrg",
                "Value": "terraform-aws-modules"
            },
            {
                "Key": "Example",
                "Value": "ex-terraform-aurora-postgresql"
            }
        ],
        "PendingModifiedValues": {
            "StorageType": "aurora-iopt1"
        },
        "AutoMinorVersionUpgrade": true,
        "NetworkType": "IPV4",
        "MasterUserSecret": {
            "SecretArn": "arn:aws:secretsmanager:us-east-1::secret:rds!cluster-",
            "SecretStatus": "active",
            "KmsKeyId": "arn:aws:kms:us-east-1::key/"
        }
    }
}
```