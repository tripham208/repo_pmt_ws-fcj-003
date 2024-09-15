---
title: "Môi trường cục bộ"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1 </b> "
---

# Môi trường cục bộ

## Khởi tạo môi trường

1. Cài đặt [Docker](https://www.docker.com/)
2. Bạn tải xuống
    - [docker-compose.yaml](/repo_pmt_ws-fcj-003/resources/docker-compose.yaml)
    - [Dockerfile](/repo_pmt_ws-fcj-003/resources/Dockerfile)
    - [requirements.txt](/repo_pmt_ws-fcj-003/resources/requirements.txt)
3. Khởi tạo thư mục theo cấu trúc bên dưới:

   <img alt="Image" height="560" src="/repo_pmt_ws-fcj-003/images/2/1/21-01.png" width="280"/>

4. Thiết lập thư mục đồng bộ với docker container

   ![Image](/repo_pmt_ws-fcj-003/images/2/1/21-02.png)
5. Chạy docker compose

   ![Image](/repo_pmt_ws-fcj-003/images/2/1/21-03.png)
6. Truy cập trang chủ Airflow  http://localhost:8080/ và sẽ nhắc bạn đăng nhập. Vui lòng sử dụng **airflow** cho cả tên
   người dùng và mật khẩu

   ![Image](/repo_pmt_ws-fcj-003/images/2/1/21-04.png)

    - Có một số dags ví dụ hiện diện nếu được bật: **AIRFLOW__CORE__LOAD_EXAMPLES: 'true'**

   ![Image](/repo_pmt_ws-fcj-003/images/2/1/21-02.png)