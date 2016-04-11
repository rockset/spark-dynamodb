# DynamoDB Data Source for Apache Spark

This library provides support for reading an [Amazon DynamoDB](https://aws.amazon.com/dynamodb/)
table as an [Apache Spark](https://spark.apache.org/) DataFrame. Users can run ad-hoc SQL
queries directly against DynamoDB tables, and easily build ETL pipelines that load
DynamoDB tables into another system. This library was created by the Product Science team
at [Medium](https://medium.com/).

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Quick Start](#quick-start)
- [Schemas](#schemas)
- [Configuration](#configuration)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Quick Start

You can register a DynamoDB table and run SQL queries against it.

```
val users = sqlContext.read.schema(schema).dynamodb("users)

users.registerTempTable("users")

val data = sqlContext.sql("select username from users where username = 'tc'")
```

You can create a DataFrame from a DynamoDB table and query with the Spark DSL.

```
val users = sqlContext.read.schema(schema).dynamodb("users")

val data = users.select("username").filter($"username" = "tc")
```

## Schemas

DynamoDB tables do not have a schema beyond the primary key(s). For this reason table schemas
must be provided for your scan.

At a high-level, Spark SQL schemas are a `StructType` that contains a sequence of typed
`StructField`s. At Medium we generate `StructType`s from protobuf schemas that define the data
structure stored in a particular DynamoDB table.

```
// Example schema
val schema = StructType(Seq(
  StructField("userId", LongType),
  StructField("username", StringType)))
```

For details about Spark SQL schemas, see
[[StructType]](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.StructType).

## Configuration

| Option | Description |
| --- | --- |
| `region` | AWS region of the DynamoDB table to scan. |
| `read_capacity_pct` | Percent of provisioned read capacity to use. Default: `20` |
| `page_size` | Scan page size. Default: `1000` |
| `aws_credentials_provider_chain` | Class name of the AWS provider chain to use when connecting to DynamoDB. |
| `endpoint` | DynamoDB client endpoint in `http://localhost:8000` format. This is generally not needed and intended for unit tests. |
