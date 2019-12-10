# Cloud SQL Database Example

This example shows how create private Cloud SQL databases for for MySQL and PostgreSQL using the Terraform module.

**Figure 1.** *diagram of Google Cloud resources*

![architecture diagram](./diagram.png)

## Run Terraform

```
terraform init
terraform plan
terraform apply
```

## Test connection to database

1. Install the Cloud SQL Proxy:

```bash
wget https://dl.google.com/cloudsql/cloud_sql_proxy.$(uname | tr '[:upper:]' '[:lower:]').amd64 -O cloud_sql_proxy
chmod +x cloud_sql_proxy
```

2. Run the Cloud SQL proxy in the background:

```bash
MYSQL_CONN_NAME=$(terraform output mysql_conn)
PSQL_CONN_NAME=$(terraform output psql_conn)
SAFER_MYSQL_CONN_NAME=$(terraform output safer_mysql_conn)

./cloud_sql_proxy -instances=${MYSQL_CONN_NAME}=tcp:3306,${PSQL_CONN_NAME}=tcp:5432,${MYSQL_CONN_NAME}=tcp:6306 &
```

3. Get the generated user passwords:

```
echo MYSQL_PASSWORD=$(terraform output mysql_user_pass)
echo PSQL_PASSWORD=$(terraform output psql_user_pass)
echo SAFER_MYSQL_PASSWORD=$(terraform output safer_mysql_user_pass)
```

4. Test the MySQL connection:

```
mysql -udefault -p --host 127.0.0.1 default
```

> When prompted, enter the value of MYSQL_PASSWORD

5. Test the PostgreSQL connection:

```
psql -h 127.0.0.1 --user default
```

> When prompted, enter the value of PSQL_PASSWORD

4. Test the MySQL connection to the safer second instance:

```
mysql -udefault -p --host 127.0.0.1 --port 6306 default
```

> When prompted, enter the value of SAFER_MYSQL_PASSWORD

## Cleanup

1. Stop the Cloud SQL Proxy:

```bash
killall cloud_sql_proxy
```

2. Remove all resources created by terraform:

```bash
terraform destroy
```

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| mysql\_version |  | string | `"MYSQL_5_6"` | no |
| network |  | string | `"default"` | no |
| network\_name |  | string | `"mysql-psql-example"` | no |
| postgresql\_version |  | string | `"POSTGRES_9_6"` | no |
| project\_id |  | string | n/a | yes |
| region |  | string | `"us-central1"` | no |
| zone |  | string | `"us-central1-b"` | no |

## Outputs

| Name | Description |
|------|-------------|
| mysql\_conn | The connection name of the master instance to be used in connection strings |
| mysql\_user\_pass | The password for the default user. If not set, a random one will be generated and available in the generated_user_password output variable. |
| psql\_conn | The connection name of the master instance to be used in connection strings |
| psql\_user\_pass | The password for the default user. If not set, a random one will be generated and available in the generated_user_password output variable. |
| safer\_mysql\_conn | The connection name of the master instance to be used in connection strings |
| safer\_mysql\_user\_pass | The password for the default user. If not set, a random one will be generated and available in the generated_user_password output variable. |

<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->