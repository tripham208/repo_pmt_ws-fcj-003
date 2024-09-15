---
title: "Amazon Managed Workflows for Apache Airflow"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---


[Amazon Managed Workflows for Apache Airflow](https://aws.amazon.com/vi/managed-workflows-for-apache-airflow/) Secure
and highly available managed workflow orchestration for Apache Airflow

- Deploy Apache Airflow at scale without the operational burden of managing underlying infrastructure.
- Run Apache Airflow workloads in your own isolated and secure cloud environment.
- Monitor environments through Amazon CloudWatch integration to reduce operating costs and engineering overhead.
- Connect to AWS, cloud, or on-premises resources through Apache Airflow providers or custom plugins.

## How it works

Amazon Managed Workflows for Apache Airflow (Amazon MWAA) orchestrates your workflows using Directed Acyclic Graphs (
DAGs) written in Python. You provide MWAA an Amazon Simple Storage Service (S3) bucket where your DAGs, plugins, and
Python requirements reside. Then run and monitor your DAGs from the AWS Management Console, a command line interface (
CLI), a software development kit (SDK), or the Apache Airflow user interface (UI).

![Image](/repo_pmt_ws-fcj-003/images/001.png)

## User cases

- **Support complex workflows**: Create scheduled or on-demand workflows that prepare and process complicated data from
  big data providers.

- **Coordinate extract, transform, and load (ETL) jobs**:  Orchestrate multiple ETL processes that use diverse
  technologies within a complex ETL workflow.

- **Prepare ML data**: Automate your pipeline to help machine learning (ML) modeling systems ingest and then train on
  data.