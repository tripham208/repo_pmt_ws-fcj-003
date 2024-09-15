---
title: "MWAA Dags"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1.1 </b> "
---

# Prerequisites

You'll need the following before you can complete the steps on this page.

* Permissions — Your AWS account must have been granted access by your administrator to
  the [AmazonMWAAFullConsoleAccess](https://docs.aws.amazon.com/mwaa/latest/userguide/access-policies.html#console-full-access)
  access control policy for your environment. In addition, your Amazon MWAA environment must be permitted by your
  execution role to access the AWS resources used by your environment.

* Access — If you require access to public repositories to install dependencies directly on the web server, your
  environment must be configured with public network web server access. For more information, see [Apache Airflow access
  modes](https://docs.aws.amazon.com/mwaa/latest/userguide/configuring-networking.html).

* Amazon S3 configuration —
  The [Amazon S3 bucket](https://docs.aws.amazon.com/mwaa/latest/userguide/mwaa-s3-bucket.html) used to store your DAGs,
  custom plugins in plugins.zip, and Python
  dependencies in requirements.txt must be configured with Public Access Blocked and Versioning Enabled.

# How it works

A Directed Acyclic Graph (DAG) is defined within a single Python file that defines the DAG's structure as code. It
consists of the following:

* A [DAG](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/index.html) definition.

* [Operators](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/index.html) that describe how to run
  the DAG and the [tasks](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/index.html) to run.

* [Operator relationships](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/index.html) that describe
  the order in which to run the tasks.

To run an Apache Airflow platform on an Amazon MWAA environment, you need to copy your DAG definition to the dags folder
in your storage bucket. For example, the DAG folder in your storage bucket may look like this:

Example DAG folder

```
dags/
└ dag_def.py
```

Amazon MWAA automatically syncs new and changed objects from your Amazon S3 bucket to Amazon MWAA scheduler and worker
containers’ /usr/local/airflow/dags folder every 30 seconds, preserving the Amazon S3 source’s file hierarchy,
regardless of file type. The time that new DAGs take to appear in your Apache Airflow UI is controlled by
**scheduler.dag_dir_list_interval**. Changes to existing DAGs will be picked up on the next DAG processing loop.

# Uploading DAG code to Amazon S3

You can use the Amazon S3 console or the AWS Command Line Interface (AWS CLI) to upload DAG code to your Amazon S3
bucket. The following steps assume you are uploading code (.py) to a folder named dags in your Amazon S3 bucket.

# Viewing changes on your Apache Airflow UI

Logging into Apache Airflow
You
need [Apache Airflow UI access policy: AmazonMWAAWebServerAccess](https://docs.aws.amazon.com/mwaa/latest/userguide/access-policies.html#web-ui-access)
permissions for your AWS account in AWS Identity
and
Access Management (IAM) to view your Apache Airflow UI.

To access your Apache Airflow UI

1. Open the [Environments page](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#environments) on the
   Amazon MWAA console.

2. Choose an environment.

3. Choose **Open Airflow UI**.