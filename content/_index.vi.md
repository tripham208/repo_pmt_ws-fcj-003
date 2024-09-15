---
title: "Airflow tích hợp với AWS"
date: "`r Sys.Date()`"
weight: 1
chapter: false
---

# Airflow tích hợp với AWS

### Tổng quan

[Apache Airflow®](https://airflow.apache.org/)  là một công cụ mã nguồn mở để lập trình, lập lịch và giám sát quy trình công việc. Trong hội
thảo này, chúng ta sẽ tìm hiểu về Apache Airflow, cài đặt nó trên localhost và triển khai nó trên AWS bằng MWAA.

[Amazon Managed Workflows for Apache Airflow](https://aws.amazon.com/vi/managed-workflows-for-apache-airflow/) Điều phối
quy trình làm việc được quản lý an toàn và có tính sẵn sàng cao cho Apache Airflow

- Triển khai Apache Airflow trên quy mô lớn mà không phải chịu gánh nặng vận hành trong việc quản lý hạ tầng cơ bản.
- Chạy khối lượng công việc Apache Airflow trong môi trường đám mây an toàn và biệt lập của riêng bạn.
- Giám sát môi trường thông qua tích hợp Amazon CloudWatch để giảm chi phí vận hành và chi phí kỹ thuật.
- Kết nối với các tài nguyên AWS, đám mây hoặc tại chỗ thông qua nhà cung cấp Apache Airflow hoặc plugin tùy chỉnh.

![Image](/repo_pmt_ws-fcj-003/images/001.png)

#### Nội dung

1. [Giới thiệu](1-Introduction/)
2. [Airflow Localhost](2-Airflow Localhost/)
3. [Amazon Managed Workflows for Apache Airflow](3-MWAA/)