# aws-elasticsearch-example [Work in progress]

## About

This repository has a series of examples for migrating your cloud.gov applications
from using the `elasticsearch56` service running on the `medium-ha` and `medium` plans to the
`elasticsearch-aws` service running on the `aws-elasticsearch-prod` and `aws-elasticsearch-dev` plans powered by
[AWS Elastisearch](https://aws.amazon.com/elasticsearch-service/). The new AWS Elasticsearch services provides customers with access the Elasticsearch v7.4; upgrading the current Elasticsearch offering from v5.4.

## Major differences

The following table has a comparison of the differences between the legacy and new AWS Elasticsearch.

| |Legacy Service|AWS Service
---|---|---
Version|**v5.6**|**v7.4**
Authentication|Basic Auth|Signed HTTP Requests
Auth Credentials|`username`, `password`|`access_key`, `secret_key`
AWS Region|*N/A*|`us-gov-west-1`

- **Versions**
  - For more information about the supported v7.4 functionality from Elasticsearch in the AWS implementation see the [AWS developer guides](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/aes-supported-es-operations.html#es_version_7_4) and the [Elasticsearch reference](https://www.elastic.co/guide/en/elasticsearch/reference/7.4/index.html)
- **Authentication**
  - The AWS Elasticsearch implementation uses signed HTTP requests. You **do not** supply any `username` or `password`credentials to the requests. You must generate a signature hash based on the `access_key` and `secret_key` provided in the credentials. That signature hash is then appended to the request as a HTTP header. [See the docs](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-request-signing.html)
  - The legacy service used basic authentication so you would add the `username` and `password` credentials from the service to create the authorization token in a request (ie. `curl --user <USERNAME>:<PASSWORD> -XPUT 'localhost:9200/idx'`)
- **AWS Region**
  - *Only applicable to the new AWS Elasticsearch service*, you will _always_ be required to include the AWS region of `us-gov-west-1` when creating a signed HTTP request. It will *always* be `us-gov-west-1`.  You can see the region in the service's `host` and `uri` credentials ie (`search-cg-broker-fake-service-host.us-gov-west-1.es.amazonaws.com`)

## Examples

The following is a list of different language examples for connecting and using the updated Elasticsearch service
offering which leverages AWS Elasticsearch.

### Creating an instance

The following example compares the difference between creating an elasticsearch instance between the old and new service offerings.

```bash
##
## The following examples are creating a development instance
##

### The old way
$ cf create-service elasticsearch56 medium dev-elastic-service

### The new way
$ cf create-service aws-elasticsearch aws-dev dev-elastic-service


##
## The following examples are creating a production, high availability instance
##

### The old way
$ cf create-service elasticsearch56 medium-ha prod-elastic-service

### The new way
$ cf create-service aws-elasticsearch aws-standard prod-selastic-service
```

### Interacting with an instance

- See the [./python](./python) example for connecting, loading, and querying data using the Python language.

## Additional AWS Elasticsearch resources

- [What Is Amazon Elasticsearch Service?](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/what-is-amazon-elasticsearch-service.html)
- [Signing HTTP Requests to Amazon Elasticsearch Service](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-request-signing.html)
  - Language examples:
    - [Java](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-request-signing.html#es-request-signing-java)
    - [Python](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-request-signing.html#es-request-signing-python)
    - [Ruby](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-request-signing.html#es-request-signing-ruby)
    - [Node](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-request-signing.html#es-request-signing-node)
    - [Go](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-request-signing.html#es-request-signing-go)
- The cloud.gov [aws-broker](https://github.com/cloud-gov/aws-broker) provides the underlying marketplace service

## Contributing

See [CONTRIBUTING](CONTRIBUTING.md) for additional information.

## Public domain

This project is in the worldwide [public domain](LICENSE.md). As stated in [CONTRIBUTING](CONTRIBUTING.md):

> This project is in the public domain within the United States, and copyright and related rights in the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](https://creativecommons.org/publicdomain/zero/1.0/).
>
> All contributions to this project will be released under the CC0 dedication. By submitting a pull request, you are agreeing to comply with this waiver of copyright interest.
