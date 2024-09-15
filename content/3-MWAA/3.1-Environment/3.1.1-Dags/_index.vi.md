---
title : "MWAA Dags"
date :  "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1.1 </b> "
---

## Điều kiện tiên quyết

Bạn sẽ cần những điều sau đây trước khi có thể hoàn tất các bước trên trang này.

* Quyền — Tài khoản AWS của bạn phải được quản trị viên cấp quyền truy cập vào
chính sách kiểm soát truy cập [AmazonMWAAFullConsoleAccess](https://docs.aws.amazon.com/mwaa/latest/userguide/access-policies.html#console-full-access)
cho môi trường của bạn. Ngoài ra, môi trường Amazon MWAA của bạn phải được vai trò thực thi của bạn cho phép truy cập vào các tài nguyên AWS mà môi trường của bạn sử dụng.

* Quyền truy cập — Nếu bạn yêu cầu quyền truy cập vào kho lưu trữ công khai để cài đặt các phụ thuộc trực tiếp trên máy chủ web,
môi trường của bạn phải được cấu hình với quyền truy cập máy chủ web mạng công khai. Để biết thêm thông tin, hãy xem [Chế độ truy cập Apache Airflow](https://docs.aws.amazon.com/mwaa/latest/userguide/configuring-networking.html).

* Cấu hình Amazon S3 —
[Amazon S3 bucket](https://docs.aws.amazon.com/mwaa/latest/userguide/mwaa-s3-bucket.html) được sử dụng để lưu trữ DAG,
các plugin tùy chỉnh trong plugins.zip và các phụ thuộc Python
trong requirements.txt phải được cấu hình với Public Access Blocked và Versioning Enabled.


## Cách thức hoạt động

Một Đồ thị không có chu trình có hướng (DAG) được định nghĩa trong một tệp Python duy nhất định nghĩa cấu trúc của DAG dưới dạng mã. Nó
bao gồm các nội dung sau:

* Định nghĩa [DAG](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/index.html).

* [Các toán tử](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/index.html) mô tả cách chạy
DAG và [các tác vụ](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/index.html) cần chạy.

* [Mối quan hệ toán tử](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/index.html) mô tả
thứ tự chạy các tác vụ.

Để chạy nền tảng Apache Airflow trên môi trường Amazon MWAA, bạn cần sao chép định nghĩa DAG của mình vào thư mục dags
trong thùng lưu trữ của bạn. Ví dụ: thư mục DAG trong thùng lưu trữ của bạn có thể trông như thế này:

Thư mục DAG ví dụ

```
dags/
└ dag_def.py
```

Amazon MWAA tự động đồng bộ hóa các đối tượng mới và đã thay đổi từ thùng Amazon S3 của bạn với thư mục /usr/local/airflow/dags của trình lập lịch và trình làm việc Amazon MWAA
container sau mỗi 30 giây, bảo toàn hệ thống phân cấp tệp của nguồn Amazon S3,
bất kể loại tệp nào. Thời gian DAG mới xuất hiện trong Giao diện người dùng Apache Airflow của bạn được kiểm soát bởi
**scheduler.dag_dir_list_interval**. Các thay đổi đối với DAG hiện có sẽ được chọn trong vòng lặp xử lý DAG tiếp theo.

## Tải mã DAG lên Amazon S3

Bạn có thể sử dụng bảng điều khiển Amazon S3 hoặc Giao diện dòng lệnh AWS (AWS CLI) để tải mã DAG lên thùng Amazon S3
của mình. Các bước sau đây giả định rằng bạn đang tải mã (.py) lên thư mục có tên dags trong thùng Amazon S3 của mình.

## Xem các thay đổi trên Giao diện người dùng Apache Airflow

Đăng nhập vào Apache Airflow
Bạn
cần [Chính sách truy cập Giao diện người dùng Apache Airflow: AmazonMWAAWebServerAccess](https://docs.aws.amazon.com/mwaa/latest/userguide/access-policies.html#web-ui-access)

quyền cho tài khoản AWS của bạn trong AWS Identity
và
Quản lý quyền truy cập (IAM) để xem Giao diện người dùng Apache Airflow của bạn.

Để truy cập Giao diện người dùng Apache Airflow của bạn

1. Mở [Trang Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#environments) trên
Bảng điều khiển Amazon MWAA.

2. Chọn một môi trường.

3. Chọn **Open Airflow UI**.