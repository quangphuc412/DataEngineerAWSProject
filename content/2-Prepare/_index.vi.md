+++
title = "Chuẩn bị"
type = ""
weight = 2
pre = "2. "
+++

#### **AWS Account and IAM User**
Để bắt đầu làm việc với các dịch vụ AWS, bạn sẽ cần một tài khoản AWS. Bạn cũng sẽ cần một người dùng IAM cho tài khoản này mà bạn có thể sử dụng để đăng nhập vào AWS Management Console nhằm cung cấp và cấu hình tài nguyên của mình.

[AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/) cho phép bạn quản lý quyền truy cập vào các dịch vụ và tài nguyên AWS một cách an toàn. Sử dụng IAM, bạn có thể tạo và quản lý người dùng và nhóm AWS, đồng thời sử dụng các quyền để cho phép và từ chối quyền truy cập vào các tài nguyên AWS.

#### Tạo người dùng quản trị
Trong trường hợp bạn muốn sử dụng tài khoản của riêng mình ngay bây giờ hoặc sau này và bạn chưa có người dùng IAM có quyền quản trị, bạn cần tạo một tài khoản.

1. Nếu bạn chưa có tài khoản AWS có quyền truy cập Quản trị viên: [tạo ngay bằng cách nhấp vào đây.](https://aws.amazon.com/free)

2. Sau khi có tài khoản AWS, hãy tạo người dùng IAM mới cho hội thảo này với quyền quản trị viên vào tài khoản AWS: [Tạo người dùng IAM mới để sử dụng cho hội thảo.](https://console.aws.amazon.com/iam/home?#/users$new)

3. Nhập thông tin chi tiết về người dùng: và chọn Loại quyền truy cập là Quyền truy cập theo chương trình.

![CreateAdminUser](/image/2.Prepare/002-1-CreateAdminUser.png)
![CreateAdminUser](/image/2.Prepare/002-2-CreateAdminUser.png)

4. Đính kèm Chính sách IAM AdministratorAccess, chọn Quyền truy cập của quản trị viên hoặc quyền truy cập chi tiết tùy thuộc vào các dịch vụ Amazon được sử dụng cho phòng thí nghiệm này. Đối với phòng thí nghiệm này, bạn có thể chọn Quyền truy cập của quản trị viên → Nhấp vào Tiếp theo → Bỏ qua thẻ → Đánh giá tiếp theo.
![AttachThePolicy](/image/2.Prepare/002-3-CreateAdminUser.png)
![AttachThePolicy](/image/2.Prepare/002-4-CreateAdminUser.png)

5. Nhấp để tạo người dùng mới.
6. Tạo Người dùng quản trị thành công.
![SuccessCreate](/image/2.Prepare/002-5-SuccessCreateAdimUser.png)

#### Xin chúc mừng vì đã thiết lập!