+++
title = "Athena and QuickSight"
type = ""
weight = 1
pre = "5.1. "
+++

#### Các bước
- Điều kiện tiên quyết
- Bắt đầu
- Truy vấn dữ liệu với Amazon Athena
- Xây dựng bảng điều khiển Amazon QuickSight
- Thiết lập QuickSight
- Tạo biểu đồ QuickSight
- Tạo tham số QuickSight
- Tạo bộ lọc QuickSight

#### Điều kiện tiên quyết
> [!NOTE]
> Các phòng thí nghiệm [Ingestion with Glue](3-Ingestion/) và [Transforming data with Glue ETL](4-Transforming/) là điều kiện tiên quyết cho phòng thí nghiệm này.

#### Bắt đầu
Trong phòng thí nghiệm này, bạn sẽ hoàn thành các nhiệm vụ sau:

1. Truy vấn dữ liệu và tạo chế độ xem với Amazon Athena
2. Nhóm làm việc Athena để kiểm soát quyền truy cập và chi phí truy vấn
3. Xây dựng bảng điều khiển với Amazon QuickSight

#### Truy vấn dữ liệu với Amazon Athena
1. Trong bảng điều khiển dịch vụ AWS, hãy tìm kiếm **Athena**.
![image1](/image/5.AthenaAndQuicksight/001-AthenaService.png)

2. Trong **Trình chỉnh sửa truy vấn**, hãy chọn cơ sở dữ liệu mới tạo của bạn, ví dụ: **"ticketdata”**.

3. Nhấp vào bảng có tên **"parquet_sporting_event_ticket"** để kiểm tra các trường. **Lưu ý:** Kiểu cho các trường **id, sporting_event_id** và **ticketholder_id** phải là **(double)**.
![image2](/image/5.AthenaAndQuicksight/002-QueryEditorDB.png)
Tiếp theo, chúng ta sẽ truy vấn trên các bảng parquet_sporting_event, parquet_sport_team và parquet_sport location.

4. Sao chép cú pháp SQL sau vào tab Truy vấn 1 và nhấp vào **Run**.
{{< highlight go "linenos=table,linenostart=1" >}}
SELECT
    e.id AS event_id,
    e.sport_type_name AS sport,
    e.start_date_time AS event_date_time,
    h.name AS home_team,
    a.name AS away_team,
    l.name AS location,
    l.city
FROM parquet_sporting_event e,
    parquet_sport_team h,
    parquet_sport_team a,
    parquet_sport_location l
WHERE
    e.home_team_id = h.id
    AND e.away_team_id = a.id
    AND e.location_id = l.id;
{{< / highlight >}}

- Kết quả xuất hiện bên dưới cửa sổ truy vấn.
![image2](/image/5.AthenaAndQuicksight/004-Query1.png)

5. Như hình trên, nhấp vào **Tạo** rồi chọn **Xem từ truy vấn**

6. Đặt tên cho chế độ xem sporting_event_info và nhấp vào **Tạo**
![image2](/image/5.AthenaAndQuicksight/005-Query1CreateView.png)
Chế độ xem mới của bạn đã được tạo
![image2](/image/5.AthenaAndQuicksight/006-CreatedViewQuery1.png)

7. Sao chép cú pháp SQL sau vào **Tab Truy vấn 3**
{{< highlight go "linenos=table,linenostart=1" >}}
SELECT t.id AS ticket_id,
    e.event_id,
    e.sport,
    e.event_date_time,
    e.home_team,
    e.away_team,
    e.location,
    e.city,
    t.seat_level,
    t.seat_section,
    t.seat_row,
    t.seat,
    t.ticket_price,
    p.full_name AS ticketholder
FROM sporting_event_info e,
    parquet_sporting_event_ticket t,
    parquet_person p
WHERE
    t.sporting_event_id = e.event_id
    AND t.ticketholder_id = p.id
{{< / highlight >}}

8. Nhấp vào nút **Lưu dưới dạng** Đặt tên cho truy vấn này: *create_view_sporting_event_ticket_info* và một số mô tả, sau đó nhấp vào **Lưu truy vấn**.
![image2](/image/5.AthenaAndQuicksight/007-CreateNewQuerySaveAs.png)
- Quay lại trình chỉnh sửa truy vấn, bạn sẽ thấy tên truy vấn đã thay đổi. Bây giờ, nhấp vào **Chạy**
- Kết quả xuất hiện bên dưới cửa sổ truy vấn.
![image2](/image/5.AthenaAndQuicksight/008-RunQuery2.png)

