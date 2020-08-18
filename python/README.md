Python Example
==============

The following example CF deployment is an overview of the new AWS Elasticsearch service and highlights the differences between the legacy `elasticsearch56` and new `aws-elasticsearch` services available in the cloud.gov marketplace.

## Getting started

This example uses two services already created and available for consumption to your organization and space applications. The services created for this example are named `old-es-service` (legacy elasticsearch56 service) and `new-es-service` (new aws-elasticsearch service).

First, create the two services with the following CLI commands:

```bash
##
## Creating the following example services
##

### The legacy elasticsearch service
$ cf create-service elasticsearch56 medium old-es-service

### The new elasticsearch service
$ cf create-service aws-elasticsearch BETA-es-dev new-es-service
```

To continue, please clone or download this repository so you can follow along and run the steps from your computer:

```bash
## Clone and navitgate into example repository
$ git clone https://github.com/cloud-gov/aws-elasticsearch-example && cd aws-elasticsearch-example
```

## Deploying

From the root repository directory (`aws-elasticsearch-example`), navigate into the `python` directory and push the CF deployment to you cloud.gov space:

```bash
# Navigate to the Python subdirectory
$ cd python

# Push the application
$ cf push -f manifest.yml
```

When the `cf push` command finishes an `elastic-tasks` app task runner will be deployed, which is used to load  data and indices into the Elasticsearch services.

## Running the task

With the `elastic-tasks` app deployed we can run the task to test loading sample data.  This involves running a small Python script found in the [`run`](./run) directory, which performs the following steps:

- Retrieves our credentials to the new AWS Elasticsearch service
- Uploads a document
- Retrieves the document back from the service instance

We can start the task with the following command:

```bash
## Run the task and call the run script
$ cf run-task elastic-tasks "./run load -s new-es-service"
# Output
# Creating task for app elastic-tasks in org my-org / space my-space as admin...
# OK

# Task has been submitted successfully for execution.
# task name:   5baef61c
# task id:     1


## Next you can see the task has run in the recent logs
$ cf logs --recent elastic-tasks
# Output
# [APP/TASK/5baef61c/0] OUT {'_index': 'movies', '_type': 'doc', '_id': '5', '_version': 1, '_seq_no': 0, '_primary_term': 1, 'found': True, '_source': {'title': 'Moneyball', 'director': 'Bennett Miller', 'year': '2011'}}
# [APP/TASK/5baef61c/0] OUT Exit status 0
```

Deleting the sample index

```bash
## Run the task and all the run script
$ cf run-task elastic-tasks "./run delete-indices -s new-es-service"
# Output
# Creating task for app elastic-tasks in org my-org / space my-space as admin...
# OK

# Task has been submitted successfully for execution.
# task name:   5d07a14f
# task id:     2


## Next you can see the task has run in the recent logs
$ cf logs --recent elastic-tasks
# Output
# [APP/TASK/5d07a14f/0] OUT {'acknowledged': True}
```
