+++
title = "Chuyển đổi dữ liệu"
type = ""
weight = 4
pre = "4. "
+++

#### Giới thiệu
Trong bài thực hành này, bạn sẽ tìm hiểu về AWS Glue, một dịch vụ tích hợp dữ liệu không cần máy chủ giúp bạn dễ dàng khám phá, chuẩn bị, di chuyển và tích hợp dữ liệu từ nhiều nguồn để phân tích, học máy (ML) và phát triển ứng dụng. Bạn có thể sử dụng trình thu thập thông tin để điền các bảng vào AWS Glue Data Catalog. Đây là phương pháp chính được hầu hết người dùng AWS Glue sử dụng. Trình thu thập thông tin có thể thu thập nhiều kho dữ liệu trong một lần chạy. Sau khi hoàn tất, trình thu thập thông tin sẽ tạo hoặc cập nhật một hoặc nhiều bảng trong Data Catalog của bạn. Các tác vụ trích xuất, chuyển đổi và tải (ETL) mà bạn xác định trong AWS Glue sẽ sử dụng các bảng Data Catalog này làm nguồn và đích. Tác vụ ETL sẽ đọc và ghi vào các kho dữ liệu được chỉ định trong các bảng Data Catalog nguồn và đích.

![Arch](../../image/1.Introduction/001-TransformingDataArchitecture.png)

#### Điều kiện tiên quyết
> [!NOTE]
> Thu thập dữ liệu bằng AWS S3 là điều kiện tiên quyết cho bài thực hành này.

#### Tóm tắt
Trong bài thực hành này, bạn sẽ hoàn thành các nhiệm vụ sau. Bạn có thể chọn chỉ hoàn thành Xác thực dữ liệu và ETL để chuyển sang bài thực hành tiếp theo, nơi có thể truy vấn bảng bằng Amazon Athena và Visualize bằng Amazon Quciksight.
- [Xác thực dữ liệu và ETL](4.1/)