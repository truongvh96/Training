#### 1. 
Tmux là một Terminal Multiplexer - "bộ ghép kênh". Nó cho phép bạn chuyển qua lại giữa các chương trình độc lập ngay trên một terminal, tách các chương trình ra một terminal riêng mà vẫn giữ được trạng thái hoạt động của chúng. Và còn làm được nhiều tiện ích khác.

#### 2. Cài đặt tmux
Ubuntu:
```
# apt-get update 
# apt-get install tmux
```
RHEL/Centos:
```
# yum update 
# yum install tmux
```
#### 3. Sử dụng tmux
Tạo session tmux mới :
```
# tmux new
```
List các session:
```
# tmux ls
```
Tách session:
CTRL +B --> gõ D --> Enter
Quay lại session session:
tmux attach -t [session_name]
VD: tmux attach -t 0 vì chưa đặt tên cho session nên session sẽ đánh số thứ tự từ 0

Tạo session với tên :
```
# tmux new -s name
```
Xóa session :
```
# tmux kill-session -t name
```
Để sử dụng như Terminal mặc định của hệ thống Nhấn giữ shift

#### 4. Các command làm việc trong terminal của Tmux

- Ctrl+b c Tạo một cửa sổ mới
- Ctrl+b w Xem danh sách cửa sổ hiện tại
- Ctrl+b n/p Chuyển đến cửa sổ tiếp theo hoặc trước đó
- Ctrl+b f 　Tìm kiếm cửa sổ
- Ctrl+b , 　Đặt/Đổi tên cho cửa sổ
- Ctrl+b & 　Đóng cửa sổ

#### 5. Cách lệnh làm việc với các panel trong 1 cửa sổ

- Ctrl+b % chia đôi màn hình theo chiều dọc
- Ctrl+b " chia đôi màn hình theo chiều ngang
- Ctrl+b o/<phím mũi tên> Di chuyển giữa các panel
- Ctrl+b q Hiện số thứ tự trên
- Ctrl+b x Xoá panel
