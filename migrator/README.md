Migrating Elasticsearch services with snapshots
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

To get started with this example, please clone or download this repository so you can follow along and run the steps from your computer.

```bash
## Clone and navitgate into example repository
$ git clone https://github.com/cloud-gov/aws-elasticsearch-example && cd aws-elasticsearch-example
```

## Deploying

The following deployment instructions assumes that you have cloned or downloaded this `aws-elasticsearch-example` and have navigated into the root of this directory (`$ cd migrator/`).  From the `migrator` directory push the CF deployment to you cloud.gov space.

```bash
$ cf push -f manifest.yml
```

The following apps are deployed into your space:
- `elastic-migrator` is a CF app task runner used to migrate snapshot data and indices into the elasticsearch services.

## Running migration

TODO