9. Như được hiển thị ở trên, nhấp vào **Xem từ truy vấn.**

10. Đặt tên cho chế độ xem sporting_event_ticket_info và nhấp vào **Tạo.**
![image2](/image/5.AthenaAndQuicksight/009-Query2CreateView.png)

11. Sao chép cú pháp SQL sau vào tab Truy vấn 5.
{{< highlight go "linenos=table,linenostart=1" >}}
SELECT
    sport,
    count(distinct location) as locations,
    count(distinct event_id) as events,
    count(*) as tickets,
    avg(ticket_price) as avg_ticket_price
FROM sporting_event_ticket_info
GROUP BY 1
ORDER BY 1;
{{< / highlight >}}

- Nhấp vào **Lưu dưới dạng** và đặt tên truy vấn này: *analytics_sporting_event_ticket_info* cùng một số mô tả, sau đó nhấp vào **Lưu**.
![image2](/image/5.AthenaAndQuicksight/010-CreateNewQuery3SaveAs.png)

- Tên của Truy vấn mới 5 sẽ được đổi thành tên được chỉ định ở bước trước. Nhấp vào **Chạy**.
- Truy vấn của bạn trả về hai kết quả trong khoảng năm giây. Truy vấn quét 25 MB dữ liệu, trước khi chuyển đổi sang parquet, dữ liệu này sẽ là 1,59 GB tệp CSV.
![image2](/image/5.AthenaAndQuicksight/011-RunQuery3.png)

- Mục đích của việc lưu các truy vấn là để phân biệt rõ ràng giữa các kết quả của các truy vấn đang chạy trên một chế độ xem. Nếu không, kết quả truy vấn của bạn sẽ được lưu trong thư mục "Chưa lưu" trong vị trí thùng S3 được cung cấp cho Athena để lưu trữ kết quả truy vấn. Vui lòng điều hướng đến thùng S3 để quan sát những thay đổi này, như được hiển thị bên dưới:
![image2](/image/5.AthenaAndQuicksight/012-OutputAthenaS3Results.png)

#### Tạo Bảng điều khiển Amazon QuickSight
##### Thiết lập QuickSight
1. Trong bảng điều khiển dịch vụ AWS, hãy tìm kiếm **QuickSight**.
![image2](/image/5.AthenaAndQuicksight/013-QuicksightService.png)
Nếu đây là lần đầu tiên bạn sử dụng QuickSight, bạn sẽ được nhắc tạo tài khoản.

2. Nhấp vào **Đăng ký QuickSight**.
3. Đối với loại tài khoản, hãy chọn Phiên bản **Doanh nghiệp** mặc định.
4. Nhấp vào **Tiếp tục**.
5. Trên trang Tạo tài khoản QuickSight của bạn, hãy chọn *Không, Có thể sau* cho tiện ích bổ sung Báo cáo được phân trang. Trên màn hình mới, hãy giữ nguyên lựa chọn mặc định cho phương thức xác thực.

6. Tiếp theo, hãy chọn vùng AWS phù hợp dựa trên nơi bạn đang chạy hội thảo này và các hộp kiểm để bật tính năng tự động phát hiện, Amazon Athena và Amazon S3. Đối với tên tài khoản QuickSight, hãy cung cấp tên duy nhất (ví dụ: quicksight-lab-<initals>-<randomstring>) và địa chỉ email.
7. Chọn thùng DMS của bạn (ví dụ: "xxx-dmslabs3bucket-xxx"), Nhấp vào **Hoàn tất**.
![image2](/image/5.AthenaAndQuicksight/014-SingUpQuicksight.png)
![image2](/image/5.AthenaAndQuicksight/015-SingUpQuicksight2.png)
![image2](/image/5.AthenaAndQuicksight/016-SelectedS3Bucket.png)
Vui lòng nhấp vào **Hoàn tất**

8. Ở góc trên bên phải, nhấp vào **Phân tích mới**.
![image2](/image/5.AthenaAndQuicksight/017-QuicksightStep1.png)

9. Nhấp vào **Bộ dữ liệu mới**.
![image2](/image/5.AthenaAndQuicksight/018-QuicksightStep2.png)

