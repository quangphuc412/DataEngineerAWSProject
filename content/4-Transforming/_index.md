+++
title = "Transforming Data"
type = ""
weight = 4
pre = "4. "
+++

#### Introduction
In this lab you will learn about AWS Glue, which is a serverless data integration service that makes it easier to discover, prepare, move, and integrate data from multiple sources for analytics, machine learning (ML), and application development. You can use a crawler to populate the AWS Glue Data Catalog with tables. This is the primary method used by most AWS Glue users. A crawler can crawl multiple data stores in a single run. Upon completion, the crawler creates or updates one or more tables in your Data Catalog. Extract, transform, and load (ETL) jobs that you define in AWS Glue use these Data Catalog tables as sources and targets. The ETL job reads from and writes to the data stores that are specified in the source and target Data Catalog tables.

![Arch](/image/1.Introduction/001-TransformingDataArchitecture.png)

#### Prerequisites
> [!NOTE]
> Data Ingestion with AWS S3 is a prerequisite for this lab.

#### Summary
In this lab you will be completing the following tasks. You can choose to complete only Data Validation and ETL in order to move to the next lab where tables can be queried using Amazon Athena and Visualize with Amazon Quciksight.
- [Data Validation and ETL](4.1/)