---
title: "MWAA Phụ thuộc"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1.3 </b> "
---

# Điều kiện tiên quyết

Bạn sẽ cần những điều sau đây trước khi có thể hoàn tất các bước trên trang này.

* Quyền — Tài khoản AWS của bạn phải được quản trị viên cấp quyền truy cập vào
  chính sách kiểm soát truy
  cập [AmazonMWAAFullConsoleAccess](https://docs.aws.amazon.com/mwaa/latest/userguide/access-policies.html#console-full-access)
  cho môi trường của bạn. Ngoài ra, môi trường Amazon MWAA của bạn phải được vai trò thực thi của bạn cho phép truy cập
  vào các tài nguyên AWS mà môi trường của bạn sử dụng.

* Quyền truy cập — Nếu bạn yêu cầu quyền truy cập vào kho lưu trữ công khai để cài đặt các phụ thuộc trực tiếp trên máy
  chủ web, môi trường của bạn phải được cấu hình với quyền truy cập máy chủ web mạng công khai. Để biết thêm thông tin,
  hãy
  xem [Chế độ truy cập Apache Airflow](https://docs.aws.amazon.com/mwaa/latest/userguide/configuring-networking.html).

* Cấu hình Amazon S3 —  [Amazon S3 bucket](https://docs.aws.amazon.com/mwaa/latest/userguide/mwaa-s3-bucket.html) được
  sử dụng để lưu trữ DAG, các plugin tùy chỉnh trong plugins.zip và các phụ thuộc Python trong requirements.txt phải
  được cấu hình với Public Access Blocked và Versioning Enabled.

# Cách thức hoạt động

Trên Amazon MWAA, bạn cài đặt tất cả các phụ thuộc Python bằng cách tải tệp requirements.txt lên thùng Amazon S3 của
mình, sau đó chỉ định phiên bản của tệp trên bảng điều khiển Amazon MWAA mỗi lần bạn cập nhật tệp. Amazon MWAA chạy `pip3
install -r requirements.txt` để cài đặt các phụ thuộc Python trên trình lập lịch Apache Airflow và từng công nhân.

Để chạy các phụ thuộc Python trên môi trường của bạn, bạn phải thực hiện ba điều sau:

1. Tạo một requirements.txt tập tin cục bộ.
2. Tải dữ liệu cục bộ requirements.txt lên Amazon S3 của bạn.
3. Chỉ định phiên bản của tệp này trong trường **Requirements file** trên bảng điều khiển Amazon MWAA.

Tổng quan về sự phụ thuộc của Python

Bạn có thể cài đặt các phần bổ sung của Apache Airflow và các phần phụ thuộc Python khác từ Python Package Index (
PyPi.org), Python wheels ( .whl) hoặc các phần phụ thuộc Python được lưu trữ trên PyPi/PEP-503 Compliant Repo riêng trên
môi trường của bạn.

## Vị trí phụ thuộc Python và giới hạn kích thước

Trình lập lịch Apache Airflow và Workers tìm kiếm các gói trong requirements.txttệp và các gói này được cài đặt trên môi
trường tại `/usr/local/airflow/.local/bin`.

* Giới hạn kích thước . Chúng tôi khuyên bạn nên sử dụng tệp `requirements.txt` tham chiếu đến các thư viện có tổng kích
  thước nhỏ hơn 1 GB. Amazon MWAA cần cài đặt càng nhiều thư viện thì thời gian khởi động trên môi trường càng lâu. Mặc
  dù Amazon MWAA không giới hạn rõ ràng kích thước của các thư viện đã cài đặt, nhưng nếu không thể cài đặt các phụ
  thuộc trong vòng mười phút, dịch vụ Fargate sẽ hết thời gian chờ và cố gắng khôi phục môi trường về trạng thái ổn
  định.

# Cài đặt các phụ thuộc Python vào môi trường của bạn

Phần này mô tả cách cài đặt các phụ thuộc mà bạn đã tải lên thùng Amazon S3 của mình bằng cách chỉ định đường dẫn đến tệp requirements.txt và chỉ định phiên bản của tệp requirements.txt mỗi khi tệp được cập nhật.

## Chỉ định đường dẫn đến requirements.txtbảng điều khiển Amazon MWAA (lần đầu tiên)

Nếu đây là lần đầu tiên bạn tạo và tải tệp requirements.txtlên thùng Amazon S3, bạn cũng cần chỉ định đường dẫn đến tệp trên bảng điều khiển Amazon MWAA. Bạn chỉ cần hoàn tất bước này một lần.

1. Mở trang [Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#/environments) trên bảng điều khiển Amazon MWAA.
2. Chọn một môi trường.
3. Chọn **Edit** .
4. Trên **DAG code in Amazon S3**, chọn **Browse S3** bên cạnh **Requirements file - optional field.** .
5. Chọn requirements.txt trên  Amazon S3 của bạn.
6. Chọn *Choose** .
7. Chọn **Next**, **Update environment**.

Bạn có thể bắt đầu sử dụng các gói mới ngay sau khi môi trường của bạn hoàn tất việc cập nhật.

## Chỉ định requirements.txtphiên bản trên bảng điều khiển Amazon MWAA

Bạn cần chỉ định phiên bản requirements.txttệp của mình trên bảng điều khiển Amazon MWAA mỗi khi tải phiên bản mới của tệp requirements.txtlên thùng Amazon S3.

1. Mở trang [Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#/environments) trên bảng điều khiển Amazon MWAA.
2. Chọn một môi trường.
3. Chọn **Edit** .
4. Trên **DAG code in Amazon S3**, hãy chọn phiên bản requirements.txt trong danh sách thả xuống.
5. Chọn **Next**, **Update environment**.

Bạn có thể bắt đầu sử dụng các gói mới ngay sau khi môi trường của bạn hoàn tất việc cập nhật.

# Xem nhật ký cho  requirements.txt

Bạn có thể xem nhật ký Apache Airflow cho Scheduler để lập lịch cho quy trình làm việc của bạn và phân tích thư mục của bạn dags. Các bước sau đây mô tả cách mở nhóm nhật ký cho Scheduler trên bảng điều khiển Amazon MWAA và xem nhật ký Apache Airflow trên bảng điều khiển CloudWatch Logs.

Để xem nhật ký cho một requirements.txt
1. Mở trang Môi trườngtrên bảng điều khiển Amazon MWAA.
2. Chọn một môi trường.
3. Chọn nhóm nhật ký lập lịch luồng khí trên ngăn Giám sát .
4. Chọn `requirements_install_ip` trong **Log streams**.
5. Bạn sẽ thấy danh sách các gói đã được cài đặt trên môi trường tại `/usr/local/airflow/.local/bin`. Ví dụ:

    ```
    Collecting appdirs==1.4.4 (from -r /usr/local/airflow/.local/bin (line 1))
    Downloading https://files.pythonhosted.org/packages/3b/00/2344469e2084fb28kjdsfiuyweb47389789vxbmnbjhsdgf5463acd6cf5e3db69324/appdirs-1.4.4-py2.py3-none-any.whl  
    Collecting astroid==2.4.2 (from -r /usr/local/airflow/.local/bin (line 2))
    ```

6. Xem lại danh sách các gói và xem có gói nào gặp lỗi trong quá trình cài đặt không. Nếu có lỗi xảy ra, bạn có thể thấy lỗi tương tự như sau:

    ```
    2021-03-05T14:34:42.731-07:00
    No matching distribution found for LibraryName==1.0.0 (from -r /usr/local/airflow/.local/bin (line 4))
    No matching distribution found for LibraryName==1.0.0 (from -r /usr/local/airflow/.local/bin (line 4))
    ```