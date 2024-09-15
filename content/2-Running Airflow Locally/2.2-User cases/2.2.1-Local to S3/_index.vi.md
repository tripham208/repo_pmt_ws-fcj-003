---
title: "Tải tệp từ hệ thống tệp cục bộ lên Amazon S3"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.2.1 </b> "
---

## 1. Tổng quan

Trong ví dụ này chúng ta sẽ tải tệp từ hệ thống tệp cục bộ(ví dụ: (
eg: [data_sample_240101](/repo_pmt_ws-fcj-003/resources/data_sample_240101.zip)) ) lên Amazon S3 bằng Airflow chạy trong
Docker

![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-01.png)

## 2. Chuẩn bị môi trường

1. S3 Bucket Landing

    - Truy cập [S3 console](https://us-east-1.console.aws.amazon.com/s3/home?region=us-east-1#)
    - Tạo S3 Bucket Landing zone

      ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-02.png)
2. IAM user

    - Truy cập [IAM console](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/home)
    - Tạo IAM user được cấp quyền S3 cho Airflow

      ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-03.png)
3. [Khởi chạy Airflow ](../../2.1-Environment)
4. Thêm kêt nối đến AWS

    - Truy cập **Admin/Connection**
    - Tạo mới Connection AWS

      ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-04.png)

      ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-05.png)

## 3. Tạo Dag

- Tạo [DAG](/repo_pmt_ws-fcj-003/resources/local_to_s3.py) và chuẩn bị tệp cần tải lên S3

  ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-06.png)
    - DAG gồm các thành phần sau:

        - Khởi tạo dag

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
        - Tạo hàm **execute()** với các operator

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
        - Điều phối operator

           ```python
                    transfer_operator >> end_operator
           ```

## 4. Kiểm tra kết quả

1. DAG hiển thị trên Airflow

   ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-07.png)

2. Chạy DAG

   ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-08.png)

   ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-10.png)
3. Kiểm tra kết quả

   ![Image](/repo_pmt_ws-fcj-003/images/2/2/1/221-09.png)

## 5. Dọn dẹp

1. [ ] Xóa S3 Bucket
2. [ ] Xóa IAM user
3. [ ] Xóa Airlow trên Docker