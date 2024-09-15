---
title: "MWAA Dependencies"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1.3 </b> "
---

## Prerequisites

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

## How it works

On Amazon MWAA, you install all Python dependencies by uploading a requirements.txt file to your Amazon S3 bucket, then
specifying the version of the file on the Amazon MWAA console each time you update the file. Amazon MWAA runs `pip3
install -r requirements.txt` to install the Python dependencies on the Apache Airflow scheduler and each of the workers.

To run Python dependencies on your environment, you must do three things:

1. Create a `requirements.txt` file locally.
2. Upload the local `requirements.txt`to your Amazon S3 bucket.
3. Specify the version of this file in the **Requirements file** field on the Amazon MWAA console.

## Python dependencies overview

You can install Apache Airflow extras and other Python dependencies from the Python Package Index (PyPi.org), Python
wheels (.whl), or Python dependencies hosted on a private PyPi/PEP-503 Compliant Repo on your environment.

### Python dependencies location and size limits

The Apache Airflow Scheduler and the Workers look for the packages in the requirements.txt file and the packages are
installed on the environment at `/usr/local/airflow/.local/bin`.

* Size limit. We recommend a requirements.txt file that references libraries whose combined size is less than than 1 GB.
  The more libraries Amazon MWAA needs to install, the longer the startup time on an environment. Although Amazon MWAA
  doesn't limit the size of installed libraries explicitly, if dependencies can't be installed within ten minutes, the
  Fargate service will time-out and attempt to rollback the environment to a stable state.

## Installing Python dependencies on your environment

This section describes how to install the dependencies you uploaded to your Amazon S3 bucket by specifying the path to
the requirements.txt file, and specifying the version of the requirements.txt file each time it's updated.

### Specifying the path to requirements.txt on the Amazon MWAA console (the first time)

If this is the first time you're creating and uploading a requirements.txt to your Amazon S3 bucket, you also need to
specify the path to the file on the Amazon MWAA console. You only need to complete this step once.

1. Open the [Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#/environments) page on
   the Amazon MWAA console.
2. Choose an environment.
3. Choose **Edit**.
4. On the **DAG code in Amazon S3** pane, choose **Browse S3** next to the **Requirements file - optional field.**
5. Select the requirements.txt file on your Amazon S3 bucket.
6. Choose **Choose**.
7. Choose **Next**, **Update environment**.

You can begin using the new packages immediately after your environment finishes updating.

### Specifying the requirements.txt version on the Amazon MWAA console

You need to specify the version of your requirements.txt file on the Amazon MWAA console each time you upload a new
version of your requirements.txt in your Amazon S3 bucket.

1. Open the [Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#/environments) page on
   the Amazon MWAA console.
2. Choose an environment.
3. Choose **Edit**.
4. On the **DAG code in Amazon S3** pane, choose a requirements.txt version in the dropdown list.
5. Choose **Next**, **Update environment**.

You can begin using the new packages immediately after your environment finishes updating.

## Viewing logs for your requirements.txt

You can view Apache Airflow logs for the _Scheduler_ scheduling your workflows and parsing your dags folder. The
following
steps describe how to open the log group for the _Scheduler_ on the Amazon MWAA console, and view Apache Airflow logs on
the CloudWatch Logs console.

To view logs for a requirements.txt

1. Open the [Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#/environments) page on
   the Amazon MWAA console.
2. Choose an environment.
3. Choose the Airflow scheduler log group on the Monitoring pane.
4. Choose the `requirements_install_ip` log in **Log streams**.
5. You should see the list of packages that were installed on the environment at `/usr/local/airflow/.local/bin`. For
   example:

    ```
    Collecting appdirs==1.4.4 (from -r /usr/local/airflow/.local/bin (line 1))
    Downloading https://files.pythonhosted.org/packages/3b/00/2344469e2084fb28kjdsfiuyweb47389789vxbmnbjhsdgf5463acd6cf5e3db69324/appdirs-1.4.4-py2.py3-none-any.whl  
    Collecting astroid==2.4.2 (from -r /usr/local/airflow/.local/bin (line 2))
    ```

6. Review the list of packages and whether any of these encountered an error during installation. If something went
   wrong, you may see an error similar to the following:

    ```
    2021-03-05T14:34:42.731-07:00
    No matching distribution found for LibraryName==1.0.0 (from -r /usr/local/airflow/.local/bin (line 4))
    No matching distribution found for LibraryName==1.0.0 (from -r /usr/local/airflow/.local/bin (line 4))
    ```