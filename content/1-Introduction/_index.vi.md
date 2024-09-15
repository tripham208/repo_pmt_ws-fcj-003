---
title: "Giới thiệu"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

# Giới thiệu

## Apache AirFlow là gì?

[Apache Airflow®](https://airflow.apache.org/)  là một công cụ mã nguồn mở để lập trình, lập lịch và giám sát quy trình
công việc.

## DAG

* Directed Acyclic Graph là một đồ thị có hướng không chu trình, mô tả tất cả các bước xử lý dữ liệu trong một quy trình

* Quy trình công việc thường được xác định với sự trợ giúp của Đồ thị theo chu kỳ có hướng (DAG)

* Mỗi DAG được xác định trong 1 file DAG, nó định nghĩa một quy trình xử lý dữ liệu, được biểu diễn dưới dạng một đồ thị
  có hướng không chu trình, trong đó các nút là các tác vụ (tasks) và các cạnh là các phụ thuộc giữa các tác vụ.

* Các tác vụ trong DAG thường được xử lý tuần tự hoặc song song theo một lịch trình được định sẵn

* Khi một DAG được thực thi, nó được gọi là một lần chạy DAG.

## Task

* Task là một đơn vị cơ bản để thực hiện một công việc nhỏ trong quy trình xử lý dữ liệu. Mỗi Task là một bước trong quy
  trình và có thể được lập lịch thực hiện tùy theo các điều kiện cụ thể.

## Operator

* Mỗi operator đại diện cho một công việc cụ thể trong quy trình, ví dụ như đọc dữ liệu từ một nguồn dữ liệu, xử lý dữ
  liệu, hoặc ghi dữ liệu vào một nguồn dữ liệu khác.

## Sensor

* Sensor là một loại Operator được sử dụng để giám sát các sự kiện và điều kiện, và thực hiện các hành động tương ứng.

* Sensor thường được sử dụng để đợi cho đến khi một điều kiện nào đó xảy ra trước khi tiếp tục thực hiện quy trình.

## Airflow hoạt động thế nào

![Image](/repo_pmt_ws-fcj-003/images/003.png?featherlight=false&width=90pc)

Hình vẽ trên tổng quan về các thành phần cơ bản của Apache Airflow.

* Scheduler: giám sát tất cả các DAG và các tác vụ được liên kết của chúng. Đối với 1 tác vụ, khi các phụ thuộc được đáp
  ứng, Scheduler sẽ khởi tạo tác vụ đó. Nó kiểm tra các tác vụ đang hoạt động để bắt đầu theo định kỳ
* Executor: xử lý việc chạy các tác vụ này bằng cách đưa chúng cho worker để chạy
* Web server: giao diện người dùng của Airflow, hiện thị trạng thái của nhiệm vụ và cho phép người dùng tương tác với cơ
  sở dữ liệu cũng như đọc tệp nhật kỹ từ kho lưu trữ từ xa như Google Cloud Storage, S3, ...
* DAG Directory: một thư mục chứa các file DAG của các quy trình xử lý dữ liệu (data pipelines) trong Airflow.
* Metabase Database: được sử dụng bởi Scheduler, Executor và Web Server để lưu trữ thông tin quan trọng của từng DAG, ví
  dụ như các phiên bản, số liệu thống kê mỗi lần chạy, khoảng thời gian lên lịch, ..