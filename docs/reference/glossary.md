---
title: Glossary
---

# Glossary

Configuration Items
- [Partition](#partition)
- [Connection](#connection)

Plugin Components
- [Source](#source)
- [Mapper](#mapper)
- [Row Struct](#row-struct)
- [Table](#table)
- [Enrichment](#enrichment)

Miscellaneous
- [Parquet](#parquet)
- [DuckDB](#duckdb)

## Partition

A partition is a HCL configuration item that represents an instance of a table with provided table and source configurations.

Partitions are defined in a `.tpc` file in the Tailpipe installations config directory (`~/.tailpipe/config`).

## Connection

A connection is a HCL configuration item that represents a reusable connection to a service, i.e. AWS access keys, region, etc.

Connections are defined in a `.tpc` file in the Tailpipe installations config directory (`~/.tailpipe/config`).

## Source

Sources reside in the `sources` package of the plugin and are responsible for the acquisition of log data, there are two main types of sources:

- **Artifact Source**: A source that requires obtaining artifacts, loading them into memory and extracting logs entries.
- **Custom Source**: A source that requires a custom implementation to obtain log entries, such as an API.

Sources should return a stream of log entries in the native format of acquisition, these could be predefined structs, strings (log lines), etc.

## Mapper

Mappers reside in the `mappers` package of the plugin, they take in data from a source and return a collection of the associated row struct.

## Row Struct

A row struct is a codified representation of a table schema, these reside in the `rows` package of the plugin. 

Incorporating required fields with `json` tags to define column namings and optionality, including the common fields for enrichment by embedding the `enrichment.CommonFields` struct.

## Table

Tables reside in the `tables` package of the plugin and are responsible for the collection of log entries in the format of the schema.

This is done by utilising a configured source, mapper(s) and row struct along with performing the Enrichment.


## Enrichment

Enrichment is the process of populating additional fields of a log entry, in Tailpipe this refers to the common `tp_*` fields.

## Parquet

Parquet is a columnar storage file format that is optimized for reading and writing large datasets.

Parquet is the backing file format of the logs collected via Tailpipe, these are exposed by DuckDB.

## DuckDB

DuckDB is an embeddable SQL OLAP database management system that is optimized for read-heavy workloads.