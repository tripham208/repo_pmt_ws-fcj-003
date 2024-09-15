---
title : "Transfer Data from Amazon S3 to Redshift"
date :  "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.2.1 </b> "
---

## 1. Introduction

In this example we will upload files(eg: [data_sample_240101](/repo_pmt_ws-fcj-003/resources/data_sample_240101.zip)) from the local file system to Amazon S3 using Airflow running in Docker


![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-01.png)

## 2. Prepare the environment

1. S3 Bucket Landing

    - Access [S3 console](https://us-east-1.console.aws.amazon.com/s3/home?region=us-east-1#)
    - Create S3 Bucket Landing zone

      ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-02.png)
2. IAM user

    - Access [IAM console](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/home)
    - Create IAM user with S3 permission for Airflow

      ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-03.png)
3. [Run Airflow ](../../2.1-Environment)
4. Add AWS connection

    - Access **Admin/Connection**
    - Create Connection AWS

      ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-04.png)

      ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-05.png)

## 3. Create Dag

- Create [DAG](/repo_pmt_ws-fcj-003/resources/local_to_s3.py) and file upload to S3

  ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-06.png)
    - DAG includes the following components:

        - Create dag

           ```python
           default_args = {
             'owner': 'pmt',
             'start_date': pendulum.now(),
             'depends_on_past': False,
             'retries': 3,
             'retry_delay': timedelta(minutes=5),
             'catchup': False,
           }
       
           @dag(
             dag_id='local_to_s3',
             default_args=default_args,
             description='Load file from local file system to S3',
             schedule='@once'
           )
           ```
        - Create function **execute()** with operators

           ```python
            def execute():
                end_operator = EmptyOperator(task_id='End_execution')
            
                files = []
            
                for file in glob.glob(f"/opt/airflow/data/data_sample_{DATE}/*"):
                    files.append(file)
            
                for idx, file in enumerate(files, start=1):
                    file_name = file.split("/")[-1]
            
                    transfer_operator = LocalFilesystemToS3Operator(
                        task_id="local_to_s3_" + str(idx),
                        filename=file,
                        dest_key=f"date={DATE}/{file_name}",
                        dest_bucket=S3_BUCKET_NAME,
                        replace=True,
                        aws_conn_id="pmt_airflow_aws"
                    )
           ```
        - Workflow operator

           ```python
                    transfer_operator >> end_operator
           ```

## 4. Check result

1. DAG show in Airflow

   ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-07.png)

2. Run DAG

   ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-08.png)

   ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-10.png)
3. Check result

   ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-09.png)

## 5. Clean up

1. [ ] Delete S3 Bucket
2. [ ] Delete IAM user
3. [ ] Delete Airlow on Docker