+++
title = "Athena and SageMaker (Không bắt buộc)"
type = ""
weight = 2
pre = "5.2. "
+++

#### Giới thiệu
[Amazon SageMaker](https://aws.amazon.com/sagemaker/) là một nền tảng học máy toàn diện cho phép bạn xây dựng, đào tạo và triển khai các mô hình học máy trong AWS. Đây là một dịch vụ có tính mô-đun cao cho phép bạn sử dụng từng thành phần này độc lập với nhau.

Bạn sẽ học:
- Cách sử dụng sổ ghi chép Jupyter trong SageMaker để tích hợp với hồ dữ liệu bằng Athena
- Điền khung dữ liệu để thao tác dữ liệu.

![image](../../../image/1.Introduction/001-AthenaSageMakerArchitecture.png)

Quy trình chuẩn bị dữ liệu này để đáp ứng nhu cầu của các thuật toán ML là lặp đi lặp lại. Để chuẩn bị dữ liệu, chúng tôi sẽ cung cấp các định nghĩa bảng trong Athena trong sổ ghi chép Jupyter trong SageMaker.

Sổ ghi chép Jupyter rất phổ biến trong giới khoa học dữ liệu và được sử dụng để trực quan hóa dữ liệu, thực hiện phân tích thống kê, hoàn tất thao tác dữ liệu và chuẩn bị dữ liệu cho công việc học máy.

#### Điều kiện tiên quyết
> [!NOTE]
> Các phòng thí nghiệm [Nhập bằng Glue](3-Nhập/) và [Chuyển đổi dữ liệu bằng Glue ETL](4-Chuyển đổi/) là điều kiện tiên quyết cho phòng thí nghiệm này.

#### Tạo Amazon SageMaker Notebook Instance
1. Đi đến [Amazon SageMaker](https://aws.amazon.com/sagemaker/) từ bảng điều khiển AWS
![image](../../../image/5.AthenaAndQuicksight/039-SageMakerService.png)

2. Trong ngăn điều hướng Amazon SageMaker, hãy đi đến **Notebook instances** và nhấp vào **Create notebook instance**.
![image](../../../image/5.AthenaAndQuicksight/040-CreateNotebooksInstances.png)

3. Nhập các giá trị sau để tạo notebook instance
- Nhập tên cho Notebook instance, ví dụ: *datalake-Sagemaker*
- Bạn có thể để Notebook instance type làm giá trị mặc định **ml.t3.medium** cho phòng thí nghiệm này
- Để Elastic Inference là *none*. Điều này là để thêm tài nguyên bổ sung.
- Trong **Vai trò IAM**, chúng ta cần chọn một vai trò cho phiên bản sổ tay trong Amazon SageMaker để tương tác với Amazon S3. Vì vai trò không tồn tại, từ tùy chọn thả xuống, hãy chọn **Tạo vai trò mới**.
- Trong cửa sổ bật lên **Tạo vai trò IAM**, hãy chọn **Bất kỳ thùng S3 nào** như hiển thị bên dưới và nhấp vào **Tạo vai trò**.
![image](../../../image/5.AthenaAndQuicksight/041-CreateNewRole.png)

- Sau khi vai trò IAM được tạo, hãy nhấp vào liên kết đến vai trò IAM, thao tác này sẽ mở vai trò IAM trong một tab mới. Chúng ta sẽ thêm quyền IAM để cho phép truy cập vào Athena từ SageMaker.
![image](../../../image/5.AthenaAndQuicksight/042-CreateNotebooksInstances.png)

- Nhấp vào **Thêm quyền** và chọn **Đính kèm chính sách**.
- Lọc chính sách theo **“Athena”**, kiểm tra chính sách được quản lý **AmazonAthenaFullAccess** và nhấp vào nút **Đính kèm chính sách** ở cuối màn hình.
![image](../../../image/5.AthenaAndQuicksight/043-AttachPolicy.png)

- Quay lại tab trình duyệt **SageMaker Create notebook instance**.
- Để nguyên tất cả các tùy chọn khác với giá trị mặc định. Nhấp vào **Create notebook instance**.

4. Đợi notebook instance được tạo và Trạng thái thay đổi thành **InService**. Nhấp vào liên kết **Open Jupyter** trong cột **Actions**.
![image](../../../image/5.AthenaAndQuicksight/044-SuccessCreateNotebookInstances.png)

5. Giao diện notebook mở ra trong một tab mới.
![image](../../../image/5.AthenaAndQuicksight/045-JupyterNotebook.png)

#### Kết nối sổ ghi chép SageMaker Jupyter với Athena
1. Trong tab sổ ghi chép Jupyter, nhấp vào **Mới** và chọn **conda_python3** cho hạt nhân. Thao tác này sẽ mở sổ ghi chép trong một tab mới.
![image](../../../image/5.AthenaAndQuicksight/046-CreateNewNotebook.png)
    > [!NOTE]
    > Amazon SageMaker cung cấp một số hạt nhân cho Jupyter, bao gồm hỗ trợ cho Python 3, MXNet, TensorFlow và PySpark. Bài tập này sử dụng Python vì nó bao gồm thư viện pandas.

2. Trong sổ ghi chép, nhập lệnh sau vào một ô và nhấp vào **Chạy** để cài đặt trình điều khiển Athena JDBC. (PyAthena là một ứng dụng khách tương thích với Python [DB API 2.0 (PEP 249)](https://www.python.org/dev/peps/pep-0249/) dành cho [trình điều khiển Amazon Athena JDBC](http://docs.aws.amazon.com/athena/latest/ug/connect-with-jdbc.html).)
{{< highlight go "linenos=table,linenostart=1" >}}
!pip install PyAthena[SQLAlchemy]
{{< / highlight >}}

    ![image](../../../image/5.AthenaAndQuicksight/047-PipInstall.png)

    Kiểm tra đầu ra rằng PyAthena đã được cài đặt thành công. Nếu bạn thấy lỗi liên quan đến awscli 1.x, bạn có thể bỏ qua.

#### Làm việc trong Pandas
Sau khi trình điều khiển Athena được cài đặt, bạn có thể sử dụng kết nối JDBC để kết nối với Athena và điền vào khung dữ liệu pandas. Đối với các nhà khoa học dữ liệu, làm việc với dữ liệu thường được chia thành nhiều giai đoạn: thu thập và làm sạch dữ liệu, phân tích và lập mô hình dữ liệu, sau đó sắp xếp kết quả phân tích thành một biểu mẫu phù hợp để vẽ đồ thị hoặc hiển thị dạng bảng. Pandas là công cụ lý tưởng cho tất cả các tác vụ này.

1. Bạn có thể tải dữ liệu bảng Athena từ kho dữ liệu vào khung dữ liệu pandas và áp dụng máy học. Sao chép mã sau vào ô sổ ghi chép của bạn và thay thế bằng vùng, vị trí thùng S3 và tên cơ sở dữ liệu của bạn.

    Vùng của bạn: chạy lệnh sau trong ô sổ ghi chép để lấy vùng AWS hiện tại.
{{< highlight go "linenos=table,linenostart=1" >}}
!aws configure get region
{{< / highlight >}}

    Tên Cơ sở dữ liệu Athena của bạn, đây là tên Cơ sở dữ liệu Glue mà bạn đã tạo trong phòng thí nghiệm Glue, ví dụ: **ticketdata**. Cập nhật vị trí thùng S3 có tên chứa chuỗi *ingestion-data-skip-dms-lab* theo sau là **athena/**, để trông giống như s3://ingestion-data-skip-dms-lab/athena/.
{{< highlight go "linenos=table,linenostart=1" >}}
from sqlalchemy import create_engine
import pandas as pd

s3_staging_dir = "s3://ingestion-data-skip-dms-lab/athena/"
connection_string = f"awsathena+rest://:@athena.us-east-1.amazonaws.com:443/ticketdata?s3_staging_dir={s3_staging_dir}"

engine = create_engine(connection_string)

df = pd.read_sql('SELECT * FROM "ticketdata"."nfl_stadium_data" order by stadium limit 10;', engine)
df
{{< / highlight >}}

2. Nhấp vào **Chạy** và khung dữ liệu sẽ hiển thị đầu ra truy vấn.
![image](../../../image/5.AthenaAndQuicksight/048-RunQuery.png)
Trong truy vấn này, bạn đang tải tất cả thông tin về sân vận động nfl vào khung dữ liệu pandas từ bảng nfl_stadium_data.
    > [!NOTE]
    > Nếu bạn gặp lỗi SageMaker không có quyền thực thi Athena. Bạn cần thêm Athena Access vào vai trò Sagemaker như các bước cung cấp trong phần trước.

3. Bạn có thể áp dụng các thuật toán ML khác nhau trong các khung dữ liệu pandas đã điền. Ví dụ: vẽ sơ đồ dữ liệu. Sao chép mã sau vào sổ ghi chép của bạn. Trong truy vấn này, bạn đang tải tất cả thông tin về vé chẵn vào khung dữ liệu pandas từ bảng *sporting_event_ticket_info*.
{{< highlight go "linenos=table,linenostart=1" >}}
df = pd.read_sql('SELECT sport, \
    event_date_time, \
    home_team,away_team, \
    city, \
    count(*) as tickets, \
    sum(ticket_price) as total_tickets_amt, \
    avg(ticket_price) as avg_ticket_price, \
    max(ticket_price) as max_ticket_price, \
    min(ticket_price) as min_ticket_price  \
    FROM "ticketdata"."sporting_event_ticket_info" \
    group by 1,2,3,4,5 \
    order by 1,2,3,4,5  limit 1000;', engine)
df
{{< / highlight >}}

4. Nhấp vào **Chạy** và khung dữ liệu sẽ hiển thị đầu ra truy vấn.
![image](../../../image/5.AthenaAndQuicksight/049-RunQuery.png)

5. Trong ô thực thi mới, sao chép mã sau
{{< highlight go "linenos=table,linenostart=1" >}}
import matplotlib.pyplot as plt
df.plot(x='event_date_time',y='avg_ticket_price')
{{< / highlight >}}

6. Nhấp vào Chạy và bạn sẽ thấy biểu đồ dữ liệu được vẽ bằng thư viện matplotlib.
![image](../../../image/5.AthenaAndQuicksight/050-VisualizationData.png)

#### Dọn dẹp
1. Đi đến [SageMaker Notebook Instances console](https://console.aws.amazon.com/sagemaker/home#/notebook-instances)
2. Chọn phiên bản notebook mà bạn đã tạo, ví dụ: *datalake-Sagemaker*.
3. Chọn **Actions → Stop**, phiên bản notebook sẽ mất vài phút để dừng.
4. Sau khi phiên bản notebook dừng, hãy chọn **Actions → Delete** và chọn **Delete** trong cửa sổ bật lên xác nhận để xóa phiên bản.