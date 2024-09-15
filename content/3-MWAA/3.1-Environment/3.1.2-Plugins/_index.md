---
title: "MWAA Plugins"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1.2 </b> "
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

To run custom plugins on your environment, you must do three things:

1. Create a plugins.zip file locally.

2. Upload the local plugins.zip file to your Amazon S3 bucket.

3. Specify the version of this file in the **Plugins file** field on the Amazon MWAA console.

# When to use the plugins

Plugins are required only for extending the Apache Airflow user interface, as outlined in
the [Apache Airflow documentation](https://airflow.apache.org/docs/apache-airflow/stable/authoring-and-scheduling/plugins.html#plugins).
Custom operators can be placed directly in the `/dags` folder alongside your DAG code.

If you need to create your own integrations with external systems, place them in the /dags folder or a subfolder within
it, but not in the `plugins.zip` folder. In Apache Airflow 2.x, plugins are primarily used for extending the UI.

Similarly, other dependencies should not be placed in `plugins.zip`. Instead, they can be stored in a location under the
Amazon S3 `/dags` folder, where they will be synchronized to each Amazon MWAA container before Apache Airflow starts.

# Custom plugins overview

Apache Airflow's built-in plugin manager can integrate external features to its core by simply dropping files in an $
AIRFLOW_HOME/plugins folder. It allows you to use custom Apache Airflow operators, hooks, sensors, or interfaces. The
following section provides an example of flat and nested directory structures in a local development environment and the
resulting import statements, which determines the directory structure within a plugins.zip.

## Custom plugins directory and size limits

The Apache Airflow Scheduler and the Workers look for custom plugins during startup on the AWS-managed Fargate container
for your environment at `/usr/local/airflow/plugins/*`.

Directory structure. The directory structure (at `/*`) is based on the contents of your plugins.zip file. For example, if
your `plugins.zip` contains the operators directory as a top-level directory, then the directory will be extracted to
`/usr/local/airflow/plugins/operators` on your environment.

Size limit. We recommend a plugins.zip file less than than 1 GB. The larger the size of a plugins.zip file, the longer
the startup time on an environment. Although Amazon MWAA doesn't limit the size of a `plugins.zip` file explicitly, if
dependencies can't be installed within ten minutes, the Fargate service will time-out and attempt to rollback the
environment to a stable state.

# Installing custom plugins on your environment

This section describes how to install the custom plugins you uploaded to your Amazon S3 bucket by specifying the path to
the plugins.zip file, and specifying the version of the plugins.zip file each time the zip file is updated.

## Specifying the path to plugins.zip on the Amazon MWAA console (the first time)

If this is the first time you're uploading a plugins.zip to your Amazon S3 bucket, you also need to specify the path to
the file on the Amazon MWAA console. You only need to complete this step once.

1. Open the [Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#/environments) on
   the Amazon MWAA console.
2. Choose an environment.
3. Choose **Edit**.
4. On the **DAG code in Amazon S3** pane, choose **Browse S3** next to the **Plugins file - optional field**.
5. Select the plugins.zip file on your Amazon S3 bucket.
6. Choose **Choose**.
7. Choose **Next**, **Update environment**.

## Specifying the plugins.zip version on the Amazon MWAA console

You need to specify the version of your plugins.zip file on the Amazon MWAA console each time you upload a new version
of your `plugins.zip` in your Amazon S3 bucket.

1. Open the [Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#/environments) on the
2. Amazon MWAA console.
3. Choose an environment.
4. Choose **Edit**.
5. On the **DAG code in Amazon S3** pane, choose a `plugins.zip`version in the dropdown list.
6. Choose **Next**.

## Example use cases for plugins.zip

* Learn how to create a custom plugin
  in [Custom plugin with Apache Hive and Hadoop](https://docs.aws.amazon.com/mwaa/latest/userguide/samples-hive.html).
* Learn how to create a custom plugin
  in [Custom plugin to patch PythonVirtualenvOperator](https://docs.aws.amazon.com/mwaa/latest/userguide/samples-virtualenv.html) .
* Learn how to create a custom plugin
  in [Custom plugin with Oracle](https://docs.aws.amazon.com/mwaa/latest/userguide/samples-oracle.html).
* Learn how to create a custom plugin
  in [Changing a DAG's timezone on Amazon MWAA](https://docs.aws.amazon.com/mwaa/latest/userguide/samples-plugins-timezone.html).