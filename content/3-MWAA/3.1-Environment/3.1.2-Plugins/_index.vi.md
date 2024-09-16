---
title: "MWAA Plugins"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 3.1.2 </b> "
---

## Điều kiện tiên quyết

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

## Cách thức hoạt động

Để chạy các plugin tùy chỉnh trên môi trường của bạn, bạn phải thực hiện ba điều sau:

1. Tạo một `plugins.zip` tập tin cục bộ.
2. Tải `plugins.zip` tệp cục bộ lên Amazon S3 của bạn.
3. Chỉ định phiên bản của tệp này trong trường tệp Plugin trên bảng điều khiển Amazon MWAA.

# Tổng quan về plugin tùy chỉnh

Trình quản lý plugin tích hợp của Apache Airflow có thể tích hợp các tính năng bên ngoài vào lõi của nó chỉ bằng cách
thả các tệp vào một thư mục `$AIRFLOW_HOME/plugins`. Nó cho phép bạn sử dụng các toán tử, móc, cảm biến hoặc giao diện
Apache Airflow tùy chỉnh. Phần sau đây cung cấp một ví dụ về các cấu trúc thư mục phẳng và lồng nhau trong môi trường
phát triển cục bộ và các câu lệnh nhập kết quả, xác định cấu trúc thư mục trong plugins.zip.

### Thư mục plugin tùy chỉnh và giới hạn kích thước

Trình lập lịch Apache Airflow và Workers tìm kiếm các plugin tùy chỉnh trong quá trình khởi động trên vùng chứa Fargate
do AWS quản lý cho môi trường của bạn tại `/usr/local/airflow/plugins/*`

* **Cấu trúc thư mục** . Cấu trúc thư mục (at `/*`) dựa trên nội dung tệp của bạn. Ví dụ, nếu tệp của bạn chứa thư mục
  `plugins.zip` dưới dạng thư mục cấp cao nhất, thì thư mục sẽ được trích xuất `/usr/local/airflow/plugins/operators`
  vào môi trường của bạn.


* **Giới hạn kích thước** . Chúng tôi khuyên bạn nên sử dụng tệp plugins.zip có kích thước nhỏ hơn 1 GB. Tệp
  `plugins.zip`
  có kích thước càng lớn thì thời gian khởi động trên môi trường càng lâu. Mặc dù Amazon MWAA không giới hạn rõ ràng
  kích thước tệp `plugins.zip`, nhưng nếu không thể cài đặt các phần phụ thuộc trong vòng mười phút, dịch vụ Fargate sẽ
  tạm dừng và cố gắng khôi phục môi trường về trạng thái ổn định.

## Cài đặt plugin tùy chỉnh trên môi trường của bạn

Phần này mô tả cách cài đặt plugin tùy chỉnh mà bạn đã tải lên thùng Amazon S3 của mình bằng cách chỉ định đường dẫn đến
tệp plugins.zip và chỉ định phiên bản của tệp plugins.zip mỗi lần tệp zip được cập nhật.

### Chỉ định đường dẫn đến plugins.zip trên bảng điều khiển Amazon MWAA (lần đầu tiên)

Nếu đây là lần đầu tiên bạn tải plugins.zip lên thùng Amazon S3 của mình, bạn cũng cần chỉ định đường dẫn đến tệp trên
bảng điều khiển Amazon MWAA. Bạn chỉ cần hoàn thành bước này một lần.

1. Mở trang [Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#/environments) trên bảng
   điều khiển Amazon MWAA.
2. Chọn một môi trường.
3. Chọn **Edit**.
4. Trên **DAG code in Amazon S3**, chọn **Browse S3** bên cạnh **Plugins file - optional field**..
5. Chọn tệp plugins.zip trên thùng Amazon S3 của bạn.
6. Chọn **Choose**.
7. Chọn **Next**, **Update environment**.

### Chỉ định phiên bản plugins.zip trên bảng điều khiển Amazon MWAA

Bạn cần chỉ định phiên bản tệp plugins.zip của mình trên bảng điều khiển Amazon MWAA mỗi khi bạn tải lên phiên bản mới
của `plugins.zip` trong thùng Amazon S3 của mình.

1. Mở trang [Environments](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#/environments) trên bảng
   điều khiển Amazon MWAA.
2. Chọn một môi trường.
3. Chọn **Edit**.
4. Trên **DAG code in Amazon S3**, chọn phiên bản plugins.zip trong danh sách thả xuống.
5. Chọn **Next**.

## Các trường hợp sử dụng ví dụ cho plugins.zip

* Tìm hiểu cách tạo plugin tùy chỉnh trong [Plugin tùy chỉnh với Apache Hive và Hadoop](https://docs.aws.amazon.com/mwaa/latest/userguide/samples-hive.html).
* Tìm hiểu cách tạo plugin tùy chỉnh trong [Plugin tùy chỉnh để vá PythonVirtualenvOperator](https://docs.aws.amazon.com/mwaa/latest/userguide/samples-virtualenv.html).
* Tìm hiểu cách tạo plugin tùy chỉnh trong [Plugin tùy chỉnh với Oracle](https://docs.aws.amazon.com/mwaa/latest/userguide/samples-oracle.html).
* Tìm hiểu cách tạo plugin tùy chỉnh trong [Thay đổi múi giờ của DAG trên Amazon MWAA](https://docs.aws.amazon.com/mwaa/latest/userguide/samples-plugins-timezone.html).