1. find
Tìm file theo tên trong thư mujc:
```
find /home -name filename.txt
```
Tìm folder bằng tên :
```
find /home -type d -name testdir
```
Tìm file theo quyền các file có quyền 777 :
```
find . -type f ! -perm 777
```
Tìm file và folder theo size:
```
find /home -size 10M : tìm các file 10MB
find /home -size +10M -size -50M Tìm các file > 10MB và < 50MB
```
Các option đi theo:

-exec CMD: Tệp đang được tìm kiếm đáp ứng các tiêu chí trên và trả về 0 làm trạng thái thoát để thực hiện lệnh thành công.
-ok CMD: Nó hoạt động giống như -exec ngoại trừ người dùng được nhắc trước.
-inum N: Tìm kiếm các tệp có số inode 'N'.
-liks N: Tìm kiếm các tệp có liên kết 'N'.
-name demo: Tìm kiếm các tệp được chỉ định bởi 'demo'.
-newer file: Tìm kiếm các tệp đã được sửa đổi / tạo sau 'tệp'.
-perm bát phân: Tìm kiếm tệp nếu quyền là 'bát phân'.
-print: Hiển thị tên đường dẫn của các tệp được tìm thấy bằng cách sử dụng phần còn lại của tiêu chí.
-empty: Tìm kiếm các tệp và thư mục trống.
-size + N / -N: Tìm kiếm các tập tin của khối 'N'; 'N' theo sau là 'c' có thể được sử dụng để đo kích thước bằng ký tự; '+ N' có nghĩa là kích thước> 'N' khối và '-N' có nghĩa là kích thước <'N' khối.
-name user: Tìm kiếm các tệp thuộc sở hữu của tên người dùng hoặc 'name ID.
\ (expr ): Đúng nếu 'expr' là true; được sử dụng để nhóm các tiêu chí kết hợp với OR hoặc AND.
! expr: Đúng nếu 'expr' là sai.
2. locate
TÌm kiếm lâu sơn so với find vì tìm ở tất cả các tệp

Cú pháp : ``` locate [option] p ```

locatet text.txt : Tìm kiếm file text.txt trên hệ thống

3. where
Hiển thị tệp bin của command đang chạy cùng với source code & man file

Cú phap : ``` # whereis [option] [command] ```

4. which
Lệnh này sử dụng tương tự where nhưng sẽ trả về binary loation

5. type
Tương tự lệnh which và sẽ tìm thêm phần alias.
