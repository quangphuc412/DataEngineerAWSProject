+++
title = "Thu thập dữ liệu với AWS S3"
type = ""
weight = 3
pre = "3.1 "
+++

### **Các bước**
1. Tạo S3 bucket.
2. Mở AWS CloudShell.
3. Sao chép dữ liệu từ Amazon S3 Bucket đang dàn dựng vào S3 Bucket của bạn.
4. Xác minh dữ liệu.

#### Bước 1: Tạo S3 bucket
Tạo S3 Bucket để lưu trữ dữ liệu.
- Tên Bucket: ```ingestion-data-skip-dms-lab```

![CreateBucket](../../../image/3.Ingestion/001-CreateS3Bucket.png)
![CreateBucket](../../../image/3.Ingestion/002-CreateS3Bucket.png)
![CreateBucket](../../../image/3.Ingestion/003-CreateS3Bucket.png)
![CreateBucket](../../../image/3.Ingestion/004-CreateS3Bucket.png)

- Thành công tạo S3 Bucket

![CreateBucket](../../../image/3.Ingestion/005-SuccessCreateS3Bucket.png)

#### Bước 2: Mở AWS CloudShell
Mở [AWS CloudShell](https://console.aws.amazon.com/cloudshell/home?region=us-east-1). Nó sẽ mở một cửa sổ terminal trong trình duyệt. (Nếu có cửa sổ bật lên, hãy đóng nó lại)
> [!NOTE]
> CloudShell có sẵn ở các khu vực cụ thể và bạn sẽ tìm thấy các khu vực được hỗ trợ trong [AWS Regional Services List](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/). Bằng cách thực hiện lệnh sau, bạn sẽ sao chép dữ liệu vào đúng S3 Bucket trong bất kỳ vùng nào mà nó thuộc về (Nó cũng có thể là giữa các vùng)

#### Bước 3: Sao chép dữ liệu từ Amazon S3 Bucket dàn dựng vào S3 Bucket của bạn
Phát hành lệnh sau trong thiết bị đầu cuối và thay thế tên Bucket bằng tên của riêng bạn.
```aws s3 cp --recursive --copy-props none s3://aws-dataengineering-day.workshop.aws/data/ s3://<YourBucketName>/tickets/```

![CloudShell](../../../image/3.Ingestion/006-CloudShell-Copy-Data-S3.png)

#### Bước 4: Xác minh dữ liệu
1. Mở bảng điều khiển S3 và xem dữ liệu đã được sao chép qua thiết bị đầu cuối CloudShell.
2. Tên S3 Bucket của bạn sẽ trông như bên dưới: BucketName/bucket_folder_name/schema_name/table_name/objects/
3. Trong ví dụ phòng thí nghiệm của chúng tôi, điều này trở thành: /'BucketName'/tickets/dms_sample với một đường dẫn riêng cho mỗi table_name

![S3Bucket](../../../image/3.Ingestion/007-IngestionDataS3Bucket.png)

### **Các bước tiếp theo**
Trong phần tiếp theo của phòng thí nghiệm này, chúng ta sẽ hoàn thành các tác vụ sau:
- [Trích xuất, Chuyển đổi và Tải Data Lake bằng AWS Glue](4-Transforming/)