10. Trên trang **Tạo Bộ dữ liệu**, chọn **Athena** làm nguồn dữ liệu.
![image2](/image/5.AthenaAndQuicksight/019-QuicksightStep3.png)

11. Đối với Tên nguồn dữ liệu, nhập *ticketdata-qs*, sau đó nhấp vào **Xác thực kết nối**.
12. Nhấp vào **Tạo nguồn dữ liệu**.
![image2](/image/5.AthenaAndQuicksight/020-QuicksightStep4.png)

13. Trong danh sách thả xuống Cơ sở dữ liệu, chọn cơ sở dữ liệu **ticketdata**.

14. Chọn bảng "**sporting_event_ticket_info**" và nhấp vào **Chọn**.
![image2](/image/5.AthenaAndQuicksight/021-QuicksightStep5.png)

15. Để hoàn tất việc tạo tập dữ liệu, hãy chọn tùy chọn **Nhập vào SPICE để phân tích nhanh hơn** và nhấp vào **Trực quan hóa**. Nếu SPICE của bạn có 0 byte khả dụng, hãy chọn tùy chọn thứ hai Truy vấn trực tiếp dữ liệu của bạn.
![image2](/image/5.AthenaAndQuicksight/022-QuicksightStep6.png)

##### Tạo Biểu đồ QuickSight
Trong phần này, chúng tôi sẽ hướng dẫn bạn một số loại biểu đồ khác nhau.

1. Trong danh sách Trường, hãy nhấp vào cột **ticket_price** để điền vào biểu đồ.

2. Nhấp vào **biểu tượng mở rộng** ở góc của trường "ticket_price" và chọn **Hiển thị dưới dạng Tiền tệ** để hiển thị số theo giá trị đô la.
![image2](/image/5.AthenaAndQuicksight/023-QSDashboard1.png)

3. Bạn có thể **thêm hình ảnh** bằng cách nhấp vào **nút Thêm** từ ngăn Hình ảnh của màn hình.
- Trong khu vực **Kiểu hình ảnh**, hãy chọn biểu tượng **Biểu đồ thanh dọc**.
- Bố cục này yêu cầu một giá trị cho trục X. Trong danh sách Trường, hãy chọn trường **event_date_time** và bạn sẽ thấy bản cập nhật hình ảnh.
- Đối với Giá trị **Trục Y**, hãy chọn **“ticket_price”** từ danh sách Trường.
![image2](/image/5.AthenaAndQuicksight/024-QSDashboard2.png)

4. Bạn có thể kéo và di chuyển các hình ảnh khác để điều chỉnh không gian trong bảng điều khiển. Trong danh sách Trường, nhấp và kéo trường **seat_level** vào hộp **Group/Color**. Bạn cũng có thể sử dụng thanh trượt bên dưới trục x để phù hợp với tất cả dữ liệu.
![image2](/image/5.AthenaAndQuicksight/025-QSDashboard3.png)
Chúng ta hãy xây dựng thêm một bước nữa bằng cách thay đổi loại biểu đồ:

5. Trong ngăn Hình ảnh, nhấp vào **Biểu đồ thanh dọc** bên dưới thay đổi loại hình ảnh và chọn biểu tượng **Biểu đồ kết hợp thanh nhóm**.

6. Trong danh sách Trường, nhấp và kéo trường **ticketholder** vào hộp Dòng.

7. Trong hộp Dòng, nhấp vào hộp thả xuống và chọn **Tổng hợp: Đếm riêng biệt** cho Tổng hợp. Sau đó, bạn có thể thấy bản cập nhật trục y ở phía bên phải.
![image2](/image/5.AthenaAndQuicksight/026-QSDashboard4.png)

8. Nhấp vào biểu tượng insight trên thanh Công cụ và khám phá thông tin insight bằng tiếng Anh đơn giản.
![image2](/image/5.AthenaAndQuicksight/027-QSDashboard5.png)
Hãy thoải mái thử nghiệm với các loại biểu đồ và trường khác nhau để hiểu rõ hơn về dữ liệu.

##### Tạo tham số QuickSight
Trong phần tiếp theo, chúng ta sẽ tạo một số tham số có điều khiển cho bảng điều khiển, sau đó gán chúng cho bộ lọc cho tất cả hình ảnh.
1. Từ thanh công cụ, chọn biểu tượng **Tham số**.
![image2](/image/5.AthenaAndQuicksight/028-ParameterIcon.png)

