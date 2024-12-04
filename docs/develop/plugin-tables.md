---
title: Plugin Tables
---

# Plugin Table Steps

## Step 1: Table Schema

The initial step to building a table is to define the schema, this should be as close in content as possible to the data available from the log entry the table is designed to represent.

These row structs should live in the `rows` package of the plugin, and consist of the fields to match the log entry as close as possible, the fields must have `json` tags to define the name of the column which should be **snake_case** and optional nullability of the column.

Additionally, the row struct should also embed the `enrichment.CommonFields` struct from the SDK, this is to ensure every table will have the common fields.

> NOTE: Not all data types are supported.

## Step 2: Data Source(s)

Next we need to ask ourselves how we obtain our log entries, which sources do we want to support for our log entries.

Some sources may be already provided in the plugin or SDK such as `AwsS3BucketSource` which can be used to obtain files from S3.

However, may need to implement a custom source for example if logs are only available from an API.

In this event we should create a custom source in our `sources` package, which should embed `row_source.RowSourceImpl` and implement the following functions:
- `Identifier()` returning the name of the source.
- `Collect(ctx context.Context)` responsible for obtaining the log entries.

A source **should** implement collection state.

### Source Config

If configuration is required, define a schema struct also in the `sources` package named `{source_name}_config.go` and implement `GetConfigSchema()` on the source returning an instance of this struct.

### Collection State

Collection state is required for resumption of log collection to ensure that the same logs are not collected multiple times (with some instances offering benefit of allowing performance benefit by adjusting the starting offset).

The `SDK` provides a few varying types of collection state, however if none of these apply to the source, create a custom collection state.

## Step 3: Table

Tables are essentially the glue that binds together a source, mapper(s) & schema to establish a collection of log entries in the format of the schema, these reside in the `tables` package of the plugin.

Tables **MUST** embed `table.TableImpl`, this is a generic struct parameterized with 3 types:
- The `type` of the row struct.
- The `type` of the table [config](#table-config).
- The `type` of the connection. (This should be optional)

They should also implement the following functions:
- `Identifier()` returning the name of the table.
- `GetRowSchema()` returning a new instance of the row struct.
- `GetConfigSchema()` returning an empty instance of the [table config](#table-config) schema struct.
- `EnrichRow` - see [enrichment](#step-5-enrichment)
- Optionally: if any sources require options `GetSourceOptions(sourceType string)` returning `[]row_source.RowSourceOption` for the source.
- Optionally: if any sources require [mappers](#step-4-mapping-sources-to-schema), these should be initialized in the tables `Init()` function.


### Table Config

A configuration struct is required and should also have a defined schema struct in the `tables` package named `{table_name}_config.go` and implement `GetConfigSchema()` on the table returning an instance of this struct.

## Step 4: Mapping Sources to Schema

The data returned from the source may need mapping into the row struct.

Mappers reside in the `mappers` package of the plugin, they take in data from a source and return a collection of the associated row struct.

Mappers are generic structs parameterized by row struct type.

A different mapper may be required depending on the source. It's the tables responsibility to initialize a mapper based on configured source (Tables `Init()` function).

## Step 5: Enrichment

To ensure log entries are populated with the common fields (`tp_* fields`), this means that our Table needs to implement an `EnrichRow` function with the following signature, where `R` is the row struct.

```go
EnrichRow(row R, sourceEnrichmentFields *enrichment.CommonFields) (R, error)
```

The purpose of this is to populate the `enrichment.CommonFields` on our row struct, either from the source or from the existing row data.

Some of these are passed from source as `sourceEnrichmentFields` these can be set directly.

For example if we have a `ClientIP` property, this should also be added to `TpIps` and potentially `TpSourceIP` or `TPDestinationIP`.

At the very least the following fields should be set for all rows:
- `TpID` should be set to a new `xid`.
- `TpPartition` should be set to the name of the partition as defined in the HCL configuration. (Note: this needs wiring up at the moment).
- `TpIndex` should be an accountable Identifier (AWS Account ID, etc).
- `TpDate` should be a string in the format `YYYY-MM-DD`.
- `TpTimestamp` should be the timestamp associated with the log entry.
- `TpIngestTimestamp` should be the current time (ingested into tailpipe).

## Step 6: Build, Configure & Test

### Building

Run the `Makefile`:
```sh
make
```

### Configure

Create a `{pluginName}.tpc` file in the Tailpipe installations config directory (`~/.tailpipe/config`) for a partition using the new table, along with the source and any configuration options they require.

```hcl
partition "aws_cloudtrail_log" "saas_prod_use2" {
  plugin = "aws"
  source "aws_s3_bucket" {
    bucket = "aws-cloudtrail-logs-165086849490-3b37f8dd"
    prefix = "AWSLogs/o-z3cf4qoe7m/287590803701/CloudTrail/us-east-2/2024/09"
    region = "us-east-2"
    extensions = [".gz"]
    lexicographical_order = true
    access_key = "ASIA..."
    secret_key = "abc..."
    session_token = "abc..."
  }
}
```

### Testing

Run a collection on the new partition:

```sh
tailpipe collect aws_cloudtrail_log.saas_prod_use2
```