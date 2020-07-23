Python Example
==============

The following with example CF deployment is an overview of the new AWS Elasticsearch service and highlights the differences between the legacy `elasticsearch56` and new `aws-elasticsearch` services available in the cloud.gov cloudfoundry marketplace.

## Getting started

The following example uses two services already created and available for consumption to your organization and space applications. The following services created for this example are named `old-es-service` (legacy elasticsearch56 service) and `new-es-service` (new aws-elasticsearch service).  Before running through this example, make sure to create the two services with the following cli commands:

```bash
##
## Creating the following example services
##

### The legacy elasticsearch service
$ cf create-service elasticsearch56 medium old-es-service

### The new elasticsearch service
$ cf create-service aws-elasticsearch aws-dev new-es-service
```

To get started with this example, please clone or download this repository so you can follow along and run the steps from your computer.

```bash
## Clone and navitgate into example repository
$ git clone https://github.com/cloud-gov/aws-elasticsearch-example && cd aws-elasticsearch-example
```

## Deploying

The following deployment instructions assumes that you have cloned or downloaded this `aws-elasticsearch-example` and have navigated into the root of this directory (`$ cd example/`).  From the `example` directory push the CF deployment to you cloud.gov space.

```bash
$ cf push -f manifest.yml
```

The following apps are deployed into your space:
- `elastic-tasks` is a CF app task runner used to load data and indices into the elasticsearch services.

## Running the task

To test loading some sample sample data, we will run the task. This will run a simple python script found in the [`run`](./run) file grabs our credentials to the new AWS Elasticsearch service, uploads a document and the gets that document back from the service instance.

```bash
## Run the task and all the run script
$ cf run-task elastic-tasks "./run"
# Output
# Creating task for app elastic-tasks in org cloud-gov-operators / space andrew.burnes as admin...
# OK

# Task has been submitted successfully for execution.
# task name:   5baef61c
# task id:     5


## Next you can see the task has run in the recent logs
$ cf logs --recent elastic-tasks
# Output
# [APP/TASK/5baef61c/0] OUT {'_index': 'movies', '_type': '_doc', '_id': '5', '_version': 1, '_seq_no': 0, '_primary_term': 1, 'found': True, '_source': {'title': 'Moneyball', 'director': 'Bennett Miller', 'year': '2011'}}
# [APP/TASK/5baef61c/0] OUT Exit status 0
```
