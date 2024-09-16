---
title: "Môi trường MVAA"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

## Tạo môi trường MVAA

1. Tạo S3 bucket chứa tài nguyên
    - Truy cập [bảng điều khuyển S3](https://us-east-1.console.aws.amazon.com/s3/home?region=us-east-1#)
    - Tạo S3 Bucket MWAA

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-01.png)
        - Tạo thư mục dags: chứa các tệp dag
          ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-02.png)
        - Tải lên tệp requirements.txt (Nếu có)
        - Tải lên tệp plugin.zip (Nếu có)

          ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-03.png)
2. Tạo môi trường MVAA
    - Truy cập [bảng điều khuyển MVAA](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#environments)
    - Tạo môi trường MVAA
        - Chọn **Create environment**
        - Nhập tên environment

          ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-04.png)
        - Chọn S3 bucket nguồn

          ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-05.png)
    - Thiết lập mạng
        - Chọn **Web server/public access** để có thể truy cập UI từ internet

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-06.png)

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-07.png)
    - Chọn loại môi trường (theo nhu cầu của bạn)

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-08.png)
    - Chọn IAM role cho Airflow: có thể tạo mới nếu chưa có

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-09.png)
        - Thêm quyền thực thi cần thiết

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-10.png)
3. Mở các kết nối đên môi trường MWAA

    - Tạo [VPC endpoint](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#Endpoints:)
        1. [x] S3 endpoint
        2. [x] Log endpoint

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-11.png)
    - Cho phép truy cập UI từ internet
        - Truy
          cập [Security Group của Airflow](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#SecurityGroups:)
        - Cập nhật Inbound rule

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-12.png)
4. Truy cập MWAA UI

    - Truy cập [bảng điều khuyển MVAA](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#environments)
    - Chọn **Open Airflow UI**

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-13.png)
    - Vui lòng sử dụng **airflow** cho cả tên người dùng và mật khẩu

      ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-14.png)

## Dọn dẹp tài nguyên

- Xóa môi trường MWAA

  ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-15.png)
- Xóa VPC endpoint

  ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-16.png)
- Xóa IAM role

  ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-17.png)
- Xóa Security Group

  ![Image](/repo_pmt_ws-fcj-003/images/3/1/31-18.png)
- Xóa S3 bucket