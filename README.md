# AWS Elasticache Terraform module

[![Open Source Helpers](https://www.codetriage.com/oded-dd/terraform-aws-elasticache/badges/users.svg)](https://www.codetriage.com/oded-dd/terraform-aws-elasticache)

Terraform module which creates Elasticache resources on AWS.

These types of resources are supported:

* [Elasticache Replication Group](https://www.terraform.io/docs/providers/aws/r/elasticache_replication_group.html)
* [Elasticache Subnet Group](https://www.terraform.io/docs/providers/aws/r/elasticache_subnet_group.html)
* [Elasticache Parameter Group](https://www.terraform.io/docs/providers/aws/r/elasticache_parameter_group.html)

Root module calls these modules which can also be used separately to create independent resources:

* [elasticache_replication_group](https://github.com/oded-dd/terraform-aws-elasticache/tree/master/modules/elasticache_replication_group) - creates Elasticache replication group
* [elasticache_subnet_group](https://github.com/oded-dd/terraform-aws-elasticache/tree/master/modules/elasticache_subnet_group) - creates Elasticache subnet group
* [elasticache_parameter_group](https://github.com/oded-dd/terraform-aws-elasticache/tree/master/modules/elasticache_parameter_group) - createsElasticache parameter group

## Usage

```hcl
module "elasticache-redis" {
  source = "github.com/oded-dd/terraform-aws-elasticache"

  identifier = "prod-redis"
  number_cache_clusters = 2

  engine = "redis"
  engine_version = "4.0.10"
  port = 6379

  node_type = "cache.m4.xlarge"

  automatic_failover_enabled = true

  # Elasticache parameter group
  family      = "redis4.0"

  # Elasticache subnet group
  subnet_ids = ["${data.aws_subnet_ids.prod.ids}"]

  # Elasticache security group
  security_group_ids = ["${module.redis_security_group.this_security_group_id}"]

  tags = {
    Role        = "redis"
    Environment = "prod"
  }
}
```

## Conditional creation

There is also a way to specify an existing Elasticache subnet group and parameter group name instead of creating new resources like this:

```hcl
# This Elasticache instance will be created using existing subnet and default parameter group
module "elasticache-redis" {
  source = "github.com/oded-dd/terraform-aws-elasticache"

  identifier = "test-redis"
  number_cache_clusters = 1

  engine = "redis"
  engine_version = "4.0.10"
  port = 6379

  node_type = "cache.t2.medium"

  parameter_group_name = "default.redis4.0"
  subnet_group_name = "test-redis-subnet"

  # ... omitted
}
```

## Notes

1. This module only tested for REDIS.
1. This module is used only for REDIS replication group with Cluster status disabled.
1. This module does not create Elasticache security group. Use [terraform-aws-security-group](https://github.com/terraform-aws-modules/terraform-aws-security-group) module for this.

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| apply_immediately | (Optional) Specifies whether any modifications are applied immediately, or during the next maintenance window. | string | `false` | no |
| at_rest_encryption_enabled | (Optional) Whether to enable encryption at rest. | string | `false` | no |
| auth_token | (Optional) The password used to access a password protected server | string | `` | no |
| auto_minor_version_upgrade | (Optional) Specifies whether a minor engine upgrades will be applied automatically to the underlying Cache Cluster instances during the maintenance window. | string | `true` | no |
| automatic_failover_enabled | (Optional) Specifies whether a read-only replica will be automatically promoted to read/write primary if the existing primary fails. | string | `false` | no |
| availability_zones | (Optional) A list of EC2 availability zones in which the replication group's cache clusters will be created. | list | `<list>` | no |
| create_db_instance | Whether to create this resource or not | string | `true` | no |
| engine | (Required) The name of the cache engine to be used for the clusters in this replication group. | string | - | yes |
| engine_version | (Required) The version number of the cache engine to be used for the cache clusters in this replication group. | string | - | yes |
| family | (Optional) The family of the ElastiCache parameter group | string | `` | no |
| identifier | (Required) The identifier of the resource | string | - | yes |
| maintenance_window | (Optional) Specifies the weekly time range for when maintenance on the cache cluster is performed. The format is ddd:hh24:mi-ddd:hh24:mi (24H Clock UTC). | string | `` | no |
| node_type | (Required) The compute and memory capacity of the nodes in the node group. | string | - | yes |
| notification_topic_arn | (Optional) An Amazon Resource Name (ARN) of an SNS topic to send ElastiCache notifications to. | string | `` | no |
| number_cache_clusters | (Required) The number of cache clusters (primary and replicas) this replication group will have | string | - | yes |
| parameter_group_name | (Optional) The name of the parameter group to associate with this replication group. If this argument is omitted, the default cache parameter group for the specified engine is used | string | `` | no |
| parameters | (Optional) A list of ElastiCache parameters to apply | string | `<list>` | no |
| port | (Required) The port number on which each of the cache nodes will accept connections. | string | - | yes |
| security_group_ids | (Optional) One or more Amazon VPC security groups associated with this replication group. Use this parameter only when you are creating a replication group in an Amazon Virtual Private Cloud | list | `<list>` | no |
| snapshot_arns | (Optional) A single-element string list containing an Amazon Resource Name (ARN) of a Redis RDB snapshot file stored in Amazon S3. | list | `<list>` | no |
| snapshot_name | (Optional) The name of a snapshot from which to restore data into the new node group. | string | `` | no |
| snapshot_retention_limit | (Optional) The number of days for which ElastiCache will retain automatic cache cluster snapshots before deleting them. | string | `0` | no |
| snapshot_window | (Optional) The daily time range (in UTC) during which ElastiCache will begin taking a daily snapshot of your cache cluster. | string | `` | no |
| subnet_group_name | (Optional) The name of the cache subnet group to be used for the replication group | string | `` | no |
| subnet_ids | (Required) List of VPC Subnet IDs for the cache subnet group | list | `<list>` | no |
| tags | (Optional) A mapping of tags to assign to the resource | map | `<map>` | no |
| transit_encryption_enabled | (Optional) Whether to enable encryption in transit. | string | `false` | no |

## Outputs

| Name | Description |
|------|-------------|
| this_elasticache_parameter_group_id | The ElastiCache parameter group name |
| this_elasticache_replication_group_id | The ID of the ElastiCache Replication Group |
| this_elasticache_replication_group_primary_endpoint_address | (Redis only) The address of the endpoint for the primary node in the replication group, if the cluster mode is disabled |
| this_elasticache_subnet_group_name | The name for the cache subnet group |
<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## Authors

Module managed by [Oded David](https://github.com/oded-dd).

## License

Apache 2 Licensed. See LICENSE for full details.
