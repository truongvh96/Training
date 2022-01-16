Environment Variable Linux
1. Biến (variable) là gì?
Trong khoa học máy tính, biến là nơi lưu giữa các giá trị như filename, text, number hoặc bất kỳ loại dữ liệu nào khác. Các giá trị trong biến có thể được hiển thị, xóa, sửa, và lưu lại.

2. Biến môi trường (Environment Variable) là gì ?
Biến môi trường là các giá trị động có ảnh hưởng đến tiến trình hoặc chương trình chạy trên máy. VD: $LANG là biến lưu trữ giá trị của ngôn ngữ trên hệ thống.

3. Một số biến môi trường phổ biến:
Truy cập biến môi trường: ``` # echo $VARIABLE ```

Một số biến môi trường cần thiết trên Linux

PATH : Biến này chỉ ra danh sách cảc thư mục thực thi file trên hệ thống được phần tách với nhau bằng dấu ":". Khi nhập một command từ bàn phím biến $PATH sẽ tìm và thực thi nếu nó tồn tại trên hệ thống.
VD:
```
# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```
Biến USER: Thông tin username
VD:
```
truong@dell:~$ echo $USER
truong
truongp@dell:~$ 
Biến UID: User ID VD:
truong@dell:~$ echo $UID
1000
Biến TERM: Default terminal emulator VD:
truong@dell:~$ echo $TERM
xterm-256color
SHELL: shell hiện tại user đang sử dụng
```
VD:
```
truong@dell:~$ echo $SHELL
/bin/bash
```
Hiển thị toàn bộ biến môi trường có trên hệ thống
```
# env
or
# printenv | less
```
4. Tạo mới một biến môi trường
```
VARIABLE_NAME= variable_value 
```
VD: truong=1996

5. Xóa biến
```
unset VARIABLE
VD: unset namdp
```
6. Set giá trị cho biến
``` export Variable=value ```

7. Biến Local & Global trong Shell Script
Global: Là biên toàn cục có giá trị trong toàn bộ shell script.
Local: Biến cục bộ này chỉ có giá trị trong hàm khởi tạo.
