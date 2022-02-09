Lệnh kill trong Linux Để kiểm tra PID các process và show các process có thể sử dụng lệnh ps với option và có thể kết hợp pipeline dữ liệu để tìm kiếm nhanh hơn

VD:
```
# ps aux | grep java
```
#### 1. Kill process với PID

Cú pháp :
```
kill SIGNAL PID
```
```
VD:
kill 63772
```
Ở đây 63772 là pid của process bạn muốn hủy. Vì không có signal nào gán cho nó nên đây sẽ là SIGTERM signal. Đôi khi nó sẽ không dùng được mà bạn phải “buộc” tắt tiến trình.

Trong nhiều trường hợp, lệnh kill command sẽ như sau:
```
kill [Signal_or_Option] pid
```
Bên dưới là ví dụ một lệnh để buộc kill process:
```
kill SIGKILL 63772
```
Tương tự, để kill process bằng cách ngắn gọn hơn thì ta dùng:
```
kill -9 63772
```
Thay số 63772 bằng pid tương ứng của process.
#### 2. Tài liệu tham khảo thêm
https://www.hostinger.vn/huong-dan/cach-kill-proccess-linux