2. Nhấp vào **Thêm** để tạo tham số mới có Tên.

3. Đối với Tên, nhập *EventFrom*.

4. Đối với Kiểu dữ liệu, chọn **Ngày giờ**.

5. Đối với Độ chi tiết thời gian, đặt **Giờ**.

6. Đối với Giá trị mặc định, hãy chọn giá trị từ lịch làm ngày bắt đầu có trong biểu đồ của bạn cho **event_date_time**. Ví dụ: *2021-01-01 00:00*.

7. Nhấp vào **Tạo**, sau đó **đóng** hộp thoại Tham số đã thêm.
![image2](/image/5.AthenaAndQuicksight/029-CreateEventFrom.png)

8. Tạo một tham số khác với các thuộc tính sau:
- **Name:** *EventTo*
- **Data type:** *Datetime*
- Đối với **Time granularity**, hãy đặt **Hour**.
- Đối với Default value, hãy chọn giá trị từ lịch làm ngày kết thúc có trong biểu đồ của bạn cho **event_date_time**. Ví dụ: *2022-01-01 00:00*
- Nhấp vào **Create**
![image2](/image/5.AthenaAndQuicksight/030-CreateEventTo.png)

9. Trong cửa sổ tiếp theo, bạn có thể chọn bất kỳ tùy chọn nào để thực hiện bất kỳ thao tác nào với tham số. Ngoài ra, bạn có thể nhấp vào ba dấu chấm cho tham số **EventFrom** và chọn **Add control**.
![image2](/image/5.AthenaAndQuicksight/031-AddControl.png)

10. Đối với Tên hiển thị, hãy chỉ định **Sự kiện từ** và nhấp vào **Thêm**.
![image2](/image/5.AthenaAndQuicksight/032-AddDateControl.png)

11. Lặp lại quy trình để thêm điều khiển cho *EventTo* với tên hiển thị là *Event To*
![image2](/image/5.AthenaAndQuicksight/033-AddDateControl.png)
Bây giờ bạn có thể thấy và mở rộng phần Điều khiển phía trên biểu đồ.
![image2](/image/5.AthenaAndQuicksight/034-ParametersControls.png)

##### Tạo Bộ lọc QuickSight
Để hoàn tất quy trình, chúng tôi sẽ kết nối bộ lọc với các điều khiển này cho tất cả hình ảnh.

1. Từ thanh Công cụ, chọn **Bộ lọc**.

2. Nhấp vào biểu tượng dấu cộng (+) để thêm bộ lọc cho trường **event_date_time**.
![image2](/image/5.AthenaAndQuicksight/035-QSFilter.png)

3. Nhấp vào bộ lọc này để **chỉnh sửa** các thuộc tính.

4. Chọn **Tất cả hình ảnh áp dụng** trong danh sách thả xuống **Áp dụng cho**

5. Đối với loại Bộ lọc, chọn **Phạm vi Ngày & Giờ** và Điều kiện là **Giữa**.

6. Chọn tùy chọn **Sử dụng Tham số**, nhấp vào **Có** để áp dụng cho tất cả hình ảnh.

7. Đối với **Tham số Ngày bắt đầu**, chọn **EventFrom**.

8. Đối với **Tham số Ngày kết thúc**, chọn **EventTo**.
![image2](/image/5.AthenaAndQuicksight/036-EditFilter.png)

9. Nhấp vào **Áp dụng**.

Bây giờ bạn có thể nhấp vào **Xuất bản** ở góc trên bên phải màn hình để xuất bản hình ảnh của mình. Bạn cũng có thể chia sẻ bằng cách nhấp vào menu **Chia sẻ**.

Bảng điều khiển là ảnh chụp nhanh chỉ đọc của một phân tích mà bạn có thể chia sẻ với những người dùng Amazon QuickSight khác cho mục đích báo cáo. Trong Bảng điều khiển, những người dùng khác vẫn có thể sử dụng hình ảnh và dữ liệu nhưng điều đó sẽ không sửa đổi tập dữ liệu.

Bạn có thể chia sẻ một phân tích với một hoặc nhiều người dùng khác mà bạn muốn cộng tác để tạo hình ảnh. Phân tích cung cấp các cách sử dụng khác để viết và sửa đổi tập dữ liệu.