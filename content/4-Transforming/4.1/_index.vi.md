+++
title = "Xác thực dữ liệu và ETL với Glue"
type = ""
weight = 1
pre = "4.1. "
+++

#### PHẦN A: Tạo Glue Crawler cho dữ liệu tải đầy đủ ban đầu
1. Điều hướng đến [AWS Glue Console](https://console.aws.amazon.com/glue/home)
![image1](../../../image/4.Transforming/001-GlueService.png)

2. Trên menu AWS Glue, trong 'Data Catalog', chọn **Crawlers**.
![image2](../../../image/4.Transforming/002-GlueCreateCrawler.png)

3. Nhấp vào Create crawler.
4. Nhập ```glue-lab-crawler``` làm tên crawler cho dữ liệu tải ban đầu.
5. Tùy chọn, nhập mô tả. Mô tả này cũng phải mô tả và dễ nhận biết, sau đó Nhấp vào **Next**.
![image3](../../../image/4.Transforming/003-GlueCreateCrawlerName.png)

6. Trong **Chọn nguồn dữ liệu và bộ phân loại**, hãy chọn **Chưa có** và nhấp vào **Thêm nguồn dữ liệu**
![image4](../../../image/4.Transforming/004-GlueCreateCrawlerStep2.png)

7. Trên trang **Thêm kho dữ liệu**, hãy thực hiện các lựa chọn sau:
- Đối với **Nguồn dữ liệu**, hãy nhấp vào hộp thả xuống và chọn **S3**.
- Đối với **Vị trí dữ liệu S3**, hãy chọn **Trong tài khoản này**
- Đối với **Đường dẫn S3**, hãy duyệt đến thư mục đích lưu trữ các tệp CSV, ví dụ: **s3://ingestion-data-skip-dms-lab/tickets/**
- Để nguyên tất cả các tham số khác theo mặc định
- Nhấp vào **Thêm nguồn dữ liệu S3**

![image5](../../../image/4.Transforming/005-CrawlerAddS3BucketSource.png)

8. Nhấp vào **Tiếp theo**
![image6](../../../image/4.Transforming/006-GlueCreateCrawlerStep2.png)

9. Trên trang Cấu hình cài đặt bảo mật, hãy thực hiện lựa chọn sau:
- Trong Vai trò IAM hiện có, hãy chọn **AWSGlueServiceRoleDefault** được tạo sẵn cho Bài thực hành này.
- Thông tin về **AWSGlueServiceRoleDefault**
![image8](../../../image/4.Transforming/008-GlueRole.png)

10. Nhấp vào **Tiếp theo**
![image7](../../../image/4.Transforming/007-AddGlueRole.png)

11. Trên **Đặt đầu ra và lập lịch**, bên dưới **Cơ sở dữ liệu mục tiêu**, nhấp vào **Thêm cơ sở dữ liệu** để mở một tab khác.
12. Nhập ```ticketdata``` làm tên cơ sở dữ liệu của bạn và nhấp vào **Tạo cơ sở dữ liệu**
![image9](../../../image/4.Transforming/009-CreateDB.png)

![image10](../../../image/4.Transforming/010-Database.png)

13. Vui lòng quay lại trang **Đặt đầu ra và lập lịch**
- Chọn **Cơ sở dữ liệu mục tiêu** là 'ticketdata'. Vui lòng sử dụng tùy chọn làm mới ngay bên cạnh menu thả xuống để đảm bảo rằng cơ sở dữ liệu mới mà chúng tôi tạo đang được liệt kê ở đó.
- Đối với **Tiền tố được thêm vào bảng (tùy chọn)**, hãy để trống trường.
- Trong **Lịch trình thu thập thông tin**, hãy chọn tần suất là 'Theo yêu cầu'
- Nhấp vào **Tiếp theo**
![image11](../../../image/4.Transforming/011-AdvancedSettings.png)
![image12](../../../image/4.Transforming/012-CrawlerSchedule.png)

14. Xem lại thông tin và nhấp vào **Tạo trình thu thập thông tin**. Trình thu thập thông tin hiện đã sẵn sàng để chạy.
![image13](../../../image/4.Transforming/013-Reviews.png)

15. Thực thi Trình thu thập thông tin bằng cách nhấp vào nút **Chạy trình thu thập thông tin**.
![image14](../../../image/4.Transforming/014-SuccessCreateCrawler.png)
Crawler sẽ thay đổi trạng thái từ 'Đang chạy' thành 'Đang dừng', hãy đợi cho đến khi crawler trở lại trạng thái 'Sẵn sàng' (quá trình này sẽ mất vài phút), bạn có thể thấy rằng nó đã tạo 15 bảng.
![image15](../../../image/4.Transforming/015-SuccessRunCrawler.png)

16. Trong ngăn điều hướng AWS Glue, bên dưới **Danh mục dữ liệu**, hãy nhấp vào **Cơ sở dữ liệu → Bảng**. Bạn cũng có thể nhấp vào **cơ sở dữ liệu ticketdata** để duyệt các bảng.
![image16](../../../image/4.Transforming/016-CheckDB.png)

#### PHẦN B: Bài tập xác thực dữ liệu
1. Trong phần Bảng của cơ sở dữ liệu **ticketdata**, hãy nhấp vào bảng **person**.
- Bạn có thể nhận thấy rằng một số bảng (chẳng hạn như **person**) có tiêu đề cột như col0,col1,col2,col3. Trong trường hợp không có tiêu đề hoặc khi trình thu thập dữ liệu không thể xác định loại tiêu đề, tiêu đề cột mặc định sẽ được chỉ định.
- Bài tập này sử dụng bảng **person** làm ví dụ về cách giải quyết vấn đề này.

2. Nhấp vào tùy chọn **Chỉnh sửa lược đồ**
![image17](../../../image/4.Transforming/017-EditSchemaPersonTable.png)

3. Trong phần **Chỉnh sửa lược đồ**, hãy chọn hàng cho **col0** (tên cột) và nhấp vào **Chỉnh sửa**.
- Nhập id làm tên cột và nhấp vào **Lưu**.
- Lặp lại các bước trên để thay đổi tên các cột còn lại cho khớp với tên được hiển thị trong hình sau: *full_name, last_name và first_name.*
![image18](../../../image/4.Transforming/018-EditSchemaPersonTable.png)

4. Nhấp vào **Lưu dưới dạng phiên bản bảng mới**.

#### PHẦN C: Bài tập ETL dữ liệu
Điều kiện tiên quyết: Để lưu trữ dữ liệu đã xử lý ở định dạng parquet, chúng ta cần một vị trí thư mục mới cho mỗi bảng, ví dụ: đường dẫn đầy đủ cho bảng sport_team trông như thế này - s3://<s3_bucket_name>/tickets/dms_parquet/sport_team

1. Trong ngăn điều hướng bên trái, nhấp vào **Công việc ETL**.

2. Nhấp vào **“Visual ETL”**
![image19](../../../image/4.Transforming/019-ETLJob.png)

3. Chọn **Amazon S3** từ danh sách Nguồn để thêm nút **Nguồn dữ liệu - thùng S3**.
![image20](../../../image/4.Transforming/020-ETLJobStep1.png)

4. Chọn nút **Nguồn dữ liệu - thùng S3** để hiển thị các thuộc tính nguồn dữ liệu.
5. Trong bảng điều khiển bên phải bên dưới “Thuộc tính nguồn dữ liệu - S3”, hãy chọn **Bảng danh mục dữ liệu** và chọn cơ sở dữ liệu ticketdata từ danh sách thả xuống. Đối với Bảng, hãy chọn bảng sport_team.
![image21](../../../image/4.Transforming/021-ETLJobStep2.png)

6. Nhấp vào nút + và chọn Thay đổi lược đồ từ danh sách Biến đổi để thêm nút **Chuyển đổi - Thay đổi lược đồ**.
![image22](../../../image/4.Transforming/022-ETLJobStep3.png)

7. Chọn nút **Chuyển đổi - Thay đổi lược đồ** để hiển thị các thuộc tính. Trong bảng Biến đổi ở bên phải, hãy thay đổi kiểu dữ liệu của cột **id** thành *double* trong danh sách thả xuống.
![image23](../../../image/4.Transforming/023-ETLJobStep4.png)

8. Nhấp vào nút + và chọn **Amazon S3** từ danh sách Mục tiêu để thêm nút **Mục tiêu dữ liệu - thùng S3**.
![image24](../../../image/4.Transforming/024-ETLJobStep5.png)

9. Chọn nút **Mục tiêu dữ liệu - thùng S3** để hiển thị các thuộc tính. Trong bảng điều khiển bên phải, hãy đổi Định dạng thành **Parquet** trong danh sách thả xuống. Trong **Loại nén**, hãy chọn *Không nén* từ danh sách thả xuống.

10. Trong “**Vị trí mục tiêu S3**”, hãy chọn “**Duyệt S3**”, duyệt đến thùng “**ingestion-data-skip-dms-lab**”, chọn mục “**vé**” và nhấn “**Chọn**”.

11. Trong hộp văn bản, hãy thêm **dms_parquet/sport_team/** vào url S3. Đường dẫn sẽ trông giống như s3://ingestion-data-skip-dms-lab/tickets/dms_parquet/sport_team/ - đừng quên dấu / ở cuối. Công việc sẽ tự động tạo thư mục.
![image25](../../../image/4.Transforming/025-ETLJobStep6.png)

12. Cuối cùng, hãy chọn tab **Chi tiết công việc** ở trên cùng. Nhập ```Glue-Lab-SportTeamParquet``` trong phần Tên.

13. Đối với Vai trò IAM, hãy chọn vai trò có tên tương tự như AWSGlueServiceRoleDefault.
![image26](../../../image/4.Transforming/026-ETLJobStep7.png)

14. Cuộn xuống trang và bên dưới **Dấu trang công việc**, hãy chọn **Tắt** trong danh sách thả xuống. Bạn có thể dùng thử chức năng dấu trang sau trong bài thực hành này.

15. Nhấn nút **Lưu** ở góc trên bên phải để tạo công việc.

16. Khi bạn thấy thông báo **Đã tạo công việc thành công** trong biểu ngữ, hãy nhấp vào nút **Chạy** để bắt đầu công việc.

17. Chọn **Công việc** từ bảng điều hướng ở phía bên trái để xem danh sách các công việc của bạn.

18. Chọn **Giám sát** từ bảng điều hướng ở phía bên trái để xem các công việc đang chạy, tỷ lệ thành công/thất bại và nhiều số liệu thống kê khác.

19. Cuộn xuống danh sách **Job run** để xác minh rằng công việc ETL đã hoàn tất thành công. Quá trình này sẽ mất khoảng 1 phút để hoàn tất.
![image27](../../../image/4.Transforming/027-RunETLJob.png)

20. Chúng ta cần lặp lại quy trình này cho thêm 4 công việc nữa để chuyển đổi các bảng **sport_location, sporting_event, sporting_event_ticket** và **person**.
- Trong quá trình này, chúng ta sẽ cần sửa đổi các kiểu dữ liệu cột khác nhau. Chúng ta có thể lặp lại quy trình trên cho từng bảng hoặc có thể sao chép công việc đầu tiên và cập nhật thông tin chi tiết. Các bước dưới đây mô tả cách sao chép công việc - nếu tạo thủ công mỗi lần, hãy làm theo các bước trên nhưng hãy đảm bảo bạn sử dụng các giá trị đã cập nhật từ các bảng bên dưới.

21. Quay lại menu **Jobs** và chọn công việc **Glue-Lab-SportsTeamParquet** bằng cách nhấp vào hộp kiểm bên cạnh tên.

![image28](../../../image/4.Transforming/028-CloneETLJob.png)

22. Trong danh sách thả xuống **Hành động**, hãy chọn công việc **Nhân bản**. Cập nhật công việc theo các bảng sau, sau đó **Lưu** và **Chạy**.

##### Sport_Location:
Tạo một tác vụ **Glue-Lab-SportLocationParquet** với các thuộc tính sau:
![imageSL](../../../image/4.Transforming/Sport_Location.png)

##### Sporting_Event:
Tạo một tác vụ **Glue-Lab-SportingEventParquet** với các thuộc tính sau:
![imageSE](../../../image/4.Transforming/Sporting_Event.png)

##### Sporting_Event_Ticket:
Tạo một tác vụ **Glue-Lab-SportingEventTicketParquet** với các thuộc tính sau:
![imageSE](../../../image/4.Transforming/Sporting_Event.png)

![imageTable](../../../image/4.Transforming/SETTable.png)

##### Person:
Tạo một tác vụ **Glue-Lab-PersonParquet** với các thuộc tính sau thuộc tính:
![imageP](../../../image/4.Transforming/TbPerson.png)

23. Chạy thành công tất cả công việc ETL
![image](../../../image/4.Transforming/MonitoringRunJob.png)

24. S3 Bucket cho tệp Parquet đã được tạo
![image](../../../image/4.Transforming/S3Bucket_dms_parquet.png)

#### PHẦN D: Tạo trình thu thập keo cho tệp Parquet
1. Trong menu **Điều hướng keo**, trong **Danh mục dữ liệu**, hãy chọn **Trình thu thập**. Nhấp vào **Tạo trình thu thập**.
![image29](../../../image/4.Transforming/029-CreateNewCrawler.png)

2. Đối với **Tên trình thu thập**, hãy nhập ```glue-lab-parquet-crawler``` và Nhấp vào **Tiếp theo**.
![image30](../../../image/4.Transforming/030-CreateNewCrawlerStep1.png)

3. Trong màn hình tiếp theo, Chọn **nguồn dữ liệu và bộ phân loại**, chọn **Chưa có** và nhấp vào **Thêm nguồn dữ liệu**
4. Trong màn hình **Thêm kho dữ liệu**
- Đối với **Chọn kho dữ liệu**, chọn “S3”.
- Đối với **Vị trí của dữ liệu S3**, chọn “**Trong tài khoản này**”.
- Đối với **Đường dẫn S3**, hãy chỉ định Đường dẫn S3 (thư mục Parquet cha) chứa các tệp parquet lồng nhau, ví dụ: s3://xxx-dmslabs3bucket-xxx/tickets/dms_parquet/
- Để nguyên tất cả các tham số khác theo mặc định
- Nhấp vào **Thêm nguồn dữ liệu S3**.
![image31](../../../image/4.Transforming/031-CreateNewCrawlerStep2.png)

5. Trong màn hình tiếp theo, Chọn **nguồn dữ liệu và bộ phân loại**, nhấp vào **Tiếp theo**.   
6. Trên trang **Cấu hình cài đặt bảo mật**, chọn **Chọn vai trò IAM hiện có**.
- Đối với vai trò IAM, chọn vai trò hiện có “xxx-GlueLabRole-xxx” và
- Nhấp vào **Tiếp theo**.
7. Trên trang **Đặt đầu ra và lập lịch**,
- Trong Cơ sở dữ liệu mục tiêu, chọn cơ sở dữ liệu hiện có mà bạn đã tạo trước đó, ví dụ: ticketdata
- Đối với Tiền tố được thêm vào bảng (tùy chọn), nhập parquet_
- Đối với lịch trình Crawler, chọn “Theo yêu cầu” và Nhấp vào Tiếp theo.
![image32](../../../image/4.Transforming/032-CreateNewCrawlerStep3.png)

8. Xem lại trang tóm tắt và nhấp vào **Tạo trình thu thập thông tin**.
![image33](../../../image/4.Transforming/033-SuccessCreateNewCrawler.png)

9. Nhấp vào **Chạy trình thu thập thông tin**. Sau khi trình thu thập dữ liệu của bạn chạy xong, bạn sẽ thấy rằng các bảng đã được thêm vào, tùy thuộc vào số lượng chuyển đổi ETL parquet mà bạn thiết lập trong phần trước.
![image34](../../../image/4.Transforming/034-SuccessRunNewCrawler.png)

**Xác nhận bạn có thể thấy các bảng**

10. Trong ngăn điều hướng bên trái, nhấp vào **Bảng**.
11. Thêm bộ lọc *parquet* cho **Phân loại** để trả về các bảng mới tạo.
![image35](../../../image/4.Transforming/035-CheckDB.png)

**Xin chúc mừng!** Bạn đã hoàn thành **Bài thực hành Xác thực dữ liệu và ETL**