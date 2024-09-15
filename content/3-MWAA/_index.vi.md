---
title: "Amazon Managed Workflows for Apache Airflow"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---


[Amazon Managed Workflows for Apache Airflow](https://aws.amazon.com/vi/managed-workflows-for-apache-airflow/) Điều phối
quy trình làm việc được quản lý an toàn và có độ sẵn sàng cao cho Apache Airflow

* Triển khai Apache Airflow trên quy mô lớn mà không phải chịu gánh nặng vận hành trong việc quản lý hạ tầng cơ bản.

* Chạy khối lượng công việc Apache Airflow trong môi trường đám mây biệt lập và an toàn của riêng bạn.

* Giám sát môi trường thông qua tích hợp Amazon CloudWatch để giảm chi phí vận hành và chi phí kỹ thuật.

* Kết nối với tài nguyên AWS, đám mây hoặc tại chỗ thông qua nhà cung cấp Apache Airflow hoặc plugin tùy chỉnh.

### Cách thức hoạt động

Amazon Managed Workflows for Apache Airflow (Amazon MWAA) điều phối luồng công việc của bạn bằng Đồ thị theo chu kỳ có
hướng (DAG) được viết bằng Python. Bạn cung cấp MWAA một vùng lưu trữ Amazon Simple Storage Service (S3) nơi đặt DAG,
plugin và yêu cầu Python của bạn. Sau đó, chạy và giám sát DAG của bạn từ Bảng điều khiển quản lý AWS, giao diện dòng
lệnh (CLI), bộ công cụ phát triển phần mềm (SDK) hoặc giao diện người dùng (UI) Apache Airflow.

![Image](/repo_pmt_ws-fcj-003/images/001.png)

### Trường hợp sử dụng

* **Hỗ trợ quy trình làm việc phức tạp** : Tạo quy trình làm việc theo lịch trình hoặc theo yêu cầu để chuẩn bị và xử lý
  dữ liệu phức tạp từ các nhà cung cấp dữ liệu lớn.

* **Điều phối các công việc trích xuất, chuyển đổi và tải (ETL)** :Điều phối nhiều quy trình ETL sử dụng các công nghệ
  đa dạng trong một quy trình ETL phức tạp.

* **Chuẩn bị dữ liệu ML**: Tự động hóa quy trình của bạn để giúp các hệ thống mô hình hóa máy học (ML) thu nạp và sau đó
  đào tạo về dữ liệu.