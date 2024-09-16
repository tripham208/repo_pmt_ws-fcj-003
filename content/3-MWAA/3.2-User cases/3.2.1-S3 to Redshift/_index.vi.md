---
title: "Chuyển dữ liệu từ Amazon S3 sang Redshift"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.2.1 </b> "
---

## 1. Tổng quan

Trong dự án này, chúng tôi sẽ xây dựng quy trình ETL cho cơ sở dữ liệu được lưu trữ trên Redshift. Chúng ta sẽ tải dữ
liệu từ S3 vào các bảng phân tầng trên Redshift và thực thi SQL để chuyển đổi dữ liệu sang bảng khác trong Star Schema
bằng MWAA

Nhóm phân tích của họ tiếp tục tìm hiểu thông tin chi tiết về những bài hát mà người dùng đang nghe.

![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-01.png)

## Dữ liệu nguồn

Tập dữ liệu được lưu trữ trong S3, udacity-dend nằm ở vùng **us-west-2**:

* Song data: `s3://udacity-dend/song_data`
* Log data: `s3://udacity-dend/log_data`

Để đọc đúng dữ liệu nhật ký `s3://udacity-dend/log_data`, bạn cần có tệp metadata sau:

* Log metadata: `s3://udacity-dend/log_json_path.json`

## Mô hình dữ liệu

### Staging tables

![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-02.png)

### Star schema tables

Từ tập dữ liệu bài hát và sự kiện, chúng tôi sẽ tạo lược đồ sao được tối ưu hóa cho các truy vấn về phân tích lượt phát
bài hát:

![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-03.png)

## 2. Chuẩn bị môi trường

1. Tạo [môi trường MWAA](../../3.1-Environment)
2. Tạo CSDL Redshift

    - Truy
      cập [Redshift](https://us-east-1.console.aws.amazon.com/redshiftv2/home?region=us-east-1#/serverless-dashboard)
    - Chọn **Create workgroup**

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-04.png)
    - Chọn **Custom credential** cho database của bạn

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-05.png)
    - Chọn **IAM role** cho database của bạn

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-06.png)
    - Chọn **VPC** cho database của bạn

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-07.png)
    - Kiểm tra thông tin và chọn **Create**

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-08.png)

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-09.png)
    - Truy cập Redshift UI với user tạo bên trên

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-10.png)

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-11.png)
    - Tải [create_tables.sql](/repo_pmt_ws-fcj-003/resources/create_tables.sql) và chạy trong Redshift

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-12.png)
3. Tạo IAM role có quyền truy cập S3
4. Thêm các kết nối đến Redshift trong MWAA
    - Truy cập [Airflow UI](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#environments)
    - Thêm Connection

        - Truy cập **Admin/Connection** thêm kết nối đến Redshift

          ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-13.png)

          ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-14.png)
    - Thêm Variable

        - Truy cập **Admin/Variable** thêm Iam role Redshift dùng để truy cập S3

          ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-15.png)

          ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-16.png)

## 3. Tạo Dag

Trong [DAG](/repo_pmt_ws-fcj-003/resources/s3_to_redshift.py), hãy thêm các tham số mặc định theo các hướng dẫn này

* DAG không phụ thuộc vào các lần chạy trước
* Khi thất bại, tác vụ sẽ được thử lại 3 lần
* Việc thử lại diễn ra sau mỗi 5 phút
* Tính năng Catchup bị tắt
* Không gửi email khi thử lại

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
    default_args=default_args,
    description='Load and transform data in Redshift with Airflow',
    schedule_interval='@hourly'
)
```

Ngoài ra, hãy cấu hình các phụ thuộc của tác vụ sao cho sau khi các phụ thuộc được thiết lập, chế độ xem biểu đồ sẽ tuân
theo luồng được hiển thị trong hình ảnh bên dưới.

![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-17.png)

```python
    start_operator >> [stage_songs_to_redshift, stage_events_to_redshift] >> load_songplays_table
load_songplays_table >> [load_song_dimension_table,
                         load_user_dimension_table,
                         load_artist_dimension_table,
                         load_time_dimension_table] >> run_quality_checks >> end_operator
```

### Tạo Operator tùy chỉnh

Để hoàn thành, bạn cần xây dựng [bốn toán tử khác nhau](/repo_pmt_ws-fcj-003/resources/plugins.zip) trong
`plugins.zip` sẽ dàn dựng dữ liệu, chuyển đổi dữ liệu và chạy kiểm tra chất lượng dữ liệu.

Hãy nhớ tận dụng các chức năng tích hợp của Airflow như các kết nối và móc nối càng nhiều càng tốt và để Airflow thực
hiện mọi công việc nặng nhọc khi có thể.

Tất cả các toán tử và phiên bản tác vụ sẽ chạy các câu lệnh SQL trên cơ sở dữ liệu Redshift. Tuy nhiên, sử dụng tham số
một cách khôn ngoan sẽ cho phép bạn xây dựng các toán tử linh hoạt, có thể tái sử dụng và có thể định cấu hình mà sau
này bạn có
thể áp dụng cho nhiều loại đường ống dữ liệu với Redshift và với các cơ sở dữ liệu khác.

#### Stage Operator

Stage operator dự kiến có thể tải bất kỳ tệp định dạng JSON nào từ S3 lên Amazon Redshift. Toán tử
tạo và chạy câu lệnh SQL COPY dựa trên các tham số được cung cấp. Các tham số của toán tử phải chỉ rõ
nơi tệp được tải trong S3 và bảng mục tiêu là gì.

Các tham số phải được sử dụng để phân biệt giữa tệp JSON. Một yêu cầu quan trọng khác của toán tử giai đoạn là
chứa một trường mẫu cho phép nó tải các tệp có dấu thời gian từ S3 dựa trên thời gian thực thi và chạy
lấp đầy.

```python
class StageToRedshiftOperator(BaseOperator):
    ui_color = '#358140'

    s3_copy = """
            copy {table_name} from {s3_part} 
            iam_role {iam_role} 
            region 'us-west-2'
            format as json {opt}
            """

    @apply_defaults
    def __init__(self,
                 redshift_conn_id="",
                 table="",
                 s3_part="",
                 iam_role="",
                 json_opt="'auto'",
                 append=False,
                 *args, **kwargs):
        super(StageToRedshiftOperator, self).__init__(*args, **kwargs)
        self.redshift_conn_id = redshift_conn_id
        self.s3_part = s3_part
        self.iam_role = iam_role
        self.json_opt = json_opt
        self.redshift = PostgresHook(postgres_conn_id=self.redshift_conn_id)
        self.table_name = table
        self.append = append

    def execute(self, context):
        if not self.append:
            self.log.info("Clearing data from destination Redshift table")
            self.redshift.run("DELETE FROM {}".format(self.table_name))

        self.log.info("Copying data from S3 to Redshift")
        self.redshift.run(
            self.s3_copy.format(
                table_name=self.table_name,
                s3_part=self.s3_part,
                iam_role=self.iam_role,
                opt=self.json_opt
            )
        )
```

#### Fact and Dimension Operators

Với dimension and fact operators, bạn có thể sử dụng lớp trợ giúp SQL được cung cấp để chạy các phép biến đổi dữ liệu.
Hầu hết
logic nằm trong các phép biến đổi SQL và toán tử được mong đợi sẽ lấy đầu vào là một câu lệnh SQL và cơ sở dữ liệu đích
để chạy truy vấn. Bạn cũng có thể định nghĩa một bảng đích sẽ chứa kết quả của
phép biến đổi.

Dimension loads thường được thực hiện bằng mẫu truncate-insert trong đó bảng đích được làm trống trước khi tải. Do đó,
bạn cũng có thể có một tham số cho phép chuyển đổi giữa các chế độ chèn khi tải kích thước. Các bảng dữ kiện
thường rất lớn nên chúng chỉ nên cho phép chức năng kiểu thêm vào.

```python
class LoadFactOperator(BaseOperator):
    ui_color = '#F98866'

    @apply_defaults
    def __init__(self,
                 redshift_conn_id="",
                 sql="",
                 table="",
                 append=False,
                 *args, **kwargs):
        super(LoadFactOperator, self).__init__(*args, **kwargs)
        self.sql = sql
        self.table = table
        self.redshift = PostgresHook(postgres_conn_id=redshift_conn_id)
        self.append = append

    def execute(self, context):
        if not self.append:
            self.log.info("Clearing data from destination Redshift Fact table")
            self.redshift.run("DELETE FROM {}".format(self.table))

        self.log.info("Load data to Redshift Fact table")
        self.redshift.run(self.sql)
```

#### Data Quality Operator

Operator cuối cùng cần tạo là toán tử chất lượng dữ liệu, được sử dụng để chạy kiểm tra trên chính dữ liệu.
Chức năng chính của toán tử là nhận một hoặc nhiều trường hợp kiểm tra dựa trên SQL cùng với kết quả mong đợi và thực
hiện
các bài kiểm tra. Đối với mỗi bài kiểm tra, kết quả kiểm tra và kết quả mong đợi cần được kiểm tra và nếu không khớp,
toán tử sẽ đưa ra ngoại lệ và tác vụ sẽ thử lại và cuối cùng sẽ thất bại.

Ví dụ, một bài kiểm tra có thể là một câu lệnh SQL kiểm tra xem một cột nhất định có chứa giá trị NULL hay không bằng
cách đếm tất cả
các hàng có NULL trong cột. Chúng ta không muốn có bất kỳ giá trị NULL nào nên kết quả mong đợi sẽ là 0 và bài kiểm tra
sẽ
so sánh kết quả của câu lệnh SQL với kết quả mong đợi.

````python
class DataQualityOperator(BaseOperator):
    ui_color = '#89DA59'

    @apply_defaults
    def __init__(self,
                 redshift_conn_id="",
                 table="",
                 sql="",
                 *args, **kwargs):
        super(DataQualityOperator, self).__init__(*args, **kwargs)
        self.sql = sql
        self.table = table
        self.redshift = PostgresHook(postgres_conn_id=redshift_conn_id)

    def execute(self, context):
        records = self.redshift.get_records(self.sql)
        if len(records) < 1 or len(records[0]) < 1:
            raise ValueError(f"Data quality check failed. {self.table} returned no results")
        num_records = records[0][0]
        if num_records > 1:
            raise ValueError(f"Data quality check failed. {self.table} contained {num_records} rows")
````

## 4. Kiểm tra kết quả

1. Tải
   lên [DAG](/repo_pmt_ws-fcj-003/resources/s3_to_redshift.py) , [plugins.zip](/repo_pmt_ws-fcj-003/resources/plugins.zip)
   vào S3 bucket của MWAA
2. Kiểm tra DAG trên [Airflow UI](https://us-east-1.console.aws.amazon.com/mwaa/home?region=us-east-1#environments)
3. Chạy DAG

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-18.png)

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-19.png)

4. Truy cập Redshift UI và query các bảng đã có

   ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-20.png)

## 5. Dọn dẹp

1. [ ] Xóa [môi trường MWAA](../../3.1-Environment/#Dọn-dẹp-tài-nguyên)
2. [ ] Xóa Redshift

    - Xóa IAM role
    - Xóa Workgroups

      ![Image](/repo_pmt_ws-fcj-003/images/3/2/1/321-21.png)