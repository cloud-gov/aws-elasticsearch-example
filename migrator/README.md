[WIP] Migrating Elasticsearch services with snapshots
===============================================

The following example CF deployment is an overview of migrating to the new AWS Elasticsearch service using a snapshot from the legacy Elasticsearch service.

## Getting started

The following example uses three services to create, store, and restore an Elasticsearch snapshot. In this example the services are named `old-es-service` (legacy elasticsearch56 service), `new-es-service` (new aws-elasticsearch service), and `snapshot-s3-service` (AWS S3 storage service).

For the first step, create the three services with the following CLI commands:

```bash
##
## Creating the following Elasticsearch example services
##

### The legacy elasticsearch service
$ cf create-service elasticsearch56 medium old-es-service

### The new elasticsearch service
$ cf create-service aws-elasticsearch BETA-es-dev new-es-service
```

Create a new S3 service to store the snapshot from the Elasticsearch service

```bash
##
## Creating the following S3 example service
##

### Make sure to use the "basic" plan so the snapshot is not publicly available
$ cf create-service s3 basic snapshot-s3-service
```

To continue with this example, please clone or download this repository so you can follow along and run the steps from your computer.

```bash
## Clone and navigate into the example repository
$ git clone https://github.com/cloud-gov/aws-elasticsearch-example && cd aws-elasticsearch-example
```

## Deploying

From the root directory of the repository that you should now be in (`aws-elasticsearch-example`), navigate to the `migrator` directory and push the CF deployment to your cloud.gov space:

```bash
# Navigate to the migrator sub-directory
$ cd migrator

# Push the application
$ cf push -f manifest.yml
```

When the `cf push` command finishes an `elastic-migrator` app task runner will be deployed, which is used to migrate snapshot data and indices into the Elasticsearch services.

## Running the task

With the `elastic-migrator` app deployed we can run the task to test loading sample data.  This involves running a small Python script found in the [`run`](./run) directory, which performs the following steps:

1. Loads the sample data into the legacy Elasticsearch service
1. Creates snapshot repositories in S3 for both the legacy and new Elasticsearch service
1. Generates the snapshot from the legacy service into S3
1. Restores the snapshot into the new service from S3

_**NOTE**: When creating a snapshot repository which will restore data between our legacy Elasticsearch services (v5.6) and new Elasticsearch services (v7.4), make sure the new Elasticsearch service repository is set to `readonly` because snapshot format can change across major versions and snapshots written by one version may not be visible to the other and the repository could be corrupted ([reference docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-register-repository.html))._

We can start the task with the following command:

```bash
## Run the task and call the run script
## Usage: ./run migrate [-h] -s SRC_SERVICE -d DEST_SERVICE -b S3_BUCKET_SERVICE
$ cf run-task elastic-migrator "./run migrate -s old-es-service -d new-es-service -b snapshot-s3-service"
# Output
# Creating task for app elastic-migrator in org my-org / space my-space as admin...
# OK

# Task has been submitted successfully for execution.
# task name:   abcd4321
# task id:     1


## Next you can see the task has run in the recent logs
$ cf logs --recent elastic-migrator
# Output
# [APP/TASK/abcd4321/0] OUT {'acknowledged': True}
```
