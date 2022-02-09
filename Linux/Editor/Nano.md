**1. Cài đặt Nano trên Ubuntu**
```
sudo apt install nano -y
```
**1.1 Cài đặt Nano trên CentOS**
```
sudo yum install epel-release nano -y
```
**2. Mở và tạo tập tin**
Để mở tệp tin hiện có hoặc để tạo tệp mới, hãy nhập lệnh nano kèm theo tên tệp tin:
```
nano tên-file
```
Thao tác này sẽ mở một cửa sổ soạn thảo mới và bạn có thể bắt đầu chỉnh sửa tệp. Ở dưới cùng của cửa sổ, có một danh sách các phím tắt cơ bản nhất để sử dụng với trình soạn thảo nano.

Tất cả các lệnh được bắt đầu bằng ký tự ^ hoặc M. Biểu tượng dấu mũ (^) đại diện cho phím Ctrl. Ví dụ: các lệnh ^ J có nghĩa là nhấn các phím Ctrl và J cùng một lúc. Chữ M đại diện cho phím Alt. Bạn có thể xem danh sách tất cả các lệnh bằng cách nhập Ctrl + g.

**3. Chỉnh sửa tập tin**
Không giống như vi, với nano bạn có thể bắt đầu nhập và chỉnh sửa văn bản ngay sau khi mở tệp.

Để di chuyển con trỏ đến một dòng và số ký tự cụ thể, hãy sử dụng lệnh Ctrl + Shitf + _ Menu ở phía dưới màn hình sẽ thay đổi. Nhập số dòng muốn di chuyển tới và nhấn Enter


**3.1. Tìm kiếm và thay thế**
Để tìm kiếm văn bản, nhấn ``` Ctrl + w ```, nhập cụm từ tìm kiếm và nhấn Enter. Con trỏ sẽ di chuyển đến từ mà bạn tìm kiếm. Để di đến vị trí tiếp theo, nhấn Alt + w.


Nếu bạn muốn tìm kiếm và thay thế, hãy nhấn Ctrl + . Nhập cụm từ tìm kiếm và nhấn Enter.


Sau đó hãy nhập cụm từ cần thay thế và nhấn Enter một lần nữa

Trình chỉnh sửa sẽ tô đậm từ bạn muốn thay thế và hỏi bạn có muốn thay thế nó hay không. Sau khi nhấn Y nếu muốn thay thế nano sẽ tiến hành thay thế từ mà bạn chọn.

**3.2. Lưu file và thoát khỏi Nano**
Để lưu các thay đổi bạn đã thực hiện vào tệp, nhấn Ctrl + o. Nếu tập tin không tồn tại, nó sẽ được tạo khi bạn lưu nó.

Để thoát nano, nhấn Ctrl + x. Nếu có những thay đổi chưa được lưu, bạn sẽ được hỏi liệu bạn có muốn lưu các thay đổi đó không.


Lưu ý: Để lưu tệp, bạn phải có quyền ghi vào tệp. Nếu bạn đang tạo một tệp mới, bạn cần có quyền ghi vào thư mục nơi tệp được tạo.

**4. Syntax Highlighting**

Nano có các quy tắc Syntax Highlighting cho hầu hết các loại tệp phổ biến. Trên hầu hết các hệ thống Linux, các tệp cú pháp được lưu trữ trong thư mục /usr/share/nano và được cấu hình theo mặc định trong tệp cấu hình /etc/nanorc.

Cách dễ nhất để bật đánh dấu cho loại tệp mới là sao chép tệp chứa quy tắc Syntax Highlighting vào thư mục /usr/share/nano.